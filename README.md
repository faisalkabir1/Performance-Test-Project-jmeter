# Performance-Test-Project-jmeter
## Overview
This repository contains JMeter test plans and reports for performance testing of the **Restful Booker API** and functional testing of the **Dmoney API**.

## Prerequisites
- **Apache JMeter** installed (latest version recommended)
- **Java 8 or later** installed
- **Git** installed
- **Internet connection** (for running API requests)

## Folder Structure
```
.
├── booking.jmx                  # JMeter test plan for Restful Booker API
├── dmoney.jmx                   # JMeter test plan for Dmoney API
├── Resources/
│   ├── deposit.csv              # CSV file for agent deposits
│   ├── sendMoney.csv            # CSV file for customer money transfers
│   ├── payment.csv              # CSV file for customer payments
├── booking-api-test-report.xlsx  # Load & stress test reports in Excel format
├── Report/                      # HTML test reports (ignored in Git)
├── .gitignore                    # Excludes dmoney.jtl and Report folder
├── README.md                     # This file
```

## Instructions to Run Tests
### Task 1: Restful Booker API Performance Testing
1. Open **JMeter**.
2. Load the **booking.jmx** file.
3. Configure test execution:
   - Set thread count based on your desired user load.
   - Ensure **Gaussian Random Timer** (Deviation: 2000ms, Constant Delay: 500ms) is used.
4. Run the test and observe the results.
5. Generate reports:
   - Load Test (5min, 10min, 20min load steps)
   - Stress Test (gradually increasing load to find bottleneck)
   - Generate an **HTML Report** and save it.
   - Run the following command in your terminal to execute the JMeter test in **non-GUI mode**: ``` jmeter -n -t .\booking.jmx -l .\booking.jtl -e -o Reports ```
#### 📌 Command Breakdown:

- `-n` → Non-GUI mode (for faster execution)  
- `-t .\booking.jmx` → Load the test script (`booking.jmx`)  
- `-l .\booking.jtl` → Store results in `booking.jtl`  
- `-e` → Enable HTML report generation  
- `-o Reports` → Save reports to the `Reports` folder  

### Task 2: Dmoney API Functional Testing
1. Open **JMeter**.
2. Load the **dmoney.jmx** file.
3. Ensure CSV files (`deposit.csv`, `sendMoney.csv`, `payment.csv`) are correctly linked.
4. Run the test to validate transactions.
5. Verify successful assertions in JMeter results.
6. Generate an **HTML report**. Run the following command in your terminal to execute the JMeter test in **non-GUI mode**: ``` jmeter -n -t .\dmoney.jmx -l .\dmoney.jtl -e -o Reports ```

## Reports & Screenshots
### Task 1: Restful Booker API
**Scenario:**
120,000 users over a 12-hour period log in, create a booking, and search for the
booking. 
#### Task Overview:  
This test plan (`booking.jmx`) automates API testing for the **RESTful Booker API**, covering:  
✔ Authentication  
✔ Booking creation with **randomized data**  
✔ Booking search and validation  

#### Test Steps & Configuration:  

1️⃣ **Set Headers** (Using HTTP Header Manager)  
   - `Accept: */*`  

2️⃣ **Login & Authentication**  
   - **Endpoint:** `https://restful-booker.herokuapp.com/auth`  
   - **Method:** `POST`  
   - **Request Body:**  
     ``` json:
         {
            "username": "admin",
            "password": "password123"
      
         }
3️⃣ **Create Booking**

   - **Endpoint:**  
   `https://restful-booker.herokuapp.com/booking`
   
   - **Method:**  
   `POST`
   
   **Request Body (Dynamic Data):**  
   ```json
{
   "firstname": "Generate Random FirstName",
   "lastname": "Generate Random LastName",
   "totalprice": "Generate random amount",
   "depositpaid": true,
   "bookingdates": {
      "checkin": "2024-01-01",
      "checkout": "2024-01-02"
   }
}
```

**Extract Booking ID:**  
Use JSON Extractor to capture `bookingid` for validation.

