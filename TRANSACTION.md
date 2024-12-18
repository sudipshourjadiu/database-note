## PL/SQL Transactions**

### **What is a Transaction?**
- A **transaction** is a series of one or more logically related SQL statements or operations performed on Oracle table data.  
- It allows programming code to be broken into manageable units.  
- A successfully executed SQL statement is **not the same as a committed transaction**:  
  - Without a **COMMIT**, even successfully executed SQL statements can be **rolled back**, undoing all changes.

---

### **Transaction Boundaries**
#### **When a Transaction Begins:**
- When the **first SQL statement is executed** after connecting to the database.  
- At the start of each new SQL statement issued after the completion of the previous transaction.  

#### **When a Transaction Ends:**
- On issuing a **COMMIT** (makes changes permanent) or **ROLLBACK** (undoes changes).  
- Automatically on issuing a **DDL statement** like `CREATE TABLE`.  
- When the **user exits SQL*Plus**.  
- If the **system crashes**.

---

### **Committing a Transaction**
- A **COMMIT** command makes a transaction permanent.  
#### Syntax:  
```sql
COMMIT;
```
#### Effects of COMMIT:  
1. All work done by the transaction becomes **permanent**.  
2. Other users can see the changes made.  
3. All locks acquired by the transaction are released.  

---

### **Rolling Back a Transaction**
- **Rollback**: Reverts the database to its previous consistent state by undoing changes from a failed or incomplete transaction.  
- **Example**:  
  - If a bank transfer deducts $100 from Account A but fails to credit Account B, a rollback will restore Account A’s original balance, ensuring consistency.  

#### Syntax:  
```sql
ROLLBACK;  
ROLLBACK TO SAVEPOINT <savepoint_name>;
```
#### Effects of ROLLBACK:  
1. Undoes all work done by the transaction.  
2. Releases any locks acquired.  

---

### **Advantages of COMMIT and ROLLBACK**
1. Ensure **data consistency**.  
2. Preview changes before making them permanent.  
3. Group logically related operations.  

---

### **Transaction Control: Savepoints**
- **Savepoints** act as checkpoints within a transaction.  
- Allows partial rollback to a specific point instead of undoing the entire transaction.  

#### Syntax:  
```sql
SAVEPOINT <savepoint_name>;
```

#### Example:  
```sql
ROLLBACK TO savepoint_name;
```

---

### **Deadlocks**
- **Deadlock**: Occurs when two or more transactions wait indefinitely for each other to release locks.  

#### Example:  
- **Transaction 1** locks Account A, then waits for Account B.  
- **Transaction 2** locks Account B, then waits for Account A.  
- Both transactions are stuck.  

---

### **Solutions to Deadlocks**
1. **Resource Ordering**: Enforce a consistent order for acquiring locks.  
2. **Wait-Die/Wound-Wait Protocols**:  
   - Older transactions wait; younger ones roll back (Wait-Die).  
   - Older transactions force younger ones to roll back (Wound-Wait).  
3. **Timeouts**: Transactions exceeding the time limit are rolled back.  
4. **Two-Phase Locking (2PL)**:  
   - **Growing phase**: Acquire locks.  
   - **Shrinking phase**: Release locks.  
5. **Finer Lock Granularity**: Lock smaller data segments to reduce conflicts.  

---

### **Deadlock Detection and Recovery**
#### **Detection**:  
- Use a **wait-for graph** to identify cycles.  
  - **No cycle** = No deadlock.  
  - **Cycle present** = Deadlock exists.  

#### **Recovery**:  
- Roll back one or more transactions to break the deadlock.  
- Choose the transaction that incurs the **minimum cost**.  
- Avoid **starvation** by considering rollback frequency in the cost factor.  
