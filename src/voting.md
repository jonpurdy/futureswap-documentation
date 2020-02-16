---
name: Voting
route: /voting
---
# Voting
* Voting is handled by FST and is applied by a modifierin the designated contract. 
```
modifier onlyVoter() {
        if (msg.sender != owner()) {
            require(msg.sender == votingProxy.currentVotingContract(), "only a vote can update this");
        }
        _;
    }
```
* The contract looks to the votingProxy.sol to see the current voter address and only allows calls from there

## createProposal

### parameters 
* address _to -> destination to act on
* bytes memory _data -> bytes to forward to the _to address
* string memory _action -> the bytes in readable form this is for verification of the call

### actions
* require the to address is not the voting proxy, there is a different function for that 
* get the voting end time which is 1 week from now
* get the fs token snapshot
* increase the proposal count 
* create proposal
    * proposalCount -> proposal ID 
    * voting time ends
    * the data to forward 
    * the address to forward it to 
    * ether if it was send 
    * resolved -> false
    * yes votes
    * no votes 
    * is it a vote for upgrading this contract
    * paused At time
    * the new implementation of the voting contract if upgrading 
    * the action for anyone to verify the call they are making

### modifiers
 * Prosal paused -> throws if proposal is paused
 * requires Payment -> costs 100 FST 


## createVotingUpgradeProposal()
* similat to createProposal but the address it takes in is the new voting contract. The call gets forwarded to the votingProxy.

## vote()

### parameters 
* _proposalID -> the vote to act on
* _yesVote -> true for yes false for no

### action
* require the vote is open (1 week period)
* check to see if they already voted 
* get the balance of the voter at the time of the snapshot (vote was created)
* if yes vote add their total balance to yes side and set vote direction to true (they voted yes)
* if no vote increment the no vote side (keep vote direction false for no)

## resolve()

### parameters 
* _propsalID -> the ID of the vote

### actions 
* requires that it was a regular proposal and not upgrade proposal
* set resolve to true 
* if yes votes > no votes forward call 
    * handle a transaction fail by revert
    * If the vote was created poorley and will always fail it will hand in not resolve state however will never be able to be called 
    * however this allows protection from frontrunning. If the contract being called on can be put into a state that can fail then someone could frontrun the call and cause the vote to fail. This way it can still be resolved when the attack is over

### modifiers
* meets thershold total votes -> 10% of FST supply. 
*  properResolution -> voting period is over, the vote has not been resolved, and the proposl exists 

## resolveUpgradeVotingContract()
* works similar to resolve. Although it is a two step process
* step 1 is there will be a yes vote so pause new proposals for 1 week to allow all current proposals to be finished. 
* forwards the call on the second call after a week (if it hasn't been a week reverts call). It also gives up its ownership of the token proxy so the next contract can upgrade the FSToken
* If forwarded call fails it resolves the vote and unpauses the contract (can't revert here because that could pause the contract forever)
* If vote is no, vote is resolved

## didAddressVote()
* takes in proposal and target address and shows if it has voted on specific proposal 

## directionOfVote()
* takes in proposal and target address then returns how they voted. True is yes false and the address voted is no