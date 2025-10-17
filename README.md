# Chip_arch_for_evm

## Block Diagram:

<img width="3647" height="2084" alt="voting chip" src="https://github.com/user-attachments/assets/03da19b1-0717-4373-9d30-b8589b6303f8" />

## EXplanation:

__Memory_1__: This is where all the blocks shared by ethernet will be stored, from here the pointer will point to the block that needs to be processed and divide it into two, generally a drawer_1 and drawer_2. drawer_1 contains the id and verfication details of voter which are encrypted by his private key, and drawer_2 will contain the  id of the party for which the voter has given his his vote.

__Signing_decrypter__: This is a special kind of unit that takes the private key form the voter to decrypt the voter id and verification details such as name to check it with the database provided to the chip, to make sure voter is casting vote only once and the voter identity is human. This portion is not what i want, i am currently workiing on removong it but rn i am out of ideas, i mean what is even the point of having a private key if it needs to be shared with the fpga even though the key is being dropped after use.

__Flag_1__: Its is just the flag that is used to set the counter a signal that the voter identity is confirmend and you are ready to increment the value to which the party id points.

__Counter__: A gray code based binary counter that is reprogrambale at the time of progamming state, increments the votes by one to which the voter id is pointed to, this portion will have three mainly states in its state machine. this are:
- _Increment State_: this will increment the location which is marked by the party id which is givne in the input.
- _Wait state_: this will wait for the falg_1 to get set till then preseve the operation and keep checking teh values of votes form previous cycle to next cycle to make sure no instruction is followed in that one clk cycle.
- _Idle state_: this is the state at the time of programming allowing the registers to be programmed with new party ids.

__ROM__: form this memory minimal number of control lines will be controlled like activating the reset state, adding the voters and removing the voters from the data base, adding and removing party list.

__Hash Generation__: This is the unit where it takes input as state of memory_3, which stores the timestamps and paty id that are voted, this data is all random and not predictable perfect for a hash to generate truly random numbers to make a very unpredivtable key.

__Memory_3__: This unit will store the encrypted votes of the voters with a time stamp, to be used for the reverification of votes if needed. This will add +1 state of verfication in the chip. the hash algorithm will be secreat for now, till i need to replace this one too, hash is similar to the encrypter it is just a blach box integrated in hardware that only chip could crack in, but the codes that are used to make that hardware can be reverse engineered and hackers can make there own encrypter and hash to do the reverse and tamper the rom data with an ease, to ensure this will not happen depending on just one factor to verify will be stupid, so i need one more factor, question is what?

## Conclusion:

- The block diagrm is the structure that is planned and needs to be improved to tackel the major flaws like :
  - Privtae key sharing.
  - Posiblility of hash and encrypter algorithm reverse engineered from the HDL codes.
- Currently research is going on.
- on positive side, ethernet functionality is tested and verfied to be working.
- and the separate modles of each blocks are good and code tested. soon i will uploaad the cost of silicon for each module to estimate the area occupancy of the full chip. 
