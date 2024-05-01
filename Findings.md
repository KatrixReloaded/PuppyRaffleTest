### [M-1] Denial of Service attack possible while looping through players array to check for duplicates in `PuppyRaffle::enterRaffle`, incrementing gas costs for future entrants  
  
**Description:** The `PuppyRaffle::enterRaffle` function loops the `players` array to check for duplicates. However, the longer the `PuppyRaffle::players` array is, the more checks a new player will have to make. This means the gas costs for players who enter right when the raffle starts will be dramatically lower than those who enter later. Every additional address in the `players` array, is an additional check the loop will have to make.  
  
    
**Impact:** The gas cost for raffle entrants will greatly increase as more players enter the raffle. Discouraging later users from entering, and causing a rush at the start of a raffle to be one of the first entrants in the queue.  
  
An attacker might make the `PuppyRaffle::entrants` array so big, that no one else enters, guaranteeing themselves the win.  
  
**Proof of Concept:**  
  
If we have 2 sets of 100 players enter, the gas costs will be as such:  
- 1st 100 players: ~6252128 gas  
- 2nd 100 players: ~18068218 gas  
  
This is more than 3 times more expensive for the second 100 players.  
  
<details>
<summary>PoC</summary>
Place the following test into `PuppyRaffle.t.sol`.  

```javascript
    function test_DoS() public {
        vm.txGasPrice(1);

        //let's enter 100 players
        uint256 playersNum = 100;
        address[] memory players = new address[](playersNum);
        for(uint256 i = 0; i<playersNum; i++) {
            players[i] = address(i);
        }
        // see how much gas it costs
        uint256 gasStart = gasleft();
        puppyRaffle.enterRaffle{value: entranceFee * players.length}(players);
        uint256 gasEnd = gasleft();
        uint256 gasUsedFirst = (gasStart - gasEnd) * tx.gasprice;
        console.log("Gas used by first hundred players: ", gasUsedFirst);

        //now for 2nd 100 players
        address[] memory playersTwo = new address[](playersNum);
        for(uint256 i = 0; i<playersNum; i++) {
            playersTwo[i] = address(i+playersNum);
        }
        // see how much gas it costs
        uint256 gasStartSecond = gasleft();
        puppyRaffle.enterRaffle{value: entranceFee * playersTwo.length}(playersTwo);
        uint256 gasEndSecond = gasleft();
        uint256 gasUsedSecond = (gasStartSecond - gasEndSecond) * tx.gasprice;
        console.log("Gas used by second hundred players: ", gasUsedSecond);

        assert(gasUsedFirst < gasUsedSecond);
    }
```
</details>
  
**Recommended Mitigation:** There are a few recommendations.  

1. Consider allowing duplicates. Users can make new wallet addresses anyway, so a duplicate check doesn't prevent the same person from entering multiple times, only the same wallet address.  
2. Consider using a mapping to check for duplicates. This would allow constant time lookup of whether a user has already entered.  
  
  
  
### [M-#] 