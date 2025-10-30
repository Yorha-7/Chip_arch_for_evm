# Chip_arch_for_evm

## Block Diagram

![Voting Chip Block Diagram](https://github.com/user-attachments/assets/03da19b1-0717-4373-9d30-b8589b6303f8)

---

## PUFs: 
 i will be introducing the PUFS which will set the bits in a register that will be used to check the coorect and unique hardware. The PUF will follow the setting of fastest path in chip race condition where the challenge will be bit ti switch the path.
 its input will be sorted from the control lines from the communication section.

## Explanation

### **Memory_1**
This is where all the blocks shared via Ethernet are stored.  
From here, a pointer selects the block that needs to be processed and divides it into two parts:
- **drawer_1** – contains the voter’s ID and verification details, which are encrypted using the voter’s private key.  
- **drawer_2** – contains the ID of the party for which the voter has cast the vote.

---

### **Signing_Decrypter**
This is a special unit that takes the private key from the voter to decrypt voter ID and verification details such as name, and checks them against the database provided to the chip.  
Its main purpose is to ensure that:
- The voter casts a vote only once.  
- The voter identity is valid (human and verified).  

However, this design currently poses a major concern — **there’s no point in having a private key if it must be shared with the FPGA**, even if the key is discarded after use. I am currently working on removing this block or finding a better approach, but for now, I’m short on ideas.

---

### **Flag_1**
A simple flag used to signal that the voter’s identity has been confirmed.  
Once set, it indicates that the counter can safely increment the value corresponding to the selected party ID.

---

### **Counter**
A **Gray-code–based binary counter** that is **reprogrammable** during the programming state. It increments the vote count for the party ID pointed to by the input.

The internal FSM of the counter consists of three main states:

1. **Increment State:**  
   Increments the location marked by the input party ID.

2. **Wait State:**  
   Waits for `Flag_1` to be set. During this state, it preserves the previous operation and keeps checking the vote values from the previous and current cycles to ensure no instruction is executed twice within the same clock cycle.

3. **Idle State:**  
   Active during the programming phase, allowing the registers to be updated with new party IDs.

---

### **ROM**
The ROM controls minimal yet essential control lines such as:
- Activating reset state  
- Adding or removing voters from the database  
- Adding or removing parties from the list  

---

### **Hash Generation**
This unit takes the input from **Memory_3**, which stores timestamps and party IDs that have been voted.  
This data is inherently random and unpredictable, making it ideal for generating hashes and producing truly random numbers — useful for creating secure, unpredictable keys.

---

### **Memory_3**
This unit stores **encrypted votes** of voters along with their **timestamps**, to be used for vote re-verification if needed.  
This adds another layer of verification within the chip.  

The **hash algorithm** is currently confidential — similar to the encrypter, it’s a hardware-embedded black box that only the chip can interpret.  
However, the algorithms used to create this hardware can be reverse-engineered, which would allow attackers to build custom encrypters or hash units capable of tampering with ROM data.  

To prevent this, depending on just one verification factor is risky.  
So, an additional independent verification factor is needed — **the question is: what should that be?**

---

## Conclusion

- The current block diagram represents the planned structure of the chip.  
- Major flaws that need to be addressed include:
  - **Private key sharing**
  - **Risk of reverse-engineering the hash/encryption algorithm from HDL code**
- Research and improvements are ongoing to resolve these issues.  
- On the positive side:
  - **Ethernet functionality has been successfully tested and verified.**
  - **All individual modules have been coded and tested successfully.**
- The **next step** is to upload the estimated **silicon cost** for each module to determine the total area occupancy of the full chip.

---
