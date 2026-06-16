# AD_DS_Enterprise_Lab
Building an Enterprise Active Directory Infrastructure from Scratch (with RBAC & Security Hardening)
The Goal
I wanted to build a realistic, production-ready identity management system from the ground up using Active Directory Domain Services (AD DS). Instead of just setting up a basic lab, I wanted to mirror how a real company actually handles user access and security.

Step 1: Mapping the Company & Setting up RBAC
I started by organizing the environment into department-level Organizational Units (OUs) (like HR, Finance, IT, and Sales).

To handle permissions cleanly, I chose a Role-Based Access Control (RBAC) strategy. Instead of assigning permissions to users one by one (which would be a total nightmare to manage as a company grows), I created two Global Security Groups inside every single department:

[Department]_Supervisors (for management)

[Department]_Staff (for regular employees)

Once that scaffolding was up, I provisioned individual user accounts and dropped them directly into their matching groups. This gave me a super clean, organized permission baseline where if a new hire joins the Finance team, I just drop them in the Finance_Staff group and they instantly get the exact access they need.

Step 2: Locking the Front Door (Group Policy Hardening)
With the structure looking good, I realized a clean directory doesn't mean much if the front door is wide open to brute-force attacks. If a hacker or an automated bot wanted to, they could just guess passwords all day long.

To stop this, I created a domain-wide Group Policy Object (GPO) called Corporate_Security_Policy and linked it right at the top of the domain so it would apply to absolutely everyone.

Inside the policy editor, I set up a strict "5-strikes" rule under the Account Lockout settings. Now, if anyone tries to guess a password and gets it wrong 5 times in a row, Active Directory steps in and automatically freezes that account for 30 minutes.

To make sure this rule went into effect immediately—instead of waiting up to 90 minutes for Windows to update itself in the background—I opened up the command prompt and ran: "gpupdate /force". This forced every single computer on the domain to pull down the new rule right that second.

Step 3: Solving the Internet Dilemma (DNS Forwarders)
Next, I hit a classic network roadblock: my lab machines couldn't get onto the internet. Active Directory relies entirely on its own local DNS to find domain controllers and log users in. Because of this, the server’s network card has to point to its loopback address (127.0.0.1). It’s an expert at finding local machines, but because it only looks inward, it has no clue how to find external sites like Google or GitHub.

If I changed the network card to point to Google DNS directly to fix the internet, it would completely break Active Directory.

To solve this, I configured DNS Forwarders inside the Windows DNS Manager. It acts like a smart switchboard:

When a user looks for a local server, our domain controller handles it internally.

When a user tries to browse a public website, the server realizes, "Hey, I don't own the internet," and forwards that specific request out to Google's public resolver (8.8.8.8).

This gave me the best of both worlds: a perfectly isolated, stable Active Directory environment that can still talk to the outside world whenever it needs to update or download packages.