4️⃣ Search Booking

  - **Endpoint:**  
   `https://restful-booker.herokuapp.com/booking/<booking_id>`
   
 - **Method:**  
   `GET`

**Validation:**  
Ensure the response contains the correct booking details.

#### Load Test & Stress Test Results
- **Request Summary**
  - 1st step: 5 min load with 831 Users ![jmeter 1-1](https://github.com/user-attachments/assets/463ffd95-26d6-440d-8733-83e3c5a9e2fe)
  - 2nd step: 10 min load with 1662 Users ![jmeter1-2](https://github.com/user-attachments/assets/3283c305-6bcb-42be-a302-350807218bb0)
  - 3rd step: 20 min load with 3324 Users **Statistics from HTML Report** ![image](https://github.com/user-attachments/assets/0a69e0f0-5157-48fa-a250-ad0e8c772601)
  
- **Stress Test Statistics from HTML Report**
  Identified the bottleneck throughput by conducting a stress test.
  ![Dmoney_stress-test](https://github.com/user-attachments/assets/38ca8e62-ba85-45e4-ba64-d7046eb72e12)

#### Load Test & Stress Test Reports (Excel)
  [Excel Reports](https://docs.google.com/spreadsheets/d/1onegMbcLFkq4Mt_uRWEi0odjANg9zXRe/edit?usp=drive_link&ouid=110591976413796555813&rtpof=true&sd=true)
 
  <img width="631" alt="image" src="https://github.com/user-attachments/assets/ae7afd58-f651-4f7e-89c4-4066446ff339" />
  <img width="707" alt="image" src="https://github.com/user-attachments/assets/148c53f7-ef01-49e3-a222-5c799a028c3a" />

### Task 2: Dmoney API
#### Scenario Overview:
This test simulates various financial transactions in the **Dmoney API** using JMeter to evaluate performance under concurrent load.

#### API Documentation
- User [(http://dmoney.roadtocareer.net/api-docs/user/)]
- Transactions [http://dmoney.roadtocareer.net/api-docs/transaction/]
#### Test Scenario:
- **5 agents** perform **deposits** for **10 customers**.  
- **5 customers** send money to **another 10 customers**.  
- **5 customers** make **payments** to **2 merchants**.  

#### Implementation Steps:
1. **Authentication**  
   - Log in as an **admin** and generate a token.  
   - Use this token across all threads for secure transactions.  

2. **Test Configuration**  
   - Create **3 Thread Groups**:
     - **Agent Transactions (Deposit)**  
     - **Customer Transactions (Send Money)**  
     - **Merchant Transactions (Payment)**  
   - Use **CSV Data Set Config** for dynamic user data:
     - `deposit.csv` → Agent & Customer account details  
     - `sendMoney.csv` → Sender & Receiver customer details  
     - `payment.csv` → Customer & Merchant details  

3. **Dynamic Transaction Amount**  
   - Implement **Random Variable Controller** to assign random small amounts to prevent zero balance issues.  

4. **Performance Settings**  
   - Each thread has a **ramp-up time of 120 seconds** to simulate real-world usage.  

5. **Assertions for Validation**  
   - Ensure that **all transactions are successful** using assertions.  

#### Functional Test Results
- **Request Summary**
  ![image](https://github.com/user-attachments/assets/95b00971-9dad-4906-8465-0b599633ed8f)

- **Statistics from HTML Report**
![dmoney-reports](https://github.com/user-attachments/assets/a9f5f978-c974-447b-bc60-8a043d751a1a)


## Notes
- The `dmoney.jtl` file and the `Report/` folder are **excluded from Git** to keep the repository clean.
- The **Excel file contains the detailed steps** for both Load & Stress tests of the **Restful Booker API**.

## Conclusion
This project ensures:
- Performance validation of the **Restful Booker API** under realistic load.
- Successful functional validation of the **Dmoney API**.

For any issues or improvements, feel free to open an **Issue** or **Pull Request**.
