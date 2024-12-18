## Relational Algebra ##

1. **Select (σ)** - Filters rows based on a specified condition.
   - **Example**: To find all loans over $1200:
     \[
     \sigma_{\text{amount} > 1200} (\text{loan})
     \]

2. **Project (π)** - Selects specific columns and removes duplicates.
   - **Example**: To list only account numbers and balances from the account relation:
     \[
     \pi_{\text{account\_number, balance}} (\text{account})
     \]

3. **Union ( ∪ )** - Combines rows from two relations, eliminating duplicates.
   - **Example**: To find all customers with either an account or a loan:
     \[
     \pi_{\text{customer\_name}} (\text{depositor}) \cup \pi_{\text{customer\_name}} (\text{borrower})
     \]

4. **Set Difference (−)** - Retrieves rows in one relation but not in the other.
   - **Example**: To find customers with loans at the Perryridge branch but without accounts:
     \[
     \pi_{\text{customer\_name}} (\sigma_{\text{branch\_name} = \text{"Perryridge"}} (\text{borrower} \times \text{loan})) - \pi_{\text{customer\_name}} (\text{depositor})
     \]

5. **Cartesian Product (×)** - Combines all rows from two relations.
   - **Example**: Combine customer and account data:
     \[
     \text{customer} \times \text{account}
     \]

6. **Rename (ρ)** - Renames relation attributes.
   - **Example**: Rename columns of a relation:
     \[
     \rho_{\text{new\_name}}(\text{old\_relation})
     \]

Let me know if you need further explanation on any specific operation or example!
