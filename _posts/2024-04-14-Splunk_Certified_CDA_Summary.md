---
title: ["Splunk Certified Cybersecurity Defense Analyst [SPLK-5001]"]
date: 2025-04-14 09:00:00 +0900
last_modified_at: 2025-04-14 09:00:00 +0900
categories: [research, splunk]
tags: [splunk]
---

# Splunk Certified Cybersecurity Defense Analyst [SPLK-5001]

```
1.0 The Cyber Landscape, Frameworks, and Standards (*10%*)
  1.1 Summarize the organization of a typical SOC and the tasks belonging to Analyst, Engineer and Architect roles.
  1.2 Recognize common cyber industry controls, standards and frameworks and how Splunk incorporates those frameworks.
  1.3 Describe key security concepts surrounding information assurance including confidentiality, integrity and availability and basic risk management.

2.0 Threat and Attack Types, Motivations, and Tactics (*20%*)
  2.1 Recognize common types of attacks and attack vectors.
  2.2 Define common terms including supply chain attack, ransomware, registry, exfiltration, social engineering, DoS, DDoS, bot and botnet, C2, zero trust, account takeover, email compromise, threat actor, APT, adversary.
  2.3 Identify the common tiers of Threat Intelligence and how they might be applied to threat analysis.
  2.4 Outline the purpose and scope of annotations within Splunk Enterprise Security.
  2.5 Define tactics, techniques and procedures and how they are regarded in the industry.

3.0 Defenses, Data Sources, and SIEM Best Practices (*20%*)
  3.1 Identify common types of cyber defense systems, analysis tools and the most useful data sources for threat analysis.
  3.2 Describe SIEM best practices and basic operation concepts of Splunk Enterprise Security, including the interaction between CIM, Data Models and acceleration, Asset and Identity frameworks, and common CIM fields that may be used in investigations.
  3.3 Describe how Splunk Security Essentials and Splunk Enterprise Security can be used to assess data sources, including common sourcetypes for on-prem and cloud based deployments and how to find content for a given sourcetype.

4.0 Investigation, Event Handling, Correlation, and Risk (*20%*)
  4.1 Describe continuous monitoring and the five basic stages of investigation according to Splunk.
  4.2 Explain the different types of analyst performance metrics such as MTTR and dwell time.
  4.3 Demonstrate ability to recognize common event dispositions and correctly assign them.
  4.4 Define terms and aspects of Splunk Enterprise Security and their uses including SPL, Notable Event, Risk Notable, Adaptive Response Action, Risk Object, Contributing Events.
  4.5 Identify common built-in dashboards in Enterprise Security and the basic information they contain.
  4.6 Understand and explain the essentials of Risk Based Alerting, the Risk framework and creating correlation searches within Enterprise Security.

5.0 SPL (Search Processing Language) and Efficient Searching (*20%*)
  5.1 Explain common SPL terms and how they can be used in security analysis, including TSTATS, TRANSACTION, FIRST/LAST, REX, EVAL, FOREACH, LOOKUP, and MAKERESULTS.
  5.2 Give examples of Splunk best practices for composing efficient searches.
  5.3 Identify SPL resources included within ES, Splunk Security Essentials, and Splunk Lantern.

6.0 Threat Hunting and Remediation (*10%*)
  6.1 Identify threat hunting techniques including configuration, modeling (anomalies), indicators, and behavioral analytics.
  6.2 Define long tail analysis, outlier detection, and some common steps of hypothesis hunting with Splunk.
  6.3 Determine when to use adaptive response actions and configure them as needed.
  6.4 Explain the use of SOAR playbooks and list the basic ways they can be triggered from Enterprise Security.
```

---

# 0.0 Security - General

## Vulnerabilities

