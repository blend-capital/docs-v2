# Pool Management

The Lending Pool contract handles updating pool status and asset parameters.

## Pool Status

Pool status is updated using either a permissionless function, `update_status()` , or a permissioned (admin-only) function `set_status()`. These set the pool to one of the following statuses:

* `Admin_Active` (enum 0): The pool is functioning normally.
* `Active` (enum 1): The pool is functioning normally.
* `Admin_On_Ice` (enum 2): Borrowing and auction cancellations are disabled.
* `On_Ice` (enum 3): Borrowing and auction cancellations are disabled.
* `Admin_Frozen` (enum 4): Borrowing, depositing, and auction cancellations are disabled.
* `Frozen` (enum 5): Borrowing, depositing, and auction cancellations are disabled.

### Permissionless Updates

Anyone can update pool status permissionlessly by calling `update_status()`. This function checks the backstop state and sets the status based on the current percentage of backstop deposits allocated to the pool that are queued-for-withdrawal(q4w).

The status is set as follows:

```mermaid
flowchart LR
    A[update_status] --> B{status==6}
    B --------->|Yes| C[[PANIC!]]
    B -->|No| D{status==4}
    D -->|Yes| C
    D -->|No| E{status==2}
    E -->|Yes| F{q4w>=75%}
    F ------>|Yes| G[["SET
    status=5"]]
    E -->|No| H{status==0}
    F -->|No| H
    H -->|Yes| I{"q4w>=50%
    or
    !threshold_met"}
    I -->|Yes| J[["SET
    status=3"]]
    I -->|No| K{q4w>=60%}
    H -->|No| K
    K -->|Yes| G
    K -->|No| L{"q4w>=30%
    or
    !threshold_met"}
    L -->|Yes| J
    L -->|No| M[["SET
    status=1"]]
```

### Permissioned Updates

Pool admins can update pool status by calling `set_status()`. This takes a `status` parameter and sets it after validating it with the following logic:

```mermaid
flowchart TB
    A[set_status] --> B[status_param=0]
    A --> C[status_param=2]
    A --> D[status_param=3]
    A --> E[status_param=4]
    A --> F[status_param=other]
    F --> G[[PANIC!]]
    B --> H{"!threshold_met
    or
    q4w>=50%"}
    H -->|Yes| G
    H -->|No| I[["SET
    status=0"]]
    C --> J{q4w>=75%}
    J -->|Yes| G
    J -->|No| K[["SET
    status=2"]]
    D --> L{q4w>=75%}
    L -->|Yes| G
    L -->|No| M[["SET
    status=3"]]
    E ---> N[["SET
    status=4"]]

```

## Asset Parameters

Asset parameters can only be updated by the pool admin. They're updated in a two-step process that involves a 7-day queue to prevent admins from suddenly adding unsafe assets or parameters. The process is as follows:

1. The admin calls `queue_set_reserve()` with the new parameters. This stores a queued reserve update in a hashmap with the address of the asset being added or updated as the key and a `QueuedReserveInit` struct as the value

```rust
pub struct QueuedReserveInit {
    pub new_config: ReserveConfig,
    pub unlock_time: u64, // block timestamp after which the queued reserve config can be added
}
```

```rust
pub struct ReserveConfig {
    pub index: u32,      // the index of the reserve in the list
    pub decimals: u32,   // the decimals used in both the bToken and underlying contract
    pub c_factor: u32,   // the collateral factor for the reserve scaled expressed in 7 decimals
    pub l_factor: u32,   // the liability factor for the reserve scaled expressed in 7 decimals
    pub util: u32,       // the target utilization rate scaled expressed in 7 decimals
    pub max_util: u32,   // the maximum allowed utilization rate scaled expressed in 7 decimals
    pub r_one: u32,      // the R1 value in the interest rate formula scaled expressed in 7 decimals
    pub r_two: u32,      // the R2 value in the interest rate formula scaled expressed in 7 decimals
    pub r_three: u32,    // the R3 value in the interest rate formula scaled expressed in 7 decimals
    pub reactivity: u32, // the reactivity constant for the reserve scaled expressed in 7 decimals
}
```

{% hint style="info" %}
The queued reserve modification (or addition) can be canceled at any point by the admin using `cancel_set_reserve()`
{% endhint %}

2. After 7 days have passed the admin calls `set_reserve()` which sets the asset parameters to the queued metadata.
