# Bandit Security Analysis Walkthrough

## Introduction
This repository provides a comprehensive walkthrough of using Bandit, a security linting tool for Python, to identify potential security vulnerabilities in a Python application and implement targeted exceptions. This study was conducted as part of a security engineering research project to evaluate Bandit's effectiveness in an enterprise setting.

Bandit is designed to detect common security issues in Python code, making it a valuable tool for identifying vulnerabilities during development before they reach production. This research aims to assess Bandit's capabilities and demonstrate its practical applications in secure software development.

## Project Objectives
- Evaluate Bandit's ability to detect security vulnerabilities in Python code.
- Demonstrate the implementation of targeted exceptions for specific vulnerabilities.
- Document the security analysis process for future reference and implementation.

## Environment Setup

### Prerequisites
- Python 3.x
- pip (Python package manager)
- Virtual environment tool (venv, virtualenv, etc.)

### Initial Setup Steps
1. Downloaded the example HR application from a GitHub repository.
2. Extracted the ZIP file to a local directory.
3. Created a new Python virtual environment:
   ```bash
   python -m venv venv
   ```
4. Activated the virtual environment:
   - **Windows:** `venv\Scripts\activate`
   - **macOS/Linux:** `source venv/bin/activate`
5. Installed required dependencies from `requirements.txt`:
   ```bash
   pip install -r requirements.txt
   ```
6. Installed Bandit version 1.7.8:
   ```bash
   pip install bandit==1.7.8
   ```

## Vulnerability Detection

### Running Bandit on the Application
Executed Bandit against the `app.py` file to identify potential security vulnerabilities:
```bash
bandit --format txt app.py
```

### Vulnerability Findings
Bandit identified a security vulnerability in the code:
```
>> Issue: [B608:hardcoded_sql_expressions] Possible SQL injection vector through string-based query construction.
   Severity: Medium    Confidence: Low
   CWE: CWE-89 (https://cwe.mitre.org/data/definitions/89.html)
   Location: Line 53
```

#### Identified Vulnerable Code:
```python
statements_str += f"select * from employees where employee_id = {emp_id};"
```

This code is vulnerable to **SQL injection** because it directly incorporates user input into an SQL query without proper parameterization. An attacker could exploit this vulnerability to manipulate queries and gain unauthorized access to data.

## Implementing Targeted Exceptions

In cases where vulnerabilities need to remain in the code due to specific requirements or constraints, Bandit allows the use of targeted exceptions to disable specific security checks.

### Adding a Security Exception
To demonstrate this feature, a targeted exception was added for the identified SQL injection vulnerability:

1. Located the vulnerable line of code (line 53).
2. Appended a `# nosec B608` comment at the end of the line:
   ```python
   statements_str += f"select * from employees where employee_id = {emp_id};"  # nosec B608
   ```
   This tells Bandit to **skip checking** for the `B608` vulnerability on this specific line.

### Verifying the Exception
After adding the exception, Bandit was re-run to confirm that the vulnerability was correctly skipped:
```bash
bandit --format txt app.py
```

#### Output Confirmation:
```
[tester]     WARNING nosec encountered (B608), but no failed test on line 53
Test results:
        No issues identified.

Code scanned:
        Total lines of code: 68
        Total lines skipped (#nosec): 0
        Total potential issues skipped due to specifically being disabled (e.g., #nosec BXXX): 1
```

### Key Observations:
- Bandit detected the `# nosec B608` comment on line 53.
- The issue was **not reported** because the vulnerability was specifically disabled.
- One potential issue was **skipped** due to the targeted exception.

## Security Analysis and Recommendations

### Vulnerability Analysis
The **SQL injection vulnerability** identified in this application is a serious security concern:
- **CWE-89: SQL Injection** - This vulnerability allows attackers to inject malicious SQL code, potentially leading to unauthorized data access, data modification, or full system compromise.
- **Severity:** Medium (as classified by Bandit) with Low confidence.

### Best Practices for Remediation
While this project demonstrates how to implement exceptions, in a real-world application, vulnerabilities should be **fixed** rather than ignored. The following security best practices should be applied:

#### 1. Use Parameterized Queries
Instead of directly inserting user input into SQL queries:
```python
# Vulnerable Code:
statements_str += f"select * from employees where employee_id = {emp_id};"
```
Use parameterized queries:
```python
# Secure Code:
statements_str += "select * from employees where employee_id = ?;"
# Pass parameters separately when executing
```
#### 2. Implement Input Validation
Strict input validation should be enforced to ensure user input matches expected formats and types.

#### 3. Utilize ORM Libraries
Using **Object-Relational Mapping (ORM)** tools such as **SQLAlchemy** ensures automatic query parameterization and prevents SQL injection attacks.

## Conclusion
This research successfully demonstrated:
- **How to use Bandit** to identify security vulnerabilities in Python code.
- **The process of implementing targeted exceptions** for specific vulnerabilities.
- **Documentation of security findings and recommendations** for secure coding practices.

The ability to selectively disable specific security checks in Bandit allows organizations to gradually improve security while maintaining system functionality. This makes Bandit a valuable tool for security engineers and developers striving for secure software development.

## Future Work
- **Expand security testing** to cover additional files and applications.
- **Integrate Bandit into a CI/CD pipeline** for automated security testing.
- **Develop a security policy** defining when exceptions are acceptable versus when vulnerabilities must be remediated.

By refining security practices and integrating automated tools like Bandit, organizations can improve their software security posture and mitigate risks effectively.