Vulnerabilities are weaknesses in a system. These are generally objects within the system in a specific state. These objects can be anything from a piece of software or a computer, to a user or door lock. Some of the same objects that can be vulnerabilities can also be threats. For instance, a user is a vulnerability if they lack security awareness, and they are also the threat actor when they are actively doing something (such as putting a USB drive into a computer.

Some other examples of vulnerabilities include:
* An email server without a spam filter
* An unpatched computer
* A weak password on a critical system

## Threats

Threats are the actors or situations that can use a vulnerability to impact the information system. While a vulnerability represents the state of an information system, a threat is a possible action that might or might not be performed.

Some other examples of threats include:
* A phisher
* Ransomware gangs
* Military cyber organizations

## Exploits

Exploits occur when threats create a tool, usually software, that can manipulate a vulnerability to cause an impact on an information system. For example, many ransomware used an improperly patched variant of Windows 7 to propagate. These ransomware relied on exploiting that specific vulnerability.

Some other examples of exploits include:
* A phishing email that successfully manipulates a user
* A trojan hiding in a music player
* A virus that can manipulate a log system to remove or manipulate data

## Risks

Risks are where threats, vulnerabilities, and exploits all converge. Anywhere a threat can exploit a vulnerability, there is a risk. An important thing to consider about risk is that it includes natural risks. Risks can be anything from an unauthorized actor using a stolen account on your network to a data center in a flood plane becoming flooded in a storm.

Some other examples of risks include:
* Being locked out of all data in your information system by ransomware
* Losing faith in log information due to a manipulation

## Common possible indicators of Malware

1. Sudden Popup Ads
    * If you are suddenly getting popups on your computer with ads from websites you may or may not be visiting, especially if the browser is not open, it is likely you have been infected with Adware
2. Command Line Popups on startup
    * Various applications will make changes on your computer at startup. While these popups are possibly indicative of malware, it's best to check with your IT team before worrying too much.
3. Periodic Slowdowns
    * Sometimes computers slow down. While periodic slowdowns (especially when working on large files or on an older system) can indicate malware, it is not very likely to be a malware infection.
    * These are in contrast to a massive, sudden slowdown of your computer. These are more likely to be indicative of malware.
4. Changes in Web Browser Behavior
    * Web Browser behavior is tricky. Different add-ons can change the behavior of the browser, as can various corporate policies. If you notice your web browser acting differently, you may have a malware infection. This would be a good time to reach out to your IT division
5. Massive, sudden slowdowns
    * Massive, sudden, and unexpected slowdowns of a computer that is not doing anything resource intensive is generally an indicator that you likely have a malware infection.
6. Anti-Malware software alerts
    * This one may go without saying, but if your anti-malware software is alerting you to malware you almost certainly have malware.
7. Firewall change requests
    * Several applications require opening ports on firewalls. If you are just using an application for the first time, have recently installed an application, or are on a managed computer, this could be malware, but it could also be nothing. This is a good time to reach out to your IT division.

## SIEM

* security information & event management
* centralized sec monitoring
* data analytics
* threat detection
* data correlation
* dashboards
* alerts
* reports
* gathered from variety of different sources
* UBA (user behavior analytics) to detect anomalies

## SOAR

* security orchestration, automation & response
* incident response (may be fully or partially automated)
* playbook workflows
* add-on integration (Splunk has ~300 of them in Splunkbase)
* dashboard visualizations
* automate detection & containment of malware incidents (e.g. ransomware, crypto minint, analysis of phising emails, vulnerability detection & remediation)
* generation of support tickets
* automated actions based on external security advisories (CVEs)
* Splunk SOAR is a different product, link to Splunk Ent. or Cloud - Automated playbooks, Comprehensive case management, App integrations, Simple, scalable security automation, Flexible deployment options, Built-in threat intelligence

# 0.0 Splunk - General

* monitoring system & app performance (app-specific details)
* detect potential sec problems
* mitigate verified sec threats
* realtime searching
* licensing and type of installation depends on what control do you require of the underlying configuration (e.g. no CLI with Splunk Cloud Platform)
* start monitoring of a location on Linux server: `./splunk add monitor /var/log`

## Splunk - Components

1. Splunk **Forwarders** - agents deployed on hosts
    * two types:
      1. **Universal** - unfiltered, raw data
      2. **Heavy** - essentially Splunk Enterprise instance, filtered data, various locations
    * data collection = `inputs.conf`
    * forward to indexers = `outputs.conf`
    * there are also "intermediary forwarders"
2. Splunk **Indexers** - data parsing, process input data from forwarders, port `9997`
    * logical grouping of data
    * group of indexers are called **search peers**
    * user defined data transformations (e.g. filtering out events)
    * data indexing (written to disk index that can be searched)
    * determine which events are sent to which indexes = `transforms.conf`
    * all forwarded data is stored in the main index by default (`index="main"`)
    * consider not storing all data in a single, large index
    * each data input can have it's own index
    * searches can specify one or multiple indexes
3. **Search head** / Web GUI - search interface, port `8000`
4. **Deployment** servers - optional, manages many forwarders
    * pull down apps and configs
    * activate by creating an app directory on the server

## Splunk Dashboards

1. Save queries as report (`Save as report`)
2. Add reports as panels on the dashboard (`Add to dashboard`)
3. Create a new dashboard:
    * Title
    * Description
    * Permission
    * Build with:
      * Classic
      * Studio

## Splunk Alerts

1. Save query as alert:
    * Alert type - scheduled (every hour/day/week/month/cron)
    * Trigger condition (when number of query results is ...)
    * Trigger - once or for each result
    * Trigger Action - Send email, webhook, mobile app ...

## Splunk - Sec monitoring

1. Example - **Snort**
    1. `/etc/snort/rules/local.rules` - e.g. add "Testing ICMP" rule
    2. Create a new index in Splunk - e.g. `snort_ids`
    3. `/opt/splunkforwarder/etc/apps/search/local/inputs.conf`
        ```
        [monitor:///var/log/snort/]
        disabled=false
        index=snort_ids
        host=linux_uf_1
        ```
    4. `./splunk restart`
    5. `snort -A console -K ascii -l /var/log/snort -c /etc/snort/snort.conf`
    6. Splunk web GUI: `index="snort_ids" ICMP`
2. Example - **Active Directory**
    * Local inputs.conf:
        ```
        [admon://DC1]
        disabled=false
        targetDC=fqdn.of.the.domain.controller
        startingNode=OU=East,DC=domain,DC=local
        index=activedirectory_events
        ```

## Splunk - HTTP Event Collector (HEC)

* by default on port `8088`
1. Global settings - All tokens - Enabled
2. New token - Name
3. New token - Input settings - Source type: Automatic
4. Token value
5. `curl -k https://<SPLUNK_SERVER>:8088/services/collector/event -H 'Authorization: Splunk <TOKEN>' -d '{"event": "Testing HEC"}'`

## Search Under the Hood

* **accelerated** data models (DM) make searches faster (e.g. accelerated over the last 1 year)
* Job Inspector
* SPL commenting anywhere with ```
* search bloom filter - tries to match what is in the buckets
* hot/warm/cold buckets
* `tsidx` files
* `tstats` is faster than `stats` since `tstats` only looks at the indexed metadata (the `.tsidx` files in the buckets on the indexers) whereas `stats` is working off the data (raw events)
* `| tstats count FROM datamodel=Malware`
* `| makeresults` command to create fake data, practicing
* `| fieldsummary`
* `| metadata` command returns a list of sources, sourcetypes, or hosts from a specified index or distributed search peer (default ones: `firstTime`, `lastTime`, `recentTime`, `source` or `host` depending on the type, `totalCount`, `type`)

### Major breakers
* A space ` `
* A newline `\n`
* A tab `   `
* Angle brackets `< >`
* Square brackets `[ ]`
* Parenthesis `( )`
* Curly brackets `{ }`
* An exclamation point `!`
* A question mark `?`
* A semicolon `;`
* A comma `,`
* Single and double quotation marks `' "`
* The ampersand sign `&`
* There are also multiple major breakers that use percent-encoding, primarily for reserved characters. These major breakers begin with a percent symbol followed by a code - For example, `%21` is the code for the exclamation point (`!`) character and `%2526` is a double encoded ampersand (`&&`)


## Splunk - Security products

* **Splunk Enterprise** = Is a flexible platform addressing an array of security use cases, enabling you to monitor and analyze machine data quickly from any source to deliver insights to act and the analytics-driven foundation to strengthen your overall security. Available in the cloud.
* **Splunk Security Essentials** = Explore new use cases and deploy security detections from Splunk Security Essentials to Splunk Enterprise and Splunk Cloud and Splunk SIEM and SOAR offerings. Now a fully supported app with an active Splunk Cloud license, start strengthening your security posture and quicken your time-to-value with Splunk.
* **Splunk Enterprise Security** = A security information and event management (SIEM) solution that provides insights into machine data generated from security technologies such as network, endpoint and access; as well as malware, vulnerability and identity information. Available in the cloud.
* **Splunk SOAR** = A security orchestration, automation and response (SOAR) platform that integrates with your existing security technologies to provide a layer of "connective tissue" between them, making them smarter, faster and stronger. Available in the cloud.
* **Splunk Phantom**
* **Splunk User Behavior Analytics** = A machine-learning-powered solution that delivers answers organizations need to find unknown threats and anomalous behavior across users, endpoint devices and applications.
* **Splunk Mission Control** = Multiple products together (ES & UBA + SOAR + SSE + 3rd party)
* **Splunk Security Content** = For customers with Splunk Enterprise Security (ES), this delivers security analysis guides, called "Analytic Stories", that explain how to best use Splunk ES to investigate and take action on new threats detected in your environment, what searches to implement and what you should be able to achieve.
* **Splunk Security for SAP solutions**
* **Splunk Attack Analyzer**

## Splunk - Use cases

|Use case|Product/Solution|
|--------|----------------|
|Incident Investigation and Forensics | Splunk Enterprise, Splunk Enterprise Security, Splunk SOAR|
|Security Monitoring | Splunk Enterprise, Splunk Security Essentials App, Splunk Enterprise Security, Splunk SOAR|
|Advanced Threat Detection | Splunk Enterprise, Splunk Security Essentials App, Splunk Enterprise Security, Splunk User Behavior Analytics|
|SOC Automation | Splunk Enterprise, Splunk Enterprise Security, Splunk SOAR|
|Incident Response | Splunk Enterprise, Splunk Enterprise Security, Splunk SOAR|
|Compliance | Splunk Enterprise, Splunk Security Essentials App, PCI, Splunk Enterprise Security|
|Fraud Analytics and Detection | Splunk Enterprise, Splunk Security Essentials App, Splunk Enterprise Security|
Insider Threat Detection | Splunk Enterprise, Splunk Security Essentials App, Splunk User Behavior Analytics|

# 0.0 Quiz

1. If a search begins with a distributable streaming command, where is it first executed?
    1. It depends on whether or not the command is a centralized command
    2. **On the search head**
    3. On the indexer
    4. It depends on whether or not the command is a transforming command

2. Which architectural component of a Splunk deployment initiates a search?
    1. Index
    2. Indexer
    3. Forwarder
    4. **Search Head**

3. Which component of a bucket stores raw event data?
    1. TSIDX files
    2. **Journal**
    3. Lexicon
    4. Posting List

4. Which of the following breakers would be used first in segmentation?
    1. **Periods**
    2. Hyphens
    3. Colons
    4. Commas

5. When is a bucket's bloom filter created?
    1. When a search is run.
    2. **When a bucket rolls from hot to warm.**
    3. When a TSIDX file is created.
    4. When a bucket rolls from warm to cold.

6. After Splunk tokenizes terms at index time, where are the tokens stored?
    1. In a frozen bucket
    2. **In the lexicon**
    3. In the posting list
    4. In a bloom filter

7. Where in the search pipeline are transforming commands executed?
    1. On the indexer
    2. **On the search head**
    3. Inside a hot bucket
    4. Inside a warm bucket

8. Which component of the Search Job Inspector shows how long a search took to execute?
    1. **Execution Costs**
    2. Search Job Properties
    3. Execution Time Range
    4. Header

9. Which of the following expressions builds a search-time bloom filter?
    1. An eval expression
    2. A macro expression
    3. **A lispy expression**
    4. A regular expression

10. Which of the following syntaxes signify a comment in SPL?
    1. comment surrounded by ```

11. Which of the following commands generates temporary search results?
    1. `tempresults`
    2. `genresults`
    3. `resultgen`
    4. **`makeresults`**

12. Which of the following conditions could cause a lispy expression to not create tokens?
    1. A wildcard (`*`) at the beginning of a search

13. Which directive can be used in a search to bypass minor breakers inside the supplied argument?
    1. `TERM`

14. When is it appropriate to use Auto Deployment on `Splunk_TA_ForIndexers` in a distributed search configuration?
    1. When the indexers are clustered.
    2. When there are multiple indexers with the same retention settings.
    3. **When there are multiple indexers with the same storage volume settings.**
    4. When there are multiple indexers with different volume and retention settings.

15. In order for ES to automatically take an action upon locating a particular event, what can a correlation search be configured to execute?
    1. Action script
    2. Activation prompt
    3. **Adaptive response**
    4. Integration script

16. When creating a correlation search, which command will generate a notable event if the risk score for any one host is greater than 100?
    1. **| where 'risk_score' > 100**
    2. | eval risk_score > 100
    3. | sum(host)risk_score > 100
    4. | All_Risk.risk_score > 100

17. Which of the following accurately describes an individual notable event?
    1. **It is immutable.**
    2. It can be cloned.
    3. It can have its status changed.
    4. It can be assigned to an analyst.

18. Which of the following is an adaptive threshold best practice?
    1. Use if there is no consistent flow of data.
    2. Disable backfill on adaptive threshold data.
    3. **Use when KPI values are expected to move dynamically.**
    4. Update adaptive threshold values manually each day at midnight.

19. Within a correlation search, how can a service be associated?
    1. By using lookup in the ad hoc search.
    2. By modifying correlation_searches.conf
    3. By specifying an appropriate time range.
    4. **By adding the service name to the service field.**

# 1.1 Organization of a typical SOC

## Cyber Teams
* **red** = breakers, offensive
* **blue** = defenders

A typical SOC is organized into three main teams:

* **Security Analysts:** This team is responsible for monitoring security alerts and events, identifying potential threats, and investigating suspicious activity. They may also be involved in responding to security incidents and implementing security controls.
* **Security Engineers:** This team is responsible for designing, deploying, and maintaining the SOC's security infrastructure. They also work to develop and implement new security solutions and processes.
* **Security Architects:** This team is responsible for developing and implementing the organization's overall security strategy. They also work to ensure that the SOC's security infrastructure and processes are aligned with the organization's security needs.

## Tasks belonging to Analyst, Engineer and Architect roles

### Security Analyst

* Monitor security alerts and events
* Identify potential threats
* Investigate suspicious activity
* Respond to security incidents
* Implement security controls
* Analyze security data
* Write security reports

### Security Engineer

* Design, deploy, and maintain security infrastructure
* Develop and implement new security solutions and processes
* Manage security tools and technologies
* Troubleshoot security issues
* Conduct security assessments
* Work with security analysts to investigate and respond to security incidents

### Security Architect

* Develop and implement the organization's overall security strategy
* Ensure that the SOC's security infrastructure and processes are aligned with the organization's security needs
* Work with other security teams to develop and implement security solutions
* Keep up with the latest security trends and threats
* Provide security guidance and advice to the organization

There is also **SOC Manager** role:
* SOC managers detect and respond to cyber security threats to ensure your organization operates securely
* They manage the team, develop policies and procedures, and keep the CISO informed about security operations
* Training and managing SOC staff
* Developing and implementing security policies
* Establishing SOC performance goals and priorities
* Overseeing SOC activities
* Managing SOC tools and resources
* Leading incident response efforts
* Analyzing incident reports
* Serving as POC for security incidents
* Reporting to the CISO about security operations
* Providing performance reviews to the SOC team

# 1.2 Common cyber industry controls, standards and frameworks

Some of the most common cyber industry controls, standards, and frameworks include:

* **NIST Cybersecurity Framework (CSF)**: National Institute of Standards and Technology Cybersecurity Framework is a voluntary framework that provides a set of standards, guidelines, and best practices to help organizations manage cybersecurity risk.
* **CIS Controls**: The CIS Controls are a prioritized set of 20 actionable security controls that organizations can implement to mitigate the majority of known cyber threats.
* **MITRE ATT&CK Framework**: The ATT&CK Framework is a knowledge base of adversary tactics and techniques that organizations can use to improve their detection and response capabilities.
* **ISO/IEC 27001**: ISO/IEC 27001 is an international standard that provides a framework for managing information security.
* **PCI DSS**: The PCI DSS is a set of security standards that organizations that process, transmit, or store credit card data must comply with.

## How Splunk incorporates those frameworks

Splunk incorporates a variety of cyber industry controls, standards, and frameworks into its products and services. For example:
* Splunk Enterprise Security helps organizations implement and manage the NIST CSF, CIS Controls, and MITRE ATT&CK Framework.
* Splunk Cloud Platform is certified to ISO/IEC 27001 and PCI DSS.
* Splunk also offers a variety of add-ons and integrations that help organizations comply with specific industry regulations and standards.

Here are some specific examples of how Splunk can be used to implement and support common cyber industry controls, standards, and frameworks:
* **NIST CSF**: Splunk Enterprise Security can be used to implement and manage all of the core functions of the NIST CSF, including asset management, risk management, threat detection and response, and incident management.
* **CIS Controls**: Splunk Enterprise Security can be used to implement and manage all of the CIS Controls. For example, Splunk can be used to collect and analyze security logs, detect suspicious activity, and generate security reports.
* **MITRE ATT&CK Framework**: Splunk Enterprise Security can be used to map security data to the MITRE ATT&CK Framework, which can help organizations identify gaps in their security coverage and improve their detection and response capabilities. Splunk displays which techniques contributed to the Risk Notable and tactics those techniques fall under.
* **ISO/IEC 27001**: Splunk Cloud Platform is certified to ISO/IEC 27001, which means that it meets the security requirements of the standard. This can help organizations comply with ISO/IEC 27001 and other industry regulations.
* **PCI DSS**: Splunk Cloud Platform is also certified to PCI DSS, which means that it can be used to help organizations comply with the PCI DSS security standards.

NIST CSF functions:
1. Identify
2. Protect
3. Detect
4. Respond
5. Recover

# 1.3 Key security concepts surrounding information assurance including confidentiality, integrity and availability and basic risk management

**Confidentiality, integrity, and availability (CIA)** are the three key security concepts surrounding information assurance.

* **Confidentiality** means that information is only accessible to authorized individuals.
* **Integrity** means that information is accurate and complete.
* **Availability** means that information is accessible to authorized individuals when they need it.

Organizations must implement security controls and processes to protect the confidentiality, integrity, and availability of their information assets.

**Basic risk management** is the process of identifying, assessing, and mitigating risks to information assets.

* **Identify risks:** The first step is to identify the potential risks to information assets. This can be done by considering the assets themselves, the threats that exist, and the vulnerabilities that could be exploited.
* **Assess risks:** Once risks have been identified, they need to be assessed to determine their likelihood and impact. This will help organizations to prioritize their risk mitigation efforts.
* **Mitigate risks:** There are a variety of ways to mitigate risks. This can include implementing security controls, developing policies and procedures, and training employees.

Organizations should regularly review their risk management processes to ensure that they are effective and up-to-date.

Here are some examples of how organizations can implement security controls and processes to protect the CIA of their information assets:

* **Confidentiality (C):**
    * Encrypt sensitive data at rest and in transit.
    * Implement access control lists to restrict access to information systems and data.
    * Use strong authentication and authorization mechanisms.
* **Integrity (I):**
    * Implement data integrity checks to detect unauthorized changes to data.
    * Use digital signatures to verify the authenticity of data.
    * Implement a backup and recovery plan to restore data in the event of a breach.
* **Availability (A):**
    * Implement redundant systems and components to ensure that systems and data are still available even if one component fails.
    * Use load balancing to distribute traffic across multiple systems.
    * Implement a disaster recovery plan to recover systems and data in the event of a major outage.

# 2.1 Recognize common types of attacks and attack vectors

**Common types of attacks**

* **Malware:** Malware is malicious software that can damage or disable computer systems or networks. Common types of malware include viruses, worms, trojans, ransomware, and spyware.
* **Phishing:** Phishing is a type of social engineering attack that attempts to trick users into revealing sensitive information, such as passwords or credit card numbers. Phishing attacks are often carried out through email or malicious websites.
* **Denial-of-service (DoS) attacks:** DoS attacks attempt to overwhelm a computer system or network with traffic, making it unavailable to legitimate users.
* **Man-in-the-middle attacks:** Man-in-the-middle attacks intercept communications between two parties and impersonate one of the parties in order to steal data or modify the communication.

**Common attack vectors**

* **Email:** Email is a common attack vector for phishing attacks, malware distribution, and social engineering attacks.
* **Web browsers:** Web browsers are a common attack vector for malware distribution, drive-by downloads, and cross-site scripting (XSS) attacks.
* **Physical access:** Physical access to a computer or network can be used to install malware, steal data, or sabotage systems.
* **Social engineering:** Social engineering attacks exploit human psychology to trick users into revealing sensitive information or performing actions that compromise security.

Organizations can protect themselves from common attacks and attack vectors by implementing security controls and processes such as:

* **Email filtering and anti-spam solutions:** These solutions can help to block phishing emails and malware attachments.
* **Web filtering and intrusion detection systems:** These solutions can help to block malicious websites and detect malware infections.
* **Endpoint protection:** Endpoint protection solutions can help to protect computers and other devices from malware infections.
* **Security awareness training:** Security awareness training can help employees to identify and avoid social engineering attacks.

# 2.2 Define common terms

1. **Supply chain attack** = A supply chain attack is a type of cyber attack that targets an organization through its suppliers or other third-party partners.
2. **Ransomware** = Ransomware is a type of malware that encrypts a victim's data and demands a ransom payment in exchange for the decryption key.
3. **Registry** = The registry is a database that stores configuration settings for the Windows operating system and applications.
4. **Exfiltration** = Exfiltration is the unauthorized transfer of data from an organization's systems or networks.
5. **Social engineering** = Social engineering is a type of cyber attack that exploits human psychology to trick users into revealing sensitive information or performing actions that compromise security.
6. **DoS** (Denial-of-Service) attack = A DoS attack attempts to overwhelm a computer system or network with traffic, making it unavailable to legitimate users.
7. **DDoS** (Distributed Denial-of-Service) attack = A DDoS attack is a type of DoS attack that uses a large number of compromised devices, such as botnets, to generate traffic.
8. **Bot** and **botnet** = A bot is a compromised computer or device that is under the control of an attacker. A botnet is a network of bots that can be used to carry out DDoS attacks, spam campaigns, and other malicious activities.
9. **C2** (Command and Control) server = A C2 server is a server that is used by an attacker to communicate with and control bots in a botnet.
10. **Zero trust** = Zero trust is a security model that assumes that no user or device is inherently trusted and that all access to systems and data must be verified.
11. **Account takeover** = Account takeover is a type of cyber attack in which an attacker gains unauthorized access to a user's account on a website or online service.
12. **Email compromise** = Email compromise is a type of cyber attack in which an attacker gains unauthorized access to a user's email account.
13. **Threat actor** = A threat actor is an individual or group that poses a threat to an organization's security.
14. **APT** (Advanced Persistent Threat) = An APT is a type of threat actor that is highly skilled and well-resourced and that is capable of carrying out sophisticated and targeted attacks.
15. **Adversary** = An adversary is a threat actor that is opposed to an organization's goals or interests.
16. **PII** = personal identifiable information
17. **PHI** = personal health information
18. **PCI** = payment card information
19. **False Positive** = False alarm, identifies an activity as an attack but the activity is normal behavior
20. **False Negative** = No alarm is raised when an attack has occurred
21. **True Positive** = Legitimate attack which triggers to produce an alarm
22. **True Negative** = Event when no attack has occurred and no detection is made
23. **Lateral movement** = a technique that adversaries use, after compromising an endpoint, to extend access to other hosts or applications in an organization

# 2.3 Identify the common tiers of Threat Intelligence and how they might be applied to threat analysis

## Splunk Threat Intelligence Management (TIM)

* SES & Mission Control feature
* Investigate sec events & suspicious activity
* Assist with investigations, accelerate time to triage
* Uses IoC (indications of compromise) in KV stores to improve detection

Process:
1. Launch TIM, connect via marketplace to an intelligence (CrowdStrike, Digital Shadows, MISP, Taxii...)
2. Create a new workflow
3. Select up to 5 intelligence sources, weight 1-5
4. Transformations
5. Destination: Splunk Mission Control

**Common tiers of threat intelligence**

Threat intelligence is typically divided into three tiers:

* **Tactical:** Tactical threat intelligence is the most detailed and actionable type of threat intelligence. It provides information about specific threats, such as malware campaigns, attack vectors, and indicators of compromise (IOCs). Tactical threat intelligence is typically used by security analysts to detect and respond to threats.
* **Operational:** Operational threat intelligence provides information about the actors behind threats, their motivations, and their capabilities. It can also provide information about emerging threats and trends. Operational threat intelligence is typically used by security managers to make informed decisions about security investments and priorities.
* **Strategic:** Strategic threat intelligence provides information about the geopolitical landscape and how it relates to cybersecurity. It can also provide information about the long-term goals and objectives of threat actors. Strategic threat intelligence is typically used by security executives to make informed decisions about the organization's overall security strategy.

**How threat intelligence is applied to threat analysis**

Threat intelligence can be applied to threat analysis in a variety of ways. For example, security analysts can use threat intelligence to:

* **Identify new threats:** Threat intelligence can help security analysts to identify new threats that they may not be aware of.
* **Understand the threat landscape:** Threat intelligence can help security analysts to understand the current threat landscape and the threats that are most likely to target their organization.
* **Prioritize threat response:** Threat intelligence can help security analysts to prioritize their threat response efforts and focus on the threats that pose the greatest risk to their organization.
* **Improve security detection and prevention:** Threat intelligence can be used to improve the detection and prevention of threats by providing security analysts with information about known attack vectors and IOCs.

Overall, threat intelligence is a valuable tool that can be used to improve the effectiveness of threat analysis and security operations.

Here are some specific examples of how threat intelligence can be applied to threat analysis:

* A security **analyst** can use tactical threat intelligence to identify known malware campaigns that are targeting their organization. The analyst can then use this information to develop detection and response rules to mitigate the risk of these campaigns.
* A security **manager** can use operational threat intelligence to understand the motivations and capabilities of threat actors that are targeting their industry. This information can be used to develop security controls and policies that are tailored to the specific threats that the organization faces.
* A security **executive** can use strategic threat intelligence to make informed decisions about the organization's overall security strategy. For example, if the organization is facing an increased risk of attacks from a particular nation-state, the security executive may decide to allocate more resources to defending against those attacks.

# 2.4 Purpose and scope of annotations within Splunk Enterprise Security

Annotations are used in Splunk Enterprise Security (ES) to **provide additional context and enrichment to security data**. They can be used to tag events, identify risks, and map data to security frameworks. Annotations can also be used to collaborate with other security analysts and share insights.

The scope of annotations in ES is broad. They can be used to annotate any type of data, including events, alerts, incidents, and risk objects. Annotations can also be used to annotate other annotations, creating a nested structure of information.

Here are some specific examples of how annotations can be used in ES:

* **Tag events:** Annotations can be used to tag events with information such as the severity of the event, the source of the event, or the potential impact of the event. This can help security analysts to quickly identify and prioritize important events.
* **Identify risks:** Annotations can be used to identify risks to the organization's security posture. For example, an annotation might be used to identify a new vulnerability in a critical asset or a new malware campaign that is targeting the organization's industry.
* **Map data to security frameworks:** Annotations can be used to map security data to security frameworks such as MITRE ATT&CK and NIST CSF. This can help security analysts to better understand the context of security events and to identify gaps in their security coverage. For example `annotations.mitre_attack.mitre_technique_id` - you can use that to verify that it works: `| inputlookup mitre_attack_lookup`
* **Collaborate with other security analysts:** Annotations can be used to collaborate with other security analysts and share insights. For example, an annotation might be used to leave a comment on an event or to share a link to a relevant research article.

**Additional benefits of using annotations in Splunk Enterprise Security:**

* **Improved visibility:** Annotations can be used to improve the visibility of security data by providing additional context and enrichment. This can help security analysts to identify, investigate, and respond to threats more quickly and effectively.
* **Reduced noise:** Annotations can be used to reduce the noise of security data by tagging important events and identifying risks. This can help security analysts to focus on the most critical information and to avoid getting bogged down in irrelevant data.
* **Increased collaboration:** Annotations can be used to increase collaboration between security analysts by providing a way to share insights and communicate about security events. This can help to improve the overall security posture of the organization.

# 2.5 Define tactics, techniques and procedures and how they are regarded in the industry

**Tactics, techniques, and procedures (TTPs)** are the methods that threat actors use to plan, carry out, and achieve their objectives. Tactics are the high-level goals of an attack, such as gaining access to a system or stealing data. Techniques are the specific methods that are used to achieve the tactical goals. Procedures are the detailed steps that are taken to implement the techniques.

TTPs are regarded in the industry as a valuable tool for understanding and defending against cyber attacks. By understanding the TTPs that are commonly used by threat actors, organizations can develop security controls and detection mechanisms to mitigate the risk of attack.

TTPs are also used by security researchers to track the activities of threat actors and to develop new security solutions. For example, if security researchers observe a new malware campaign using a new technique, they can develop a new detection rule to identify and block the malware.

Here are some examples of TTPs:

* **Tactics:**
    * Reconnaissance: Gathering information about the target organization, such as its systems, networks, and users.
    * Initial access: Gaining initial access to the target organization's systems or networks.
    * Execution: Executing malicious code on the target organization's systems.
    * Persistence: Maintaining access to the target organization's systems or networks.
    * Command and control: Communicating with the target organization's systems or networks to control them and exfiltrate data.
* **Techniques:**
    * Phishing: Sending fraudulent emails to trick users into revealing sensitive information or clicking on malicious links.
    * SQL injection: Injecting malicious SQL code into database queries to gain unauthorized access to data.
    * Cross-site scripting (XSS): Injecting malicious JavaScript code into web pages to steal cookies or other sensitive data from users.
    * Remote code execution (RCE): Exploiting vulnerabilities in software to execute malicious code on remote systems.
    * Fileless malware: Deploying malware that does not require any files to be written to disk, making it more difficult to detect and remove.
* **Procedures:**
    * Using a phishing email to trick a user into revealing their credentials.
    * Injecting malicious SQL code into a database query to steal customer information.
    * Injecting malicious JavaScript code into a web page to steal the user's session cookie.
    * Exploiting a vulnerability in a software application to execute a remote shell on the target system.
    * Using a fileless malware loader to deploy a backdoor on the target system.

TTPs are constantly evolving, as threat actors develop new methods to attack organizations. However, by understanding the common TTPs that are used by threat actors, organizations can better protect themselves from cyber attacks.

**How TTPs are regarded in the industry:**

TTPs are regarded in the industry as a valuable tool for understanding and defending against cyber attacks. They are used by security professionals to develop security controls, detection mechanisms, and incident response plans. TTPs are also used by security researchers to track the activities of threat actors and to develop new security solutions.

Here are some specific examples of how TTPs are used in the industry:

* Security teams use TTPs to develop **security controls**, such as firewalls, intrusion detection systems, and access control lists. These controls can help to prevent attackers from exploiting TTPs to gain access to systems and data.
* Security teams also use TTPs to develop **detection mechanisms**. For example, security teams can use TTPs to develop rules for security information and event management (SIEM) systems. These rules can help to identify suspicious activity that may be indicative of an attack.
* Security teams also use TTPs to develop **incident response plans**. These plans outline the steps that should be taken to respond to a cyber attack. TTPs can help security teams to identify the scope of an attack, contain the attack, and eradicate the malware.
* Security researchers use TTPs to **track the activities of threat actors**. By understanding the TTPs that are being used by threat actors, security researchers can develop new security solutions to defend against these attacks.

# 3.1 Common types of cyber defense systems:

1. Firewalls: Firewalls monitor and control incoming and outgoing network traffic based on predetermined security rules. They can help to prevent unauthorized access to systems and data.
2. Intrusion detection systems (IDS)/Intrusion prevention systems (IPS): IDS/IPS systems monitor network traffic for malicious activity and can alert administrators or take action to block the activity.
3. Antivirus software: Antivirus software scans files for malicious code and can remove or quarantine infected files.
4. Endpoint security software: Endpoint security software provides a comprehensive suite of security features for devices such as computers, laptops, and mobile devices. These features can include antivirus, anti-malware, firewall, and intrusion detection.
5. Security information and event management (SIEM) systems: SIEM systems collect and analyze security logs from a variety of sources to identify suspicious activity and generate alerts.

Common types of cyber security analysis tools:
1. Network traffic analyzers: Network traffic analyzers capture and analyze network traffic to identify suspicious activity.
2. Packet sniffers: Packet sniffers capture and analyze individual packets of data on a network.
3. Log analyzers: Log analyzers collect and analyze security logs from a variety of sources to identify suspicious activity.
4. Malware analysis tools: Malware analysis tools analyze malware samples to identify their capabilities and how to remove them.
5. Threat intelligence platforms: Threat intelligence platforms collect and analyze threat data from a variety of sources to provide insights into the latest threats and trends.

Most useful data sources for threat analysis:
1. Security logs: Security logs contain information about events that occur on systems and networks. This information can be used to identify suspicious activity and investigate security incidents.
2. Network traffic: Network traffic can be analyzed to identify malicious activity, such as malware infections and data exfiltration.
3. Threat intelligence: Threat intelligence can provide insights into the latest threats and trends. This information can be used to improve security defenses and detect and respond to threats more quickly.

Other useful data sources for threat analysis include:
* Vulnerability assessment results: Vulnerability assessment results can identify vulnerabilities in systems and networks that could be exploited by attackers.
* Publicly available information: Publicly available information, such as social media posts and news articles, can provide insights into the motivations and capabilities of threat actors.

# 3.2 SIEM best practices

Security information and event management (SIEM) systems are a valuable tool for detecting and responding to security incidents. However, to get the most out of a SIEM system, it is important to implement it and use it correctly. Here are some SIEM best practices:
1. Collect the right data: The first step is to collect the right data from your systems and networks. This data should include security logs, network traffic, and other data that can be used to identify suspicious activity.
2. Normalize the data: The data that is collected by a SIEM system should be normalized to a common format. This will make it easier to search and analyze the data.
3. Enrich the data: The data that is collected by a SIEM system can be enriched with additional information, such as threat intelligence and asset information. This will help security analysts to better understand the context of the data and to identify potential threats.
4. Create rules and alerts: SIEM systems allow you to create rules and alerts to identify suspicious activity. These rules and alerts can be used to generate notifications for security analysts or to take automated actions, such as blocking malicious traffic.
5. Monitor the SIEM system: It is important to monitor the SIEM system on a regular basis to ensure that it is working properly and that it is generating accurate alerts.

## Basic operation concepts of Splunk Enterprise Security

Splunk Enterprise Security (ES) is a popular SIEM solution that provides a variety of features for collecting, analyzing, and responding to security incidents.

The basic operation concepts of Splunk ES are as follows:
1. Data **ingestion**: Splunk ES can ingest data from a variety of sources, including security logs, network traffic, and threat intelligence feeds.
2. Data **indexing**: Splunk ES indexes the data that it ingests to make it searchable and analyzable.
3. Data **analysis**: Splunk ES provides a variety of tools for analyzing security data, such as search, reporting, and dashboards.
4. Incident **response**: Splunk ES provides a variety of features for responding to security incidents, such as alerting, case management, and integrations with other security tools.

## Interaction between Common Information Model (CIM), Data Models, and acceleration

The CIM is a data model that provides a common way to represent security data. Splunk ES uses the CIM to normalize data from different sources and to make it easier to search and analyze.

Data Models in Splunk ES are used to define the fields and tags that are used to represent different types of security data. For example, there are Data Models for events, alerts, incidents, and risk objects.

Acceleration in Splunk ES is used to improve the performance of search queries. Splunk ES can accelerate search queries by pre-processing data and by caching search results.

The interaction between CIM, Data Models, and acceleration is as follows:
1. CIM: Splunk ES uses the CIM to normalize data from different sources and to make it easier to search and analyze.
2. Data Models: Data Models are used to define the fields and tags that are used to represent different types of security data.
3. Acceleration: Splunk ES can accelerate search queries by pre-processing data and by caching search results.

## Asset and Identity frameworks

* Splunk ES provides Asset and Identity frameworks that can be used to manage and enrich asset and identity data.
* The Asset framework can be used to manage asset information such as IP addresses, hostnames, and operating systems. The Identity framework can be used to manage identity information such as usernames, email addresses, and roles.
* Asset and Identity frameworks can be used to automatically enrich security data referencing users/accounts/identities/devices/assets with additional information. For example, an alert can be enriched with the asset information for the host that generated the alert.
* Multiple identities correspond to the same asset = grouped together

## Common CIM fields that may be used in investigations

Here are some common CIM fields that may be used in investigations:
* `event_type` = The type of event, such as a system log event, network traffic event, or security alert.
* `source` = The source of the event, such as a host, IP address, or application.
* `destination` = The destination of the event, such as a host, IP address, or application.
* `timestamp` = The time at which the event occurred.
* `user` = The user associated with the event.
* `host` = The host associated with the event.
* `message` = The message associated with the event.
* `normalized_risk_object` = correlation searches can grp risk events & asset/identity
* `src_user` = In privilege escalation events, represents the user who initiated the privilege escalation
* `src_user_id` = unique id of the user who initiated the privilege escalation
* `user` =  string or identifier that a user is logging in with
* `user_id` = unique id of the user involved in the event

## Auditing Splunk

* Splunk logs information about itself:
  * Performance & system data
  * Scheduler activity
  * System errors
  * User activity
* Only accessible by admin user:
  * `index=_audit` (logins, login failures, configurations, searches)
  * `index=_internal`
  * `index=_introspection`

# 3.3 Splunk Security Essentials and Splunk Enterprise Security for data source assessment

Splunk Security Essentials (SSE) and Splunk Enterprise Security (SES) can be used to assess data sources in a variety of ways. Both products provide a variety of tools for searching, analyzing, and visualizing security data.

## Splunk Enterprise Security (SES)

* installed on Splunk server
* constantly running in the background, works behind the scenes
* uses **corellation searches** to detect notable events (`index=notable`)
* **notable event** urgency:
  1. assets (IP, MAC, dns etc)
  2. identities (user, username, email etc)
* captures data from network capture devices, fws/proxies, production servers, vuln. scanners
* uses custom **data models** (DM)
* if you need to customize DMs, don't touch the default ones but rather create a clone
* data models turn **unstructured** (raw) data to **structured** data
* many predefined searches/dashboards (e.g. Asset investigator dashboard)
* cloud-based streaming security analytics = behavioral analytis services, enhances SES, advanced threat detection to uncover unknown threats
* Functional areas:
  1. **Perimeter defense** - known threats (vuln., traffic, unexp. processes etc)
  2. **Preventative analysis** - anomaly detection (traffic naalysis, pattern matching etc)
  3. **Breach response** - incident tracking, investigation journaling, forensics, auditing
* 6 tools:
  1. security posture
  2. incident review
  3. app configuration
  4. documentation
  5. community
  6. product tour
* The 6 analytics-driven security according to Splunk are:
  1. **Collection** = Collect basic security logs and other machine data from your environment, focuses on obtaining raw materials necessary to start gaining a deeper understanding of the environment you must defend
  2. **Normalization** = Apply a standard security taxonomy and add asset and identity data, ensuring your data is compliant and that the fields representing common values (e.g. source IP...) have common names regardless of which device created the event
  3. **Expansion** = Collect additional data sources like endpoint activity and network metadata to drive advanced attack detection, DNS and endpoint data will unlock a rich set of detection capabilities empowering threat hunters to uncover and track adversaries dwelling in the network. The only metadata associated to any particular event (by default) is: `host`, `sourcetype`, `source`, and `index`.
  4. **Enrichment** = Augment security data with intelligence sources to better understand the context and impact of an event, contextual and investigative knowledge (including threat-intelligence feeds, open-source intelligence sources and internally sourced information) allows security personnel to extract more value from the collected data to detect security events and incidents sooner
  5. **Automation and Orchestration** = Establish a consistent and repeatable security operation capability, leveraging a security orchestration, automation and response (SOAR) solution allows organizations to reduce risk in a number of powerful ways
  6. **Advanced Detection** = Apply sophisticated detection mechanisms including machine learning/data science/advanced statistics

## Kill Chain (KC)

* developed by Lockheed Martin, the Cyber Kill Chain framework is part of the Intelligence Driven Defense model for identification and prevention of cyber intrusions activity
* the model identifies what the adversaries must complete in order to achieve their objective.
* Stages:
  1. **Delivery** - attempt to infiltrate
  2. **Exploitation**
  3. **Command & Control** - execute code, change settings etc
  4. **Accomplishment**
* ES protects you during every KC stage
* provides indicators of compromise (IoC)

## Common sourcetypes for on-prem and cloud based deployments

Here are some common sourcetypes for on-prem and cloud-based deployments:

1. On-prem:
  * Windows system logs
  * Linux system logs
  * Firewall logs
  * IDS/IPS logs
  * Antivirus logs
  * Application logs
2. Cloud:
  * AWS CloudTrail logs
  * Azure Activity logs
  * GCP Audit logs
  * Web application logs
  * Cloud storage logs

## How to find content for a given `sourcetype`

To find content for a given `sourcetype`, you can use the following methods:
1. **Splunkbase**: Splunkbase is a community-driven website where users can share Splunk content, such as dashboards, reports, and searches. To find content for a given `sourcetype`, you can search Splunkbase for the `sourcetype` name.
2. Splunk **documentation**: The Splunk documentation provides information on a variety of topics, including `sourcetypes`. To find information on a given `sourcetype`, you can search the Splunk documentation for the `sourcetype` name.
3. Splunk **support**: If you cannot find content for a given `sourcetype` on Splunkbase or in the Splunk documentation, you can contact Splunk support for assistance.

Once you have found content for a given `sourcetype`, you can import it into SSE or SES. To import content, you can use the following methods:
1. Splunk **Web**: To import content from Splunkbase, you can use the Splunk Web UI.
2. Splunk **CLI**: To import content from the command line, you can use the Splunk CLI.

Once you have imported content for a given `sourcetype`, you can use it to search, analyze, and visualize the data from that `sourcetype`.
Here are some specific examples of how SSE and SES can be used to assess data sources:
1. Identifying high-risk events: You can use SSE and SES to identify high-risk events by searching for events that match certain criteria, such as events that are generated from critical systems or events that contain known indicators of compromise (IOCs).
2. Analyzing trends: You can use SSE and SES to analyze trends in security data to identify potential threats. For example, you can track the number of login failures over time or the number of alerts that are generated from a particular system.
3. Investigating security incidents: You can use SSE and SES to investigate security incidents by searching for events that are related to the incident. For example, you can search for events that occurred around the time of the incident or events that were generated from the system that was affected by the incident.

# 4.1 Continuous monitoring

**Continuous monitoring** is the ongoing process of collecting, analyzing, and visualizing security data to identify threats and incidents. It is an essential part of any cybersecurity program, as it allows organizations to detect and respond to threats quickly and effectively.

Splunk Enterprise Security (SES) provides a variety of tools for continuous monitoring, including:
1. Data ingestion: can ingest data from a variety of sources, including security logs, network traffic, and threat intelligence feeds.
2. Data indexing: indexes the data that it ingests to make it searchable and analyzable.
3. Data analysis: provides a variety of tools for analyzing security data, such as search, reporting, and dashboards.
4. Incident response: provides a variety of features for responding to security incidents, such as alerting, case management, and integrations with other security tools.

To implement continuous monitoring, organizations can use SES to collect data from all of their security systems and networks. The data can then be indexed and analyzed to identify suspicious activity. SES can also be used to generate alerts and notifications for security analysts.

## Five basic stages of investigation according to Splunk
1. **Triage**: The triage stage involves assessing the severity of the potential incident and determining whether it requires further investigation.
2. **Containment**: The containment stage involves taking steps to prevent the incident from spreading or causing further damage.
3. **Eradication**: The eradication stage involves removing the malware or other threat that caused the incident.
4. **Recovery**: The recovery stage involves restoring the systems and data that were affected by the incident.
5. **Lessons learned**: The lessons learned stage involves documenting the incident and identifying ways to improve security posture to prevent similar incidents from happening in the future.

SES can be used to support all five stages of investigation. For example, SES can be used to search for events that are related to the incident, to generate reports on the incident, and to track the progress of the investigation.

Here are some specific examples of how Splunk ES can be used to support continuous monitoring and incident investigation:
* Continuous monitoring:
    * can be used to create dashboards that monitor key security metrics, such as the number of login failures, the number of alerts that are generated from each system, and the number of high-risk events that are identified.
    * can also be used to generate alerts when suspicious activity is detected. For example, SES can be configured to generate an alert when a user logs in from an unusual location or when there is a sudden increase in traffic from a particular IP address.
* Incident investigation:
    * can be used to search for events that are related to the incident. For example, SES can be used to search for events that occurred around the time of the incident or events that were generated from the system that was affected by the incident.
    * can also be used to generate reports on the incident. For example, SES can be used to generate a report that lists all of the events that are related to the incident, as well as a report that summarizes the impact of the incident.

# 4.2 Analyst performance metrics

Analyst performance metrics are used to measure the effectiveness and efficiency of cybersecurity analysts. These metrics can be used to identify areas where analysts need to improve and to track the overall performance of the security team over time.

Some common analyst performance metrics include:
* Mean time to respond (**MTTR**): MTTR is the average time it takes an analyst to respond to an incident. A lower MTTR indicates that analysts are responding to incidents more quickly. Threat topology visualization reduces MTTR.
* **Dwell time**: Dwell time is the average time it takes an analyst to investigate and resolve an incident. A lower dwell time indicates that analysts are resolving incidents more quickly.
* **Number of incidents** resolved: This metric measures the total number of incidents that an analyst resolves in a given period of time. A higher number of incidents resolved indicates that the analyst is productive and effective.
* **Quality of incident resolution**: This metric measures the quality of the analyst's work in resolving incidents. For example, this metric may measure the percentage of incidents that are resolved correctly and the percentage of incidents that are resolved within a certain timeframe.

In addition to these general metrics, there are also a number of more specific metrics that can be used to measure analyst performance. For example, organizations may track the following metrics:
* Number of **false positives**: This metric measures the number of times that an analyst incorrectly identifies a non-malicious event as malicious. A lower number of false positives indicates that the analyst is more accurate in identifying threats.
* Number of **false negatives**: This metric measures the number of times that an analyst incorrectly identifies a malicious event as non-malicious. A lower number of false negatives indicates that the analyst is better at detecting threats.
* Time to detect new threats: This metric measures the average time it takes an analyst to detect a new threat. A lower time to detect new threats indicates that the analyst is more effective at staying ahead of the threat landscape.

It is important to note that there is no one-size-fits-all approach to measuring analyst performance. The specific metrics that are used will vary depending on the organization's specific needs and goals. However, the metrics listed above are a good starting point for organizations that are looking to track and improve analyst performance.

## Benefits of using analyst performance metrics

There are a number of benefits to using analyst performance metrics. These benefits include:
* Identifying areas for improvement: Analyst performance metrics can be used to identify areas where analysts need to improve. For example, if an analyst has a high MTTR, it may indicate that they need more training on how to respond to incidents quickly.
* Tracking overall performance: Analyst performance metrics can be used to track the overall performance of the security team over time. This information can be used to identify trends and to make sure that the team is meeting its goals.
* Improving communication: Analyst performance metrics can be used to improve communication between analysts and management. For example, analysts can use metrics to demonstrate their value to management and to advocate for additional resources or training.

# 4.3 Common event dispositions

* Closed: The event has been investigated and resolved.
* Duplicate: The event is a duplicate of another event.
* False positive: The event was incorrectly identified as malicious.
* False negative: The event was incorrectly identified as non-malicious.
* Low priority: The event is not a high priority and can be investigated later.
* Medium priority: The event is a medium priority and should be investigated soon.
* High priority: The event is a high priority and should be investigated immediately.

To assign an event disposition, you should consider the following factors:

* The severity of the event
* The impact of the event
* The likelihood of the event happening again
* The resources that are available to investigate the event

Here are some examples of how to assign event dispositions to common events:

1.  Event: A user logs in from an unusual location.

    Severity: **Medium**

    Impact: **Low**

    Likelihood of happening again: **Medium**

    Resources available: **High**

    Event disposition: **Medium** priority

2. Event: A user's account is compromised.

    Severity: **High**

    Impact: **High**

    Likelihood of happening again: **High**

    Resources available: **High**

    Event disposition: **High** priority

3. Event: A false positive alert is generated.

    Severity: **Low**

    Impact: **Low**

    Likelihood of happening again: **Low**

    Resources available: **Medium**

    Event disposition: **Low** priority

# 4.4 Splunk Enterprise Security terms and aspects

1. **SPL** (Search Processing Language) = powerful language that can be used to search, analyze, and visualize data in Splunk Enterprise Security. SPL searches can be used to identify suspicious activity, investigate security incidents, and generate reports.
2. **Notable Event** = security event that has been identified as being potentially significant. Notable events can be generated by Splunk Enterprise Security rules, or they can be manually created by analysts.
3. **Risk Notable** = notable event that has been assessed as posing a risk to the organization. Risk notables are generated by Splunk Enterprise Security's risk engine, which takes into account a variety of factors, such as the severity of the event, the impact of the event, and the likelihood of the event happening again.
4. **Adaptive Response Action** = predefined action that Splunk Enterprise Security can take when a notable event is generated. Adaptive response actions can be used to block suspicious traffic, quarantine infected hosts, or notify analysts of potential incidents.
5. **Risk Object** = representation of a risk to the organization. Risk objects can be created by Splunk Enterprise Security's risk engine, or they can be manually created by analysts. Risk objects are used to track and assess risks over time.
6. **Contributing Events** = related to a notable event, SES can automatically identify contributing events, or analysts can manually link events together.

## Uses of Splunk Enterprise Security terms and aspects

* SPL: SPL can be used to search, analyze, and visualize data in Splunk Enterprise Security to identify suspicious activity, investigate security incidents, and generate reports.
* Notable Event: Notable events can be used to identify and prioritize security events for investigation.
* Risk Notable: Risk notables can be used to identify and respond to security events that pose a risk to the organization.
* Adaptive Response Action: Adaptive response actions can be used to automate the response to security incidents, which can help to reduce the time it takes to contain and eradicate threats.
* Risk Object: Risk objects can be used to track and assess risks over time, which can help organizations to make informed decisions about how to allocate resources and mitigate risks.
* Contributing Events: Contributing events can be used to understand the context of a security event and to identify the root cause of the event.

Here are some examples of how Splunk Enterprise Security terms and aspects can be used in security operations:
* SPL: A security analyst can use SPL to create a search that identifies all failed login attempts from a specific IP address. This search can be used to identify potential attackers who are trying to brute-force their way into the organization's systems.
* Notable Event: Splunk Enterprise Security can generate a notable event when a user logs in from an unusual location. This notable event can be used to alert an analyst to the potential for a security incident.
* Risk Notable: Splunk Enterprise Security can generate a risk notable when a user's account is compromised. This risk notable can be used to prioritize the investigation of the incident and to take steps to protect the organization from further damage.
* Adaptive Response Action: Splunk Enterprise Security can be configured to automatically block suspicious traffic from a specific IP address. This adaptive response action can help to prevent attackers from exploiting vulnerabilities in the organization's systems.
* Risk Object: A security analyst can create a risk object to represent the risk of a data breach. The risk object can be used to track the likelihood of a data breach occurring and the potential impact of a data breach.
* Contributing Events: A security analyst can review the contributing events to a notable event to understand the context of the event and to identify the root cause of the event. For example, if a security analyst is investigating a notable event that represents a failed login attempt, they can review the contributing events to see if there were any other failed login attempts from the same IP address.

# 4.5 Common built-in dashboards in Splunk Enterprise Security and the basic information they contain

* Splunk Enterprise Security (SES) includes a number of built-in dashboards that can be used to monitor and analyze security data. These dashboards provide a variety of information on different security topics, such as threats, incidents, and assets.
* The built-in dashboards in Splunk Enterprise Security provide security analysts with a valuable tool for monitoring, analyzing, and responding to security threats. By using these dashboards, security analysts can improve the organization's security posture and reduce the risk of security incidents.

Here are some of the most common built-in dashboards in SES and the basic information they contain:
1. Basic Security = This dashboard provides a high-level overview of the organization's security posture, including the number of events, alerts, and incidents.
2. Threats = This dashboard provides information on known threats, such as vulnerabilities, malware, and attack vectors.
3. Incidents = This dashboard provides information on open and closed incidents, including the severity, impact, and status of each incident.
4. Assets = This dashboard provides information on the organization's assets, such as hosts, users, and applications.
5. Compliance = This dashboard provides information on the organization's compliance with various security standards and regulations.
6. Risk = This dashboard provides information on the organization's security risks, including the likelihood and impact of each risk.
7. Investigations = This dashboard provides tools for investigating security incidents, such as the ability to search for events, correlate events, and view event details.

## Usage for security analysts
Security analysts can use the built-in dashboards in SES to:
* Get a high-level overview of the organization's security posture
* Identify any areas of concern
* Stay up-to-date on the latest threats
* Identify any threats that may be targeting the organization
* Track the status of open incidents
* Prioritize the investigation of incidents
* Understand the organization's attack surface
* Identify any assets that may be at risk
* Ensure that the organization is compliant with various security standards and regulations
* Identify and assess the organization's security risks
* Investigate security incidents
* Identify the root cause of incidents

# 4.6 Risk-based alerting (RBA) and Risk Framework

## RBA

RBA is a security monitoring approach that prioritizes alerts based on the risk they pose to the organization. This is in contrast to traditional alerting approaches, which typically prioritize alerts based on their severity.

RBA works by using a **risk framework** to assign a **risk score** to each event. The risk score is calculated based on a variety of factors, such as the severity of the event, the impact of the event, and the likelihood of the event happening again.

Once each event has been assigned a risk score, RBA can be used to generate alerts for events that meet a certain risk threshold. This helps to ensure that security analysts focus their attention on the most important alerts.

In a nutshell:
* discrete data => connected behavior
* disconnected sec events => contextualized sec stories
* notable events = related to risk scenarios
* only risky scenarios create risk events (only if group of RE exceeds threshold)
* generic threat intelligence (TI) => custom TI
* operationalize MITRE, CIS20, Killchain, NIST ...
* reduces alert volumes
* applying observations to risk index
* risk event timeline
* correlation search adds MITRE tag, risk score to objects and that's how it works under the hood
* risk factor then multiplies by X factor (e.g. `1.5x` score)

## Risk framework

* A risk framework is a set of rules and processes that organizations use to identify, assess, and manage their security risks. Common risk frameworks include NIST Cybersecurity Framework (CSF) and ISO/IEC 27005.
* When choosing a risk framework, organizations should consider their specific needs and goals. For example, an organization may want to choose a risk framework that is aligned with its industry or compliance requirements.

## Creating correlation searches in Enterprise Security

Correlation searches in SES are used to identify patterns and relationships between events. This can be useful for identifying suspicious activity and investigating security incidents.

To create a correlation search, you need to define a set of criteria that the events must meet. For example, you may want to create a correlation search that identifies all events that are generated from a specific IP address or that contain a specific keyword.

Once you have defined the criteria for the correlation search, you need to save it and run it. Enterprise Security will then search for events that meet the criteria and generate a list of results.

**Here are some examples of how RBA and correlation searches can be used in Enterprise Security:**

* use RBA to generate alerts for events that are associated with known threats.
* use RBA to generate alerts for events that occur on sensitive systems or data.
* use correlation searches to identify patterns of suspicious activity, such as a large number of failed login attempts from a single IP address.
* use correlation searches to investigate security incidents, such as a data breach.

Overall, RBA and correlation searches are powerful tools that can help security analysts to more effectively detect, investigate, and respond to security threats.

## Advanced Alerting

Most common actions:
* Log event
* Output result to lookup (e.g. `| inputlookup <INPUT>`)
* Send email

Other actions:
* Add to triggered alerts
* Output results to telemetry endpoint
* Send to Splunk mobile
* Use a webhook
* Custom alert actions - scripts

# 5.1 Common SPL terms and how they can be used in security analysis

* **TSTATS** = used to perform statistical analysis on data. It can be used to calculate metrics such as the count, average, and standard deviation of a field.
* **TRANSACTION** = used to group related events together. This can be useful for identifying suspicious activity, such as a series of events that lead up to a security incident.
* **HEAD/TAIL** = used to return the first or last event in a group of events. This can be useful for identifying the start and end times of a security incident.
* **REX** = used to extract regular expressions from event data. This can be useful for identifying malicious patterns in data, such as malicious URLs or email addresses.
* **EVAL** = used to evaluate expressions in SPL. This can be used to perform calculations, convert data types, and create new fields.
* **FOREACH** = used to iterate over a set of data and perform an action on each item in the set. This can be useful for performing complex analysis on data, such as identifying all of the hosts that have been infected with a particular malware.
* **LOOKUP** = used to look up values in a lookup table. This can be useful for enriching event data with additional information, such as the asset information for a host or the user information for a user.
* **MAKERESULTS** = used to create a new result set from scratch. This can be useful for creating custom dashboards and reports.

Examples of how these SPL terms can be used in security analysis:
* **TSTATS:**
    * Calculate the number of failed login attempts per hour to identify unusual activity.
    * Calculate the average response time for security alerts to identify any areas for improvement.
    * Calculate the standard deviation of the number of events generated by each host to identify any anomalous hosts.
* **TRANSACTION:**
    * Identify all of the events that are associated with a particular security incident.
    * Identify all of the events that are generated from a particular IP address or host.
    * Identify all of the events that contain a particular keyword or phrase.
* **HEAD/TAIL:**
    * Identify the first and last events in a series of events that lead up to a security incident.
    * Identify the first and last events that are generated from a particular IP address or host.
    * Identify the first and last events that contain a particular keyword or phrase.
* **REX:**
    * Extract malicious URLs from event data to identify phishing attacks.
    * Extract malicious email addresses from event data to identify spam campaigns.
    * Extract malicious patterns from network traffic data to identify malware infections.
* **EVAL:**
    * Calculate the severity of a security event based on its impact and likelihood.
    * Convert event timestamps from one format to another.
    * Create a new field that contains the asset information for the host that generated the event.
* **FOREACH:**
    * Identify all of the hosts that have been infected with a particular malware.
    * Identify all of the users who have logged in from an unusual location.
    * Identify all of the assets that are vulnerable to a particular exploit.
* **LOOKUP:**
    * Enrich event data with the asset information for the host that generated the event.
    * Enrich event data with the user information for the user who generated the event.
    * Enrich event data with the threat intelligence information for a particular IP address or domain name.
* **MAKERESULTS:**
    * Create a custom dashboard that shows the number of security alerts generated by each severity level.
    * Create a custom report that lists all of the hosts that have been infected with malware in the past week.
    * Create a custom search that identifies all of the events that are associated with a particular security incident.

Other common search commands

| Command | Description |
|---------|-------------|
|`chart`/`timechart` | Returns results in a tabular output for (time-series) charting.|
|`dedup` | Removes subsequent results that match a specified criterion.|
|`eval` | Calculates an expression.|
|`fields` | Removes fields from search results.|
|`head`/`tail` | Returns the first/last N results.|
|`lookup` | Adds field values from an external source.|
|`rename` |Renames a field. Use wildcards to specify multiple fields.|
|`rex` | Specifies regular expression named groups to extract fields.|
|`search` |Filters results to those that match the search expression.|
|`sort` | Sorts the search results by the specified fields.|
|`stats` | Provides statistics, grouped optionally by fields.|
|`mstats` | Similar to stats but used on metrics instead of events.|
|`table` | Specifies fields to keep in the result set. Retains data in tabular format.|
|`top`/`rare`|Displays the most/least common values of a field.|
|`transaction`|Groups search results into transactions.|
|`where`|Filters search results using eval expressions. Used to compare two different fields.|

## Workshop bots.splunk.com

```
# All workshop data is in this index
index=botsv1 imreallynotbatman.com

# There is always implicit AND - above is the same as:
index=botsv1 AND imreallynotbatman.com

# Fast/Smart/Verbose modes
index=botsv1 imreallynotbatman.com
| stats count by src

# AND, OR, NOT operators
index=botsv1 imreallynotbatman.com (sourcetype="stream:http" OR sourcetype="suricata")
index=botsv1 imreallynotbatman.com NOT (sourcetype="fgt_utm" OR sourcetype="iis")

# Look at `dest_ip`
index=botsv1 imreallynotbatman.com sourcetype=suricata

# User agent `cs_User_Agent`
index=botsv1 imreallynotbatman.com sourectype=iis

# See what values are most frequently seen
index=botsv1 imreallynotbatman.com src=40.80.148.42
| stats count by url
| sort 10 - count

# Wildcards - return all events with any `form_data` to see brute force attempts
index=botsv1 sourcetype=stream:http dest=192.168.250.70 http_method=POST form_data=*
| table form_data

index=botsv1 sourcetype=stream:http dest=192.168.250.70 http_method=POST form_data=*username*passwd*

index=botsv1 sourcetype=stream:http dest=192.168.250.70 http_method=POST form_data=*username*passwd*
| table form_data src

index=botsv1 sourcetype=stream:http dest=192.168.250.70 http_method=POST form_data=*username*passwd*
| table form_data src
| stats count by src

# How many events had a http_user_agent with both OSX and Chrome?
index=botsv1 sourcetype=stream:http http_user_agent=*OS*X*chrome* | table _time src http_user_agent

# How many URLS do NOT reference Joomla?
index=botsv1 sourcetype=stream:http NOT url=*joomla*
| stats count by url
| sort - count

# What was the first brute force password? Oldest results on top:
index=botsv1 sourcetype=stream:http dest=192.168.250.70 http_method=POST form_data=*username*passwd*
| reverse
| table _time form_data

# Extracting string with rex
index=botsv1 sourcetype=stream:http dest=192.168.250.70 http_method=POST form_data=*username*passwd*
| rex field=form_data "passwd=(?<userpassword>\w+)"
| table form_data userpassword

# Return the first or last...
index=botsv1 sourcetype=stream:http dest=192.168.250.70 http_method=POST form_data=*username*passwd*
| rex field=form_data "passwd=(?<userpassword>\w+)"
| reverse
| head 1
| table form_data userpassword
# ...Same as
index=botsv1 sourcetype=stream:http dest=192.168.250.70 http_method=POST form_data=*username*passwd*
| rex field=form_data "passwd=(?<userpassword>\w+)"
| tail 1
| table form_data userpassword

# Comparing events against lists (e.g. compare IP addresses, hashes to threat intel lists of indicators)
index=botsv1 sourcetype=stream:http dest=192.168.250.70 http_method=POST form_data=*username*passwd*
| rex field=form_data "passwd=(?<userpassword>\w+)"
| table form_data userpassword
| eval lenpword=len(userpassword)
| table form_data userpassword lenpword
# ...Narrow results to only 6 length
index=botsv1 sourcetype=stream:http dest=192.168.250.70 http_method=POST form_data=*username*passwd*
| rex field=form_data "passwd=(?<userpassword>\w+)"
| eval lenpword=len(userpassword)
| search lenpword=6
| table userpassword
# List Coldplay songs
| inputlookup "cp.csv"
| eval song=lower(Songs)
| fields song
# Link events (see what passwords match the Coldplay songs)
index=botsv1 sourcetype=stream:http dest=192.168.250.70 http_method=POST form_data=*username*passwd*
| rex field=form_data "passwd=(?<userpassword>\w+)"
| eval lenpword=len(userpassword)
| search lenpword=6
| eval password=lower(userpassword)
| lookup coldplay.csv song AS password OUTPUTNEW song
| search song=*
| table song password

# Were passwords in the attack used more than once?
index=botsv1 sourcetype=stream:http dest=192.168.250.70 http_method=POST form_data=*username*passwd*
| rex field=form_data "passwd=(?<userpassword>\w+)"
| stats count values(src) AS src by userpassword
| sort - count

# Calculating average passwd length, rounded to the nearest int
index=botsv1 sourcetype=stream:http dest=192.168.250.70 http_method=POST form_data=*username*passwd*
| rex field=form_data "passwd=(?<userpassword>\w+)"
| eval lenpword=len(userpassword)
| stats avg(lenpword) AS avgPassword
| eval average_password_length=round(avgPassword,0)

# Line chart of the frequency of password attempts, also try `span=1m`
index=botsv1 sourcetype=stream:http dest=192.168.250.70 http_method=POST form_data=*username*passwd*
| rex field=form_data "passwd=(?<userpassword>\w+)"
| timechart span=1s fixedrange=false count by dest

# How many seconds between first batman password and the same password again?
index=botsv1 sourcetype=stream:http dest=192.168.250.70 http_method=POST form_data=*username*passwd*
| rex field=form_data "passwd=(?<userpassword>\w+)"
| search userpassword=batman
| table _time userpassword src
# ...Let Splunk calculate with Transaction based on common fields
index=botsv1 sourcetype=stream:http dest=192.168.250.70 http_method=POST form_data=*username*passwd*
| rex field=form_data "passwd=(?<userpassword>\w+)"
| search userpassword=batman
| transaction userpassword
| eval round_duration=round(duration,2)
| table userpassword duration round_duration

# Use iplocation to understand geo of src
index=botsv1 sourcetype=stream:http dest=192.168.250.70
| stats count by src
| iplocation src
| table src count City Region Country lat lon
# ...Overlay sources on the map (Visualization - Cluster map)
index=botsv1 sourcetype=stream:http dest=192.168.250.70
| iplocation src
| geostats latfield=lat longfield=lon count by src

# Challenge #1 - how many characters were in the longest command executed
index=botsv1 sourcetype="xmlwineventlog:microsoft-windows-sysmon/operational" CommandLine=*
| eval lenCmdLine=len(CommandLine)
| table CommandLine lenCmdLine
| sort - lenCmdLine
| head 1

# Challenge #2 - show the top 5 events code for the server as pie chart
index=botsv1 sourcetype="wineventlog:security" src=we8105desk.waynecorpinc.local
| stats count by EventCode
| sort - count
| head 5

# Challenge #3 - generate a list of sites visited and provide the sum bytes in/out, do not include Microsoft
index=botsv1 sourcetype=stream:http site!="ssw.live.com" site!=192.168.* site!=*.msn.com* site!=*.windowsupdate.com site!=*.microsoft.com
| stats sum(bytes_in) AS sum_bytes_in sum(bytes_out) AS sum_bytes_out values(url) AS url values(src) AS src by site dest
| sort - sum_bytes_out
```

# 5.2 Splunk best practices for composing efficient searches

* **Use indexes wisely:** Indexes are what make Splunk searches so fast, so it's important to use them effectively. When creating a search, make sure to index the fields that you will be searching on. You can also use wildcards to index multiple fields at once.
* **Use filters to narrow down your search:** Filters are a great way to narrow down your search results and improve performance. When creating a filter, make sure to use the appropriate logical operators (AND/OR/NOT).
* **Use subsearches to organize your search:** Subsearches are a great way to organize your search and make it more readable. Subsearches can also be used to improve performance by breaking down your search into smaller, more efficient pieces.
* **Use the EVAL command to perform calculations:** perform calculations on your search results. This can be useful for creating custom metrics or enriching your data with additional information.
* **Use the STATS command to summarize your search results:** summarize your search results and identify trends. This can be useful for creating reports or dashboards.
* **Use the SORT command to sort your search results:** sort your search results in a specific order. This can be useful for creating reports or dashboards.
* **Use the LIMIT command to limit your search results:** limit the number of results that are returned by your search. This can be useful for improving performance or for creating reports that only display a certain number of results.
* **Use the FIELDS command to select the fields that are returned by your search:** select the fields that are returned by your search. This can be useful for improving performance or for creating reports that only display specific fields.
* **Use the TIMECHART command to visualize your search results:** visualize your search results over time. This can be useful for identifying trends and patterns.
* **Use the TABLE command to display your search results in a table:** display your search results in a table. This can be useful for creating reports or dashboards.

Here are some additional tips for composing efficient Splunk searches:

* **Use the search assistant:** The search assistant is a built-in tool that can help you to create efficient searches. The search assistant can suggest fields, values, and operators that you can use in your search.
* **Use the Splunk documentation:** The Splunk documentation is a valuable resource for learning about Splunk and how to use it effectively. The Splunk documentation includes information on all of the Splunk commands and functions.
* **Test your searches:** Before you run a search in production, it's important to test it thoroughly. This will help to ensure that your search is efficient and that it returns the results that you expect.

## Troubleshooting

Search heads might slow down or crash if you exceed the storage capacity for KV Stores. Based on Splunk service limits, each KV Store can have a maximum size of **25 GB**, and an entire KV Store collection can have a maximum size of **100 GB**. See Service limits and constraints. Following are some tips to avoid exceeding the size limitations for KV Store collections:
* Do not write an excessive amount of data to your KV Store collections using scheduled saved searches.
* Do not retain an excessive amount of old data in your KV Store collections.

Follow these steps if your search heads crash or slow down due to large KV Store collections:
* Identify KV Store collections that exceed size limitations
* Identify data sources that write to KV Store collections
* Deactivate / Turn off saved searches that use specific KV Store collections
* Delete data in a KV Store collection
* Manage large KV Stores such as Access Tracker
* Add a retention policy to KV Store collections
* Increase storage space for KV Store collections
* Customize growth monitoring for KV Store collections

# 5.3 Identify SPL resources included within ES, Splunk Security Essentials, and Splunk Lantern

## Splunk Enterprise Security (ES)

* **SPL documentation:** The ES documentation includes a comprehensive guide to SPL, as well as information on SPL commands, functions, and syntax.
* **SPL examples:** The ES documentation also includes a number of SPL examples that can be used as a starting point for creating your own searches.
* **SPL tutorials:** The ES documentation also includes a number of SPL tutorials that can teach you how to use SPL to perform common tasks, such as searching for events, analyzing data, and generating reports.

## Splunk Security Essentials (SSE)

* **SSE content library:** The SSE content library includes a number of SPL searches that can be used to detect and investigate security threats. These searches are based on best practices and are designed to be efficient and effective.
* **SSE documentation:** The SSE documentation includes information on how to use the SSE content library and how to create your own custom searches.

## Splunk Lantern

(Splunk Lantern is a new self-help adoption resource hub with a growing library of the security, observability, and IT use cases.)

* **Lantern documentation:** The Lantern documentation includes a number of resources on SPL, including a guide to SPL commands, functions, and syntax.
* **Lantern tutorials:** The Lantern documentation also includes a number of SPL tutorials that can teach you how to use SPL to perform common tasks, such as searching for events, analyzing data, and generating reports.

In addition to these resources, there are a number of other SPL resources available online and in libraries. However, the resources listed above are a good starting point for cybersecurity defense analysts who want to learn more about SPL and how to use it to improve their security operations.

**Here are some tips for using SPL resources effectively:**

* **Start with the basics:** Before you start using SPL to perform complex tasks, it's important to have a strong understanding of the basics of SPL. This includes understanding SPL commands, functions, and syntax.
* **Use the documentation:** The documentation for ES, SSE, and Lantern provides a wealth of information on SPL. Be sure to consult the documentation when you need help with a particular SPL task.
* **Use the examples and tutorials:** The examples and tutorials that are included in the ES, SSE, and Lantern documentation are a great way to learn how to use SPL to perform common tasks.
* **Test your searches:** Before you run a search in production, it's important to test it thoroughly. This will help to ensure that your search is efficient and that it returns the results that you expect.

# 6.1 Threat Hunting Techniques

Threat hunting is the proactive search for threats and vulnerabilities in an organization's IT infrastructure. It is a critical component of any cybersecurity program, as it can help to identify and remediate threats before they cause damage.

There are a number of different threat hunting techniques that analysts can use, including:
1. **Configuration:** This technique involves examining the configuration of systems and networks to identify potential vulnerabilities. This can be done manually or using automated tools.
2. **Modeling (anomalies):** This technique involves creating models of normal behavior for systems and networks. Analysts can then use these models to identify anomalous behavior, which could be indicative of a threat.
3. **Indicators:** This technique involves using indicators of compromise (IOCs) and indicators of attack (IOAs) to identify threats. IOCs are artifacts that are associated with known threats, while IOAs are behaviors that are associated with known attacks.
4. **Behavioral analytics:** This technique involves using machine learning and artificial intelligence to identify suspicious behavior. For example, analysts can use behavioral analytics to identify anomalous login patterns or network traffic patterns.

Analysts can combine these techniques to create a comprehensive threat hunting strategy. For example, an analyst might start by examining the configuration of a system to identify potential vulnerabilities. The analyst could then create a model of normal behavior for the system and use it to identify anomalous behavior. Finally, the analyst could use IOCs and IOAs to identify known threats.

## Examples of how threat hunting techniques can be used
* A threat hunter might use configuration to identify all systems that are running a specific version of a software application. The threat hunter could then research known vulnerabilities in that version of the software application and look for signs that those vulnerabilities have been exploited.
* A threat hunter might use modeling to create a model of normal network traffic patterns. The threat hunter could then use this model to identify anomalous traffic patterns, which could be indicative of a threat.
* A threat hunter might use indicators to look for known IOCs and IOAs on a system. If the threat hunter finds any known IOCs or IOAs, this could be indicative that the system has been compromised.
* A threat hunter might use behavioral analytics to identify anomalous login patterns. For example, the threat hunter might look for accounts that have been accessed from unusual locations or at unusual times.

Threat hunting is a complex and challenging task, but it is essential for protecting organizations from cyber threats. By using the techniques described above, threat hunters can identify and remediate threats before they cause damage.

Was threat hunting successful, if the hypothesis was not proven?

* If the hypothesis is **incorrect**: A disproved hypothesis is the desired outcome because it means that the potential intrusion or vulnerability does not exist. After disproving a hypothesis, a threat hunter should document the investigation for future reference and move on to a new hunt.
* If the hypothesis is **correct**: A proven hypothesis means that the threat hunter has discovered a previously unknown threat. In addition to documenting the investigation, the hunter should also take steps to remediate the issue, such as initiating incident response activities, patching a discovered vulnerability, and improving defenses for the future.
* If **no decision** can be made: After collecting and analyzing data, the threat hunter may be unable to conclusively prove or disprove their hypothesis. This means that the threat hunter should work to set up collection for any missing required data or find a way to refine their hypothesis to a more testable and provable form.

# 6.2 Define long tail analysis, outlier detection, and some common steps of hypothesis hunting with Splunk

## Long tail analysis

Long tail analysis is a technique that can be used to identify rare or unusual events in data. It works by grouping data into categories and then analyzing the distribution of data within each category. Long tail analysis can be used to identify events that are statistically significant, even if they occur infrequently. This can be useful for identifying malicious activity or other anomalies in data.

**Here are some examples of how long tail analysis can be used in Splunk:**

* **Identifying malicious login attempts:** An analyst could use long tail analysis to identify all failed login attempts from unusual locations. This could help to identify potential attackers who are trying to brute-force their way into the organization's systems.
* **Identifying malware infections:** An analyst could use long tail analysis to identify all processes that are running with unusual permissions. This could help to identify malware infections that are trying to evade detection.
* **Identifying network intrusions:** An analyst could use long tail analysis to identify all network traffic that is coming from unusual IP addresses. This could help to identify network intrusions that are trying to gain access to the organization's network.

## Outlier detection

Outlier detection is a technique that can be used to identify events that are significantly different from the norm. This can be useful for identifying anomalous activity that could be indicative of a threat.

**Here are some examples of how outlier detection can be used in Splunk:**

* **Identifying anomalous login attempts:** An analyst could use outlier detection to identify all login attempts that are significantly different from the norm in terms of the time of day, the location of the login attempt, or the account that is being logged into.
* **Identifying anomalous network traffic:** An analyst could use outlier detection to identify all network traffic that is significantly different from the norm in terms of the source IP address, the destination IP address, the port number, or the amount of data being transferred.
* **Identifying anomalous system events:** An analyst could use outlier detection to identify all system events that are significantly different from the norm in terms of the type of event, the source of the event, or the severity of the event.

## Hypothesis hunting with Splunk

Hypothesis hunting is a process of using data and analytics to identify and investigate security threats. It is a proactive approach to security that can help to identify threats before they cause damage.

**Here are some common steps of hypothesis hunting with Splunk:**

1. **Develop a hypothesis:** The first step is to develop a hypothesis about the type of threat that you are looking for. This hypothesis could be based on your knowledge of the organization's environment, current security threats, or previous security incidents.
2. **Collect data:** Once you have a hypothesis, you need to collect data that is relevant to your hypothesis. This data could come from a variety of sources, such as log files, network traffic data, and system events.
3. **Analyze the data:** Once you have collected data, you need to analyze it to identify patterns and anomalies. This can be done using a variety of Splunk tools and commands.
4. **Investigate the findings:** If you find any patterns or anomalies in the data, you need to investigate them further to determine if they are indicative of a threat. This investigation may involve collecting additional data, performing further analysis, or consulting with other security analysts.
5. **Remediate the threat:** If you determine that the findings are indicative of a threat, you need to take steps to remediate the threat. This may involve blocking malicious IP addresses, patching vulnerabilities, or quarantining infected systems.

Hypothesis hunting can be a complex and challenging task, but it is an essential part of any cybersecurity program. By following the steps above, security analysts can use Splunk to identify and investigate security threats before they cause damage.

# 6.3 Determine when to use adaptive response actions and configure them as needed

## When to use adaptive response actions

Adaptive response actions are a powerful tool that can be used to automate the response to security incidents. They can be used to perform a variety of tasks, such as blocking malicious IP addresses, quarantining infected systems, and notifying security analysts of incidents.

Adaptive response actions should be used when:
* You need to automate the response to security incidents.
* You need to respond to security incidents quickly and efficiently.
* You need to reduce the workload on security analysts.
* You need to improve the consistency of the response to security incidents.

## Configuring adaptive response actions

To configure adaptive response actions, you need to:
1. Identify the security incidents that you want to respond to automatically.
2. Create adaptive response actions for each security incident.
3. Configure the adaptive response actions to perform the desired tasks.
4. Test the adaptive response actions to ensure that they are working as expected.

**Here are some examples of how adaptive response actions can be used:**
* You can use an adaptive response action to automatically block a malicious IP address when a security information and event management (SIEM) system detects a brute-force login attempt from that IP address.
* You can use an adaptive response action to automatically quarantine an infected system when a SIEM system detects that the system is running a known malware process.
* You can use an adaptive response action to automatically notify a security analyst when a SIEM system detects a high-severity security incident.

Adaptive response actions can be configured to perform a variety of tasks, such as:
* Blocking IP addresses
* Quarantining systems
* Isolating networks
* Shutting down systems
* Notifying security analysts
* Running scripts
* Calling webhooks

**Here are some tips for configuring adaptive response actions:**
* Be specific when defining the security incidents that you want to respond to automatically.
* Test the adaptive response actions thoroughly before deploying them in production.
* Monitor the adaptive response actions to ensure that they are working as expected.

# 6.4 SOAR playbooks

Security Orchestration, Automation, and Response (SOAR) playbooks are reusable workflows that automate security tasks. They can be used to automate a wide range of tasks, from incident response to vulnerability management.

They are provided by Splunk and cannot be modified. Use update to have latest from source control.

SOAR playbooks are typically written in a scripting language, such as Python or JavaScript. They can be triggered by a variety of events, such as security alerts, new vulnerabilities, or manual user input.

Once triggered, a SOAR playbook will execute a series of steps, such as:

* Gathering information about the event
* Analyzing the information to determine the appropriate response
* Taking action, such as blocking an IP address, quarantining a system, or notifying a security analyst
* Can also notify and call other playbooks
* Playbooks may require SOAR Apps & Assets in your Splunk
* Playbook may run inside a **workbook**
* They can perform any task, but usually:
  1. Gather & Enhance information in an event.
  2. Make decisions or further actions based on the information.
  3. Perform actions outside of SOAR to respond to the event.
  4. Update the event with results of actions and change the event status.

## SOAR playbooks in Enterprise Security

Enterprise Security includes a number of built-in SOAR playbooks that can be used to automate common security tasks. Organizations can also create their own custom SOAR playbooks to meet their specific needs.

SOAR playbooks can be triggered (can automatically respond to new events) from Enterprise Security in a number of ways:
* **Security alerts:** SOAR playbooks can be triggered by security alerts that are generated by Enterprise Security.
* **New vulnerabilities:** SOAR playbooks can be triggered by new vulnerabilities that are discovered by Enterprise Security.
* **Manual user input:** SOAR playbooks can be triggered manually by a security analyst.

## Benefits of using SOAR playbooks

SOAR playbooks offer a number of benefits:
* **Increased efficiency:** SOAR playbooks can automate repetitive tasks, freeing up security analysts to focus on more complex tasks.
* **Improved consistency:** SOAR playbooks can help to ensure that security tasks are performed consistently, regardless of the individual security analyst who is handling the task.
* **Reduced errors:** SOAR playbooks can help to reduce human errors.
* **Faster response times:** SOAR playbooks can help to automate the response to security incidents, which can lead to faster response times and reduced damage.

# Additional Links:

- https://www.splunk.com/pdfs/solution-guides/splunk-quick-reference-guide.pdf
- https://www.splunk.com/content/dam/splunk2/en_us/pdfs/gated/ebooks/the-essential-guide-to-security.pdf
- https://docs.splunk.com/Splexicon
- https://docs.splunk.com/Documentation/Splunk/latest/SearchReference/WhatsInThisManual
- https://www.splunk.com/blog/2017/07/06/hunting-with-splunk-the-basics.html
- https://www.splunk.com/blog/2017/04/25/mapping-with-splunk.html
- https://bots.splunk.com/
- [About risk-based alerting in Splunk Enterprise Security](https://docs.splunk.com/Documentation/ES/latest/RBA/Overview)
- [Troubleshoot performance issues due to large KV Store collections](https://docs.splunk.com/Documentation/ES/latest/Admin/TroubleshootperformancelargeKVStore)

---

Updated: Thu 28 Sep 2023 01:44:31 UTC (`v0.3b`)