---
name: Deployment
route: /deployment
---

# Deploying the contracts

Deploying our whole suite of contracts can get a confusing as they all rely on each other and thus have to be deployed in certain order and after certain calls.

# Full Deploy
* Run the live deploy migration script.
* This should create a new file in migration logs which will have further instructions.

* After this you will have FST tokens to create a voting proposal.
* This can allow you to create a voting proposal and vote on it to create an exchange from our factory
* Or you can use the deploying key to call `createExchange()` passing in the proper parameters. See Factory.md for more details
