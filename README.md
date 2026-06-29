# CS 305 – Software Security Portfolio

**Student:** Nick Ridente  
**Course:** CS 305 – Software Security  
**Artifact:** Practices for Secure Software Report (Project Two)

---

## Briefly summarize your client, Artemis Financial, and its software requirements. Who was the client? What issue did the company want you to address?

Artemis Financial is a financial services company that had an existing Spring Boot web application that wasn't secure. They came to us because they needed to protect the sensitive financial data being sent between their clients and their servers. The two main things they wanted addressed were making sure data couldn't be tampered with during transmission and encrypting all communication so nothing could be intercepted. Basically, they needed their app to go from HTTP to HTTPS and have some kind of data verification built in.

---

## What did you do well when you found your client's software security vulnerabilities? Why is it important to code securely? What value does software security add to a company's overall well-being?

I think I did a good job picking the right tools for the job rather than trying to overcomplicate things. For the data verification piece, I went with SHA-256 because it's a well-established, NIST-recommended hashing algorithm. There was no reason to reinvent the wheel when Java already has a built-in `MessageDigest` class that handles it cleanly. For the HTTPS side, I used Java Keytool to generate the SSL certificate and configured a PKCS12 keystore, which is the current industry standard.

Coding securely matters a lot, especially for a company like Artemis Financial where people are trusting them with their money and personal information. If there's a breach, it's not just a technical problem but can mean regulatory fines, lawsuits, and customers leaving. Building security in from the start is a lot cheaper and smarter than trying to patch it after something goes wrong.

---

## Which part of the vulnerability assessment was challenging or helpful to you?

Running the OWASP Dependency-Check report was probably the most eye-opening part. I knew going in that there might be some vulnerabilities in the third-party libraries, but seeing 92 vulnerabilities across 11 dependencies listed out was a little overwhelming at first. The helpful part was digging into the report and realizing that none of those vulnerabilities came from code I wrote they all existed in the original project's dependencies like Spring Boot, Tomcat, and SnakeYAML. That helped me understand the difference between vulnerabilities you introduce yourself versus ones that come from libraries you're pulling in, and why it's important to scan regularly and keep dependencies updated.

---

## How did you increase layers of security? In the future, what would you use to assess vulnerabilities and decide which mitigation techniques to use?

I added security in three main areas. First, I implemented SHA-256 hashing through a `/hash` REST endpoint, which lets the receiver verify that data arrived intact by comparing checksums. Second, I converted the application from HTTP to HTTPS by configuring SSL/TLS with a self-signed certificate, so all communication between the client and server is encrypted on port 8443. Third, since the `/hash` endpoint is only accessible over HTTPS, the API layer is protected as well.

Going forward, I'd continue using OWASP Dependency-Check as a first pass for finding known CVEs in third-party libraries. From there I'd prioritize fixing critical and high-severity vulnerabilities first, especially ones affecting dependencies that are actively used by the app. For mitigation, the options are usually updating the library, applying a patch, or suppressing the finding with documented justification if it's a false positive or not actually exploitable in context.

---

## How did you make certain the code and software application were functional and secure? After refactoring the code, how did you check to see whether you introduced new vulnerabilities?

After I finished refactoring, I ran the OWASP Dependency-Check again as a secondary scan to make sure I hadn't introduced anything new. Since I only used Java's built-in security libraries (`MessageDigest` for SHA-256 and the standard SSL/TLS configuration), nothing new showed up, all 92 vulnerabilities were pre-existing in the original project's dependencies.

I also did a manual code review of `SslServerApplication.java`. The `/hash` endpoint doesn't take any user input, which eliminates injection risks entirely. There's no hardcoded sensitive data in the source files (the keystore password is in `application.properties` which wouldn't be committed to a public repo in a real project). I tested it by running the app in Eclipse and navigating to `https://localhost:8443/hash` in a browser to confirm the SHA-256 checksum was displaying correctly over an encrypted connection.

---

## What resources, tools, or coding practices did you use that might be helpful in future assignments or tasks?

- **OWASP Dependency-Check (Maven plugin v9.0.9)** – really useful for automated scanning of third-party libraries against the National Vulnerability Database. I'd use this on any future Java/Maven project.
- **Java Keytool** – straightforward way to generate SSL certificates and manage keystores without needing third-party tools.
- **Java `MessageDigest` class** – clean built-in way to implement SHA-256 without pulling in extra libraries.
- **Spring Boot `application.properties`** – simple way to configure SSL settings without touching the core application code.
- **"Never roll your own crypto"** – this was reinforced throughout the project. Using established, audited implementations of cryptographic algorithms is always safer than writing your own, no matter how confident you are.

---

## Employers sometimes ask for examples of work that you have successfully completed to show your skills, knowledge, and experience. What might you show future employers from this assignment?

I'd show the Practices for Secure Software Report along with the refactored `SslServerApplication.java` and `application.properties` files. Together they give a pretty complete picture — the report shows I can identify security requirements, make informed decisions about which algorithms and tools to use, and document everything clearly. The code shows I can actually implement those decisions: SHA-256 hashing for data integrity, SSL/TLS configuration for encrypted communication, and clean REST endpoint design. I'd also point to the OWASP Dependency-Check report to show I understand how to use static analysis tools and interpret the results, which is something that comes up a lot in real development environments.
