# Lending Pool Deployment

The Pool Factory contracts primary responsibility is to deploy and initialize new lending pool contracts

## Deployment

Lending pools are deployed using the `deploy()` function. This will deploy a new lending pool contract using the hash stored on the pool factory, and initialize it with the input parameters.

Additionally, the new lending pool's address will be stored in the pool factory. This allows the pool factory to be queried to check if a pool was deployed by it.
