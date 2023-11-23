# Applications
- Passport or B2C Consumer Portal  --> Customer For Apply loan 
- Exchange --> For Lenders to approve loans or Select for further processing
	- Partner portal --> reissue key ke sath  report generate karta hai --> QB and Riki score ki
	- Invitiation --> Invite to the customer 
	- QB

# Reports
- QB --> Qualified Borrower 
- Riki score

# API Cluster from Architecture
- Analytics Orchestration API #Hammerhead-built-service
- Outbound Notification API [INTERNAL]  #Hammerhead-built-service
- Consumer API #FF-built-service
- Transaction Processor API #Hammerhead-built-service
- Access Window Status API #FF-built-service
- RIKI API #FF-built-service
- Report Transformation API #Hammerhead-built-service
- Transaction Persistence API #FF-built-service
- Customer API [PRIVATE] #FF-built-service
- Authorization Octa

# Services
- Analytics Service
- Transaction Service
- Transaction Persistence Service
- Notification Service 
- RIKI Service 
- Transformation Service
- Consumer Service 
- Chat Service




# Third Party Application
- Okta
- Aws - Dynamo DB, Event Bus, Transform S3,Secret Manager
- AccountChek
- Other 3P Aggregator
- MX
- Redis
- Open AI

# Event Flow - Partner Portal

![[PartnerPortal1.png]]![[PartnerPortal2.png]]
## Flow Description
1. Login using OKTA -->Partner Web Portal UI & OKTA​
2. Access Token​ + JWT with Claims 
3. Return to Dashboard   
4. (***`Report History`***) List --> RIKI Service (Includes Tagging)​ & DynamoDB
5. Get a New Report
6. Get New Report Request with Reissue Key  
	1. Get New Report Request with Reissue Key
	2. Create Consumer if not exist --> Consumer Service --> POST api/v1/consumer​  ^ab20c2
	3. ​Get Consumer Information​
	4. Get Unprocessed Transaction ID​
	5. Notification for Transaction Processed​
	6. Webhook Call Back​ **// Research on this part**
	7. Post RIKI/Report​
	8. Transform Queue​ **// Research on this part**
	9. Transform ​Consumer  **// Research on this part**
	10. Report Stored to S3​
	11. Report Generated Queue​
	12. Notify to RIKI​
	13. Update the Report Status​
	14. Notify Service to call Webhook/ Websocket ​
	15. Web Notification​
	16. Get Notification List​
7. Click on Notification​
	1. Get Notification List​
8. Click on Report History Grid Row / Download​
	1. Call to Get Report​
	2. Show RIKI Report​

# Event Flow – B2C Consumer Portal ​
![[B2C Consumer Portal 1.png]]![[B2C Consumer Portal 2.png]]![[B2C Consumer Portal 3.png]]
## IMP
- This Contains Mx Platform which is used to gather information of user from banks
## Flow Description
1. SignUp
	1. Sign Up Request​
	2. Access Token
	3. Request to Create Consumer ​​
	4. Consumer Created​
2. Login​
	1. Log in Request​
	2. Access Token​
	3. Consumer lookup with Email ​
	4. Return the Consumer ID
3. Connect your Bank Account ​
	1. (email, id) https://int-api.mx.com/users
	2. Create user​
	3. user_guid, id (Response)​
	4. User Created ​
	5. Request To Get Bank List​
	6. Return Institution List​
4. Clicking Bank Name (***`IF OAUTH IS DISABLED`***) ​
	1. (***`IF OAUTH IS DISABLED`***) Linking with Bank by User Id & Bank Code
	2. Get Bank Credentials​​
	3. Institution Credentials with Authentication Type​
	4. Return Authentication Type​
5. Bank Login Form​
	1. Create a member by user's credentials​
	2. Create Member​
	3. Return member details with connection status​
6. Clicking Bank Name ​(***`IF OAUTH IS ENABLED`***)
	1. (***`IF OAUTH IS ENABLED`***) Linking with Bank by User Id & Bank Code 
	2. Request to get Institution Credentials
	3. Return Institution Credentials
	4. Create a Member
	5. Return oauth window url 
7. Bank Login Form
	1. Request for extended transaction
	2. Polling to check member status  ^123523
8. MFA Screen (IF [[Project Details#^123523|STATUS]] CHALLENGED) 
	1. Request to get MFA Details
	2. Return MFA Details
	3. MFA Request
	4. Return MFA request to caller
	5. Submit MFA details entered by user
	6. MFA Details Submitted
	7. Submit MFA Response
	8. Polling to check member status ^4330ed
9. Pull the Transactions (**IF** [[Project Details#^4330ed|STATUS]] **CONNECTED**) // 
	1. Notify to Pull Transactions
	2. Webhook Call to Pull the Transactions 
	3. Request to Pull Transactions
	4. Pull Transactions
	5. Return Transactions
	6. Get Unprocessed Transaction ID
	7. Notification for Transaction Processed
	8. Webhook Call Back
	9. Post RIKI/Report
	10. Notify Service to call Webhook/ Websocket 
	11. Web Notification
10. RIKI Processed
	1. Get Financial Details
	2. Get Accounts
	3. Get Account Details 
	4. Return Accounts Details
	5. Return Accounts
	6. Lookup for RIKI Id based on Consumer ID
	7. Return RIKI ID
	8. Request to get Riki result set
	9. Return Riki result
	10. Return Financial Data  ^27c762
	11. Get [[Project Details#^27c762|Connected Bank Details]]
11. Loan Request Form
	1. Request Personal Details to get a Loan
	2. Populate Personal Details on Loan Request Form
12. Submit Loan Request Form
	1. Submit Loan Request
	2. Request Submitted 
13. Loan History
	1. Get Loan History
	2. Return Loan History List
14. View Profile
	1. Get Consumer Profile Data
	2. Return Response 
15. Edit Profile
	1. Update Consumer Profile Data
	2. Return Response 
16. Dashboard
	1. Get MX Widget
	2. Return MX Widget
# Event Flow – B2C Chat Flow Phase 1
​![[B2C Chat Flow Phase 1.png]]
![[B2C Chat Flow Phase 2.png]]
## Flow Description
1. Start Chat
2. Chat Screen
	1. User query
	2. Store Message
	3. Select Embedding
	4. Context
	5. Prompt+Context
	6. Generated Response
	7. Refined Response
3. Chat Screen
	1. Start Chat
	2. User Query
	3. Store Message
	4. Select Embedding
	5. Context (***`NOT NULL`***)
	6. RIKI Data
	7. RIKI Json
	8. Prompt + Context
	9. Generated Response
	10. Push Response
	11. Response
 
# Event Flow – B2C Get A Loan Flow Phase 1
 ![[B2C Get A Loan Flow Phase 1.png]]
## Flow Description
 1. Get a Loan Screen
	1. Request Chat JSON
	2. Request data
	3. Form Fields Data
	4. Requested JSON with filtered fields
	5. Submit Form
	6. Success















Questions

1. What is Consumer and Customer API ? 
   Ans. Is Consumer --> Lender And Customer --> Loan Apply karne wale
2. What is work of Transaction Processor API ?
   Ans. Does User bank transaction
3. What is this reissue key and generate from where ?
4. What is this check [[#^ab20c2|Create Consumer if not exist]] ?
5. What is Tagging Agent?
6. What is Counseling PDF Adapter & MISMO Adapter
7. Work of 3P Agregator and Account Check
Aggregator
Tps Service 

--> connect 