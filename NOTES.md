# About

>About the project in my own words
! Bug with refund + selectWinner -> `refund()` creates blank spots in the array and `selectWinner()` doesn't check whether the pseudo-random index position chosen is blank or not and directly transfers the `prizePool`, even if `players[winnerIndex] == address(0)`.  
! Write the PoC for the overflow bug