# About

>About the project in my own words
! Bug with refund + selectWinner -> `refund()` creates blank spots in the array and `selectWinner()` doesn't check whether the pseudo-random index position chosen is blank or not and directly transfers the `prizePool`, even if `players[winnerIndex] == address(0)`.  
! Write the PoC for the overflow bug  
  
# Notes  
floating pragma - `^` not recommended, pick a particular version  
variable names - use `s_` as a prefix for naming storage variables, `m_` for memory  
Magic numbers - use of actual numbers without any descriptors are bad, 0 and 1 are exceptions  
Cached array length - `uint256 arrLength = arr.length;` instead of directly using `arr.length` in loops as it is calling from storage every time. [@audit-gas]  
  
slither:
  - `//slither-disable-next-line <DETECTOR-NAME>` in code above the LoC [Disables the detector for the next LoC]  
  - `--exclude-dependencies` in cmd to ignore lib folder dependencies  
