---
description: Possible statuses and their meaning
---

# Transaction statuses

### **Deposit**:

**confirmed - final. Transaction is approved.  
not\_confirmed - transaction pending.  
not\_enough\_fee - transaction needs attention from client's team.  
cancelled - final. Transaction will not get approved.**

transitions:

#### **null -&gt; confirmed** - **final status**

In case of **not\_confirmed** and **not\_enough\_fee** status can transition into following statuses:

#### **confirmed** - if transaction is approved. **cancelled** - final status if transaction was removed/not approved.

\_\_\_\_\_\_\_\_\_\_  
**not\_enough\_fee** - can appear only for USDT or ERC 20 tokens.  
USDT is "fueled" by **OmniBTC** balance.  
Ethereum ERC20 tokens are "fueled" by **ETH** balance.  
If Fuel balance is below required - transactions will appear in "**not\_enough\_fee"** status.  




