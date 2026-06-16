# AD_DS_Enterprise_Lab
Manual deployment of an enterprise Active Directory infrastructure with Role-Based Access Control (RBAC).
"I built a scalable identity management infrastructure from scratch using Active Directory Domain Services (AD DS). To mirror how a real company operates, I organized the environment into department-level Organizational Units (OUs). From there, I set up a strict Role-Based Access Control (RBAC) framework by creating separate Global Security Groups for management ('Supervisors') and regular employees ('Staff') within each business unit. Finally, I provisioned individual user accounts and mapped them directly to these groups—building a clean, secure, and easily maintainable permission baseline that scales cleanly as the company grows."

With the core identity structure in place, I shifted my focus to security hardening. A clean directory structure doesn't mean much if the front door is left wide open to brute-force attacks. To protect our users against automated password-guessing, I created and linked a root-level Group Policy Object (GPO) named Corporate_Security_Policy.

Inside the Account Lockout policies, I configured a strict '5-strikes' threshold. If anyone—or any malicious bot—attempts to guess a password and fails five consecutive times, Active Directory steps in and automatically freezes the account. To avoid leaving the network exposed during standard background refresh intervals, I hopped into the command line and forced an immediate global deployment using gpupdate /force. This wrapped up the phase by ensuring that our new security baseline went live across every single domain node instantly."

"After locking down our user security, I needed to solve a classic network dilemma: how to let our lab computers access the internet without breaking Active Directory.

Because Active Directory relies entirely on its own private DNS records to find local domain controllers and users, its network card is intentionally locked to its loopback address (127.0.0.1). This means the server looks entirely inward. It is the absolute authority for our private domain, but it has no idea how to find websites on the public internet like Google or GitHub.

To bridge this gap cleanly without breaking local authentication, I implemented a split-DNS strategy by configuring DNS Forwarders within the Windows DNS Manager.

Think of this as establishing a smart switchboard rule. When a user looks for a local server or computer, our domain controller handles it internally. But if a user tries to browse the web, our server recognizes that it doesn't own the public internet and automatically forwards that request out to a public resolver like Google DNS (8.8.8.8). This keeps our Active Directory environment perfectly isolated and stable while giving our infrastructure safe, reliable outbound access to the outside world."
