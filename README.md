# 💳 Data Bank Case Study

<img width="247" height="310" alt="image" src="https://github.com/user-attachments/assets/e700b4b7-313c-4d69-b7e1-f75572909079" />


## 📌 Introduction
There is a new innovation in the financial industry called **Neo-Banks**: digital-only banks without physical branches.  
Danny envisioned an intersection between these new age banks, cryptocurrency, and the data world — leading to the launch of **Data Bank!**  

Data Bank runs like any other digital bank but with a twist:  
- It also provides the **world’s most secure distributed data storage platform**.  
- Customers are allocated **cloud storage limits** linked to their account balances.  
- To reduce security risks, customers are **frequently reallocated across nodes**.  

The **management team** wants to:  
- Increase the total customer base  
- Better track customer storage requirements  
- Forecast growth and plan for future development  

This case study focuses on **calculating metrics, analyzing data, and producing insights** for Data Bank.  

---

## 📂 Files in the Repository
- `regions.csv` – Region IDs and names  
- `customer_nodes.csv` – Records of customers allocated to nodes  
- `customer_transactions.csv` – Transactional data (deposits, withdrawals, purchases)  
- `SQL_Case_Study_Solution.md` – SQL queries written to solve the case study questions  
- `README.md` – Project documentation  

---

## 🎯 Expectations
- Understand customer allocation across regions and nodes  
- Track how frequently customers are reallocated  
- Analyze transaction behaviors (deposits, withdrawals, purchases)  
- Calculate balances, growth, and customer activity  
- Provide insights to help management plan **future scaling**  

---

## ✅ What We Did
- **Explored node and region distributions** using SQL  
- **Analyzed customer reallocations** with averages and percentiles  
- **Studied transaction data**: totals, averages, and customer behaviors  
- **Calculated closing balances** for each customer by month  
- **Identified growth patterns** (e.g., % of customers with >5% balance increase)  

---

## 🚀 Steps to Run the Project

1. **Analyze Entity Relationship Diagram (ERD)**: Studied the schema to understand relationships between regions, customer nodes, and transactions.  
       <img width="586" height="170" alt="image" src="https://github.com/user-attachments/assets/dc66581f-f4df-4875-86ac-ad7d5b6007af" />

2. **Transport Data into Excel**: Imported raw CSV datasets into Excel for initial inspection, formatting, and validation.  

3. **Load Data into MySQL**: Transferred the cleaned Excel data into MySQL tables for structured storage and query execution.  

4. **Convert Data Types**: Updated data type of date columns to `DATETIME` for accurate time-based analysis.  

5. **Write SQL Queries**: Designed queries to answer the case study questions on customer nodes, allocations, and transactions.  

6. **Perform Analysis**: Interpreted query results to provide business insights on customer growth, balances, and storage requirements.  

   


 


