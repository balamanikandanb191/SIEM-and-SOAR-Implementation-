# **PART 1 – PROJECT OVERVIEW & ARCHITECTURE**

# **SOC Lab Implementation Documentation**

## **Splunk Enterprise + Splunk SOAR + VirusTotal + Automated Incident Response on Microsoft Azure**

Project documentation · Part 1 of 10 · Architecture and project foundation

# **1. Document Title**

**Project Name:** SOC Lab: Splunk Enterprise + Splunk SOAR + VirusTotal on Azure

**Document Title:** SOC Lab Implementation Documentation – Splunk Enterprise, Splunk SOAR, VirusTotal and Automated Incident Response on Microsoft Azure

**Project Category:** Security Operations Centre (SOC) Automation

**Cloud Platform:** Microsoft Azure

**SIEM Platform:** Splunk Enterprise

**SOAR Platform:** Splunk SOAR On-premises

**Threat Intelligence Platform:** VirusTotal

**Log Source:** Linux SSH authentication logs

**Automation Language:** Python within Splunk SOAR playbook

**Email Integration:** Splunk SOAR SMTP application with Gmail SMTP asset configuration in progress

## **1.1 Document scope**

This document records the implementation of a security monitoring and automated incident investigation laboratory deployed using Microsoft Azure infrastructure.

It covers the complete implementation journey from the initial cloud infrastructure and log collection setup through detection, security orchestration, threat intelligence enrichment, automated risk classification, severity updates and investigation note creation.

It also documents the current email integration stage, including the SMTP application installation and Gmail SMTP asset configuration.

The documentation distinguishes between:

- Successfully implemented and validated functionality.
- Earlier implementation errors and the troubleshooting performed.
- Configuration details that were not explicitly captured.
- The remaining email delivery activity.

The document uses the latest successful implementation state rather than treating earlier troubleshooting failures as the final project status.

## **1.2 Intended audience**

This documentation is intended for:

1. Azure Cloud Engineers responsible for deploying and maintaining the underlying infrastructure.
2. SOC Engineers implementing SIEM and SOAR integrations.
3. Security Analysts learning automated incident investigation.
4. Splunk Administrators managing log ingestion, searches and event forwarding.
5. Infrastructure Engineers responsible for Linux servers and network connectivity.
6. Technical interviewers and reviewers evaluating the implementation.
7. Engineers who need to reproduce or extend the laboratory.

## **1.3 Documentation accuracy and evidence standard**

The primary source for this document is the implementation history available in this conversation.

The following rules are applied:

- Actual VM names and IP addresses are retained where available.
- Actual Splunk versions and SOAR versions are retained.
- Confirmed SPL queries and Python logic are documented.
- Confirmed playbook results are treated as completed implementation evidence.
- Unavailable configuration values are explicitly identified.
- Passwords, tokens and API keys are not reproduced.
- General technical explanations are distinguished from configuration values confirmed in the laboratory.

A value described as *not explicitly available in the conversation* should not be interpreted as a missing implementation step. It means only that the exact value was not preserved in the available implementation record.

# **2. Document Purpose**

## **2.1 Why this document was created**

The purpose of this document is to provide a complete technical record of the SOC laboratory implementation.

The laboratory demonstrates how a security monitoring workflow can be automated using a combination of cloud infrastructure, SIEM, SOAR and external threat intelligence.

The implementation begins with failed SSH authentication attempts on a Linux virtual machine.

These authentication events are collected by Splunk Universal Forwarder and delivered to Splunk Enterprise. Splunk Enterprise searches the collected events to identify failed login attempts and forwards matching security events to Splunk SOAR.

Splunk SOAR receives the event, creates the corresponding investigation container and artifacts, and executes a Python-based playbook.

The playbook extracts the source IP address, queries VirusTotal, evaluates the returned threat intelligence information, classifies the IP address and updates the SOAR container severity.

The playbook also creates an automated investigation note containing the relevant evidence.

The SMTP application has been installed in SOAR. Gmail SMTP asset configuration is the current implementation stage. Successful email delivery has not yet been confirmed.

## **2.2 What the documentation explains**

| Area                    | Documentation coverage                                                 |
| ----------------------- | ---------------------------------------------------------------------- |
| Azure infrastructure    | Virtual machines, operating systems, networking and known IP addresses |
| Linux log source        | SSH authentication events and authentication log collection            |
| Universal Forwarder     | Forwarding Linux authentication logs to Splunk Enterprise              |
| Splunk Enterprise       | Indexing, search processing and detection                              |
| Detection engineering   | Failed SSH login SPL query                                             |
| Splunk-SOAR integration | Event forwarding and integration configuration                         |
| SOAR containers         | Creation and investigation context                                     |
| SOAR artifacts          | Source IP and related event fields                                     |
| VirusTotal              | External IP reputation lookup                                          |
| Playbook                | Python-based automation logic                                          |
| Risk classification     | HIGH, MEDIUM and LOW risk classification                               |
| Incident management     | Container severity update                                              |
| Investigation evidence  | Automated investigation note                                           |
| Email integration       | SMTP app and Gmail asset configuration stage                           |
| Troubleshooting         | Historical problems, corrections and successful validations            |

## **2.3 Expected outcome**

The intended end-to-end outcome is a SOC workflow in which a suspicious authentication event is detected, enriched with threat intelligence and presented to an analyst with a calculated risk classification and investigation evidence.

The email integration is intended to provide an additional notification channel for HIGH-risk events after the Gmail SMTP asset is configured and tested.

# **3. Project Overview**

## **3.1 Introduction**

Security monitoring is an important requirement for organisations operating Linux servers, cloud infrastructure and internet-facing workloads.

Servers exposed to SSH authentication attempts may receive repeated login failures from external IP addresses.

A failed SSH login does not automatically mean that a system has been compromised. However, repeated or suspicious authentication attempts can indicate:

- Password guessing.
- Brute-force activity.
- Attempts to access accounts using commonly targeted usernames.
- Automated internet scanning.
- Authentication activity originating from IP addresses with a known malicious reputation.

Manually reviewing every authentication event can be time-consuming.

This laboratory demonstrates how to automate important parts of the security investigation process.

It combines:

- Microsoft Azure for hosting the laboratory infrastructure.
- Linux for generating authentication events.
- Splunk Universal Forwarder for log collection and forwarding.
- Splunk Enterprise for event indexing and security detection.
- Splunk SOAR for security investigation automation.
- VirusTotal for external IP reputation enrichment.
- Python for playbook logic.
- SMTP for the intended email notification workflow.

## **3.2 Main technologies**

[Microsoft and SpaceX partner to use Azure with Starlink satellite | Shacknews](https://images.openai.com/static-rsc-4/kRwouIZ9H0EeJxOh0fJ9uOvUAb8NsmcbtqyJ6R9IZF760pnYMpRMXkr6kHxxBJQmCz7LQQZmuKj1VFandCre1cUbVBMInj2UbklQJ21SL5F2_Z2pDksIb8DE9rtGDfC4sbPiM_DpBtw9v6K8ov9GnU0wM8HucwFKE2YmsLhSDGE?purpose=inline)

**Microsoft Azure**

Cloud infrastructure hosting the laboratory virtual machines.

[What are Splunk Apps and Add-Ons ? | Splunk](https://images.openai.com/static-rsc-4/0Fbz1LxvhKKOVfnGiLxVoFuGzcIo8WiyH665mkYz4lqavatYWshqexGY_INyAIMw-s053ZDUwwWXWuuXvEILIvBoFjw16q-5jobvFhT6tum2e0_JdwpdF7KLrMx6uGAwINp6vLufqlrPQSm0FuVKjNLBxCQNLc7jq3tj-qSoJ9c?purpose=inline)

**Splunk Enterprise**

Central log collection, indexing, searching and detection.

[Splunk SOAR: Anyone Can Automate | Splunk](https://images.openai.com/static-rsc-4/bYb4Pk7T4bROBAi36crqSdoYVaMMKlDHuiDguXtEns2qcdeidSGlZw9sPciexGdC5_7hNHVVwQmrXoR6f1yjKMLQrEZljK-pBDkW52TnW6THQTSFcJirA4S9Z6PX_QNyfCEyE4lZ_QcWmy6eMwGKUmpEih1AFh6g1ppp_CJK9TQ?purpose=inline)

**Splunk SOAR**

Security orchestration, investigation containers and playbook execution.

[VirusTotal Logo PNG Vector (SVG) Free Download](https://images.openai.com/static-rsc-4/ypE-5KqH7v4Kr2sB4IwvJllu3hz2AOccM3IwR3kYhVlaaqHhh3PmgiGnzyBjsTwj78x-YpB4zoeyhGT0Sf_eYeFrPkgVTGfnZfqGHBD1bgGLApvbcUejm80siz3BhcXYrRVrvtKtabSNpyyvtX5mwtQzpwBehEDS07xqn5FUYYc?purpose=inline)

**VirusTotal**

External IP reputation and threat intelligence enrichment.

[Gmail New 2020 Logo PNG (Transparent) SVG AI – Free Download](https://images.openai.com/static-rsc-4/1Hbj2BNKA46lYOXKSmUqF1xcAyLiHwCPIajMCy5J3Z8gjE5CuHIs3VjLlHgUZC8EzpJtDlfgCOCGir3PmJZwexXhjrKdkjh30QvJwZcUM23A8p0lCjZGzvk-z8U3OYfHlowu03dmWIDEpJDcP0T8Yez7r7CutqXJWAT3Z0UTDEs?purpose=inline)

**Gmail SMTP**

Intended email notification channel; asset configuration is in progress.

## **3.3 Role of each component**

### **Microsoft Azure**

Azure provides the cloud infrastructure used to host the SOC laboratory.

The virtual machines provide separate environments for:

- Splunk Enterprise.
- Splunk SOAR.
- Linux log generation and Universal Forwarder.

The separation of these systems helps demonstrate communication between independent security infrastructure components.

### **Linux server**

The Linux virtual machine acts as the monitored endpoint.

It generates SSH authentication events, including failed password attempts.

The authentication events provide the input data for the detection workflow.

### **Splunk Universal Forwarder**

The Universal Forwarder is installed on the Linux test machine.

Its responsibility is to monitor the authentication log and forward relevant log data to the central Splunk Enterprise receiver.

It allows the central SIEM to collect data without requiring the Splunk Enterprise server to directly read files on the Linux endpoint.

### **Splunk Enterprise**

Splunk Enterprise acts as the central SIEM platform.

Its responsibilities include:

1. Receiving logs from the Universal Forwarder.
2. Indexing the authentication events.
3. Storing events in the configured index.
4. Running searches against collected events.
5. Identifying failed SSH login activity.
6. Forwarding matching security events to Splunk SOAR.

### **Splunk SOAR**

Splunk SOAR provides the security orchestration and automation functionality.

It receives security events from Splunk Enterprise and creates investigation records.

Its playbook performs the following operations:

- Extracts event information.
- Retrieves the source IP address.
- Queries VirusTotal.
- Evaluates the reputation results.
- Classifies the IP risk.
- Updates container severity.
- Creates an investigation note.

### **VirusTotal**

VirusTotal provides external threat intelligence enrichment.

The playbook queries the VirusTotal IP address API using the source IP extracted from the SOAR event.

The returned information includes reputation and analysis statistics.

The playbook uses the malicious and suspicious detection counts, reputation value and related response data in its investigation logic.

### **SMTP and Gmail**

The SMTP application provides email-sending functionality within Splunk SOAR.

The application has been installed successfully.

The current task is configuring a Gmail SMTP asset so that the playbook can use the configured SMTP service for alert notifications.

Successful Gmail connectivity and actual email delivery are not yet confirmed in the available conversation.

# **4. Project Objective**

## **4.1 Primary objective**

The primary objective is to build an automated SOC investigation workflow that detects failed SSH login activity, enriches the source IP with external threat intelligence and automatically updates the corresponding SOAR investigation.

The project demonstrates the integration of SIEM and SOAR technologies in a cloud-hosted laboratory.

## **4.2 Security problem being addressed**

A Linux server can receive repeated authentication attempts from different source IP addresses.

For example, an attacker or automated scanner may attempt to authenticate using a common username such as `root`.

A failed authentication attempt creates a security event, but the event alone does not provide enough information to determine the reputation of the source IP.

A security analyst may need to:

1. Find the failed login event.
2. Identify the source IP address.
3. Search for the IP address in a threat intelligence service.
4. Review the malicious detection count.
5. Determine the risk level.
6. Update the incident severity.
7. Record investigation findings.
8. Notify the responsible security team.

The laboratory automates these investigation steps.

## **4.3 Implemented security workflow**

**Linux Server**

SSH authentication attempts

**Linux Authentication Logs**

Failed password events

**Splunk Universal Forwarder**

Log collection and forwarding

**Splunk Enterprise**

Indexing and detection search

**Splunk SOAR**

Container and artifact creation

**SOAR Python Playbook**

Extract source IP and perform enrichment

**VirusTotal API**

IP reputation and analysis statistics

**Risk Classification**

HIGH / MEDIUM / LOW

**SOAR Investigation Update**

Container severity and investigation note

**Gmail Email Notification**

Configuration in progress

## **4.4 Detailed explanation of the workflow**

### **Stage 1 – Linux SSH failed login**

The Linux test server receives SSH authentication attempts.

For the laboratory, failed SSH password authentication events are used as the security event source.

An example of an event observed during the successful playbook test contained:

- Username: `root`
- Source IP: `193.32.162.15`
- Hostname: `splunktest`

The presence of a failed login event does not independently prove that the server has been compromised.

### **Stage 2 – Linux authentication logging**

Linux records authentication activity in its authentication log.

The Universal Forwarder monitors the relevant log file and collects the events.

The exact full installation and monitoring commands are covered in Part 3.

### **Stage 3 – Universal Forwarder**

The Universal Forwarder sends the collected events to the Splunk Enterprise server.

The configured destination is the private IP address `10.10.1.4` on TCP port `9997`.

### **Stage 4 – Splunk Enterprise detection**

Splunk Enterprise receives the events and stores them in the `security` index.

The failed SSH login events are identified using a search based on:

- Index: `security`
- Sourcetype: `linux_secure`
- Event condition: `Failed password`

The detection search also extracts the username and source IP.

### **Stage 5 – Splunk to SOAR event forwarding**

The Splunk App for SOAR Export is configured to forward matching search results to the SOAR server.

The saved search is named:

`SOAR - Failed SSH Login Events`

The saved search runs every one minute and uses a last-15-minute search window.

The event forwarding configuration uses:

- SOAR server: `automation`
- SOAR endpoint: `https://20.80.97.79:8443`
- Event label: `events`
- Artifact label: `ssh_failed_login`

### **Stage 6 – SOAR container and artifact**

SOAR receives the forwarded event and creates an investigation container and associated artifact data.

The container represents the investigation record.

The artifact contains the event evidence, including the source IP, username and hostname.

### **Stage 7 – VirusTotal enrichment**

The Python playbook extracts the source IP address from the artifact and queries VirusTotal.

The VirusTotal API request is made against the IP address endpoint:

```
https://www.virustotal.com/api/v3/ip_addresses/{ip}
```

Authentication uses the configured API key in the HTTP request header.

The API key is stored in the SOAR environment settings and is not included in this documentation.

### **Stage 8 – Risk classification**

The playbook evaluates the VirusTotal response.

The implemented thresholds are:

| Malicious detection count | Risk classification |
| ------------------------- | ------------------- |
| 10 or more                | HIGH                |
| 3 to 9                    | MEDIUM              |
| 0 to 2                    | LOW                 |

The actual test produced a malicious detection count of 16, resulting in HIGH risk.

### **Stage 9 – Severity update**

When the playbook classifies the IP as HIGH risk, it updates the SOAR container severity using:

```
phantom.set_severity(container, risk_level.lower())
```

The playbook execution log confirmed that the severity update succeeded.

### **Stage 10 – Investigation note**

The playbook creates an investigation note using `phantom.add_note()`.

The note contains:

- Source IP address.
- Username.
- Hostname.
- VirusTotal reputation.
- Malicious detection count.
- Suspicious detection count.
- Harmless detection count.
- Undetected count.
- Risk classification.
- Risk reason.
- Recommendation for further analyst review.

This operation was successfully verified in the SOAR container.

### **Stage 11 – Email notification**

The SMTP application has been installed in SOAR.

The current work is configuring the SMTP asset with Gmail.

Once configuration and testing are successful, the intended workflow is to send an email notification for HIGH-risk events.

Email delivery is not included as a completed validation at this stage.

# **5. Project Architecture**

## **5.1 Architecture overview**

The SOC laboratory follows a distributed architecture.

Each virtual machine performs a separate function. The systems communicate over network connections to transfer security logs, trigger investigations and retrieve threat intelligence.

The architecture consists of five primary components:

| Component           | System                     | Responsibility                             |
| ------------------- | -------------------------- | ------------------------------------------ |
| Log source          | Linux VM `splunktest`      | Generates SSH authentication logs          |
| Log collector       | Splunk Universal Forwarder | Collects and forwards logs                 |
| SIEM                | Splunk Enterprise          | Indexes logs and detects failed SSH logins |
| SOAR                | Splunk SOAR On-premises    | Automates investigation and response       |
| Threat intelligence | VirusTotal                 | Provides IP reputation information         |
| Notification        | Gmail SMTP through SOAR    | Intended to deliver email alerts           |

## **5.2 Architecture diagram**

```
                         MICROSOFT AZURE
                    SOC LABORATORY ENVIRONMENT
                                |
          +---------------------+---------------------+
          |                     |                     |
          |                     |                     |
   +------+-------+     +-------+-------+     +-------+-------+
   | Linux Test VM|     | Splunk VM     |     | SOAR VM       |
   | splunktest   |     | SOC-SPLUNK-01 |     | SOC-SOAR-01   |
   |              |     |               |     |               |
   | Linux        |     | Ubuntu 24.04  |     | RHEL 9.4      |
   | Universal    |     | Splunk        |     | Splunk SOAR   |
   | Forwarder    |     | Enterprise    |     | On-premises   |
   +------+-------+     +-------+-------+     +-------+-------+
          |                     ^                     ^
          |                     |                     |
          | TCP 9997            |                     |
          +-------------------->|                     |
                                |                     |
                         Log indexing                 |
                                |                     |
                         Detection search             |
                                |                     |
                         SOAR Export integration      |
                                +-------------------->|
                                                      |
                                              Container creation
                                                      |
                                               Artifact extraction
                                                      |
                                                Python playbook
                                                      |
                                                      v
                                             +----------------+
                                             |  VirusTotal    |
                                             |  IP API        |
                                             +----------------+
                                                      |
                                               Reputation data
                                                      |
                                              Risk classification
                                                      |
                                              Severity update
                                                      |
                                             Investigation note
                                                      |
                                                      v
                                             +----------------+
                                             | SMTP / Gmail   |
                                             | Notification   |
                                             +----------------+
                                               Configuration
                                                in progress
```

**Important architecture distinction:** VirusTotal is an external service, not an Azure virtual machine. Gmail SMTP is also an external service. The laboratory connects to these services through application/API integrations.

## **5.3 Virtual machine inventory**

The following inventory reflects the actual infrastructure details available in the conversation.

| Property         | Splunk Enterprise          | Splunk SOAR              | Linux test VM                               |
| ---------------- | -------------------------- | ------------------------ | ------------------------------------------- |
| VM name          | `SOC-SPLUNK-01`            | `SOC-SOAR-01`            | `splunktest`                                |
| Operating system | Ubuntu Server 24.04        | RHEL 9.4                 | Linux distribution not explicitly available |
| Software         | Splunk Enterprise 10.6.1.1 | Splunk SOAR 6.4.1.361    | Universal Forwarder 10.6.1.1                |
| Private IP       | `10.10.1.4`                | Not explicitly available | Not explicitly available                    |
| Public IP        | `20.112.235.59`            | `20.80.97.79`            | Not explicitly available                    |
| Primary purpose  | SIEM                       | SOAR                     | Log source                                  |

### **5.3.1 Splunk Enterprise VM**

**VM name:** `SOC-SPLUNK-01`

**Operating system:** Ubuntu Server 24.04

**Splunk version:** 10.6.1.1

**Private IP:** `10.10.1.4`

**Public IP:** `20.112.235.59`

**Web interface:**

```
http://20.112.235.59:8000
```

**Management interface port:**

```
8089
```

**Receiver port:**

```
9997
```

The Splunk Enterprise VM serves as the central SIEM server.

It receives authentication events from the Linux Universal Forwarder, indexes them and executes the detection search.

It also hosts the Splunk App for SOAR Export integration used to forward matching security events to SOAR.

### **5.3.2 Splunk SOAR VM**

**VM name:** `SOC-SOAR-01`

**Operating system:** Red Hat Enterprise Linux 9.4

**SOAR version:** 6.4.1.361

**Public IP:** `20.80.97.79`

**SOAR web interface:**

```
https://20.80.97.79:8443
```

**SOAR home directory:**

```
/data/phantom
```

The SOAR VM runs Splunk SOAR On-premises.

It receives security events from Splunk Enterprise and executes the automated investigation playbook.

The SOAR installation uses the unprivileged `phantom` user.

Known account details:

| Property                    | Value                                |
| --------------------------- | ------------------------------------ |
| SOAR operating user         | `phantom`                            |
| UID                         | `1001`                               |
| GID                         | `1001`                               |
| Installation/home directory | `/data/phantom`                      |
| Start script                | `/data/phantom/bin/start_phantom.sh` |
| Stop script                 | `/data/phantom/bin/stop_phantom.sh`  |

Credentials are not documented.

### **5.3.3 Linux test VM**

**VM name:** `splunktest`

The Linux VM is used as the monitored endpoint.

Its Universal Forwarder monitors the authentication log and forwards events to the Splunk Enterprise server.

The configured destination is:

```
10.10.1.4:9997
```

The event data is associated with:

| Field      | Value                     |
| ---------- | ------------------------- |
| Host       | `splunktest`              |
| Index      | `security`                |
| Sourcetype | `linux_secure`            |
| Log type   | SSH authentication events |

The exact Linux distribution, VM size, public/private IP and Azure resource group are not explicitly available in the conversation.

## **5.4 Network communication architecture**

The implementation requires communication between the Linux VM, Splunk Enterprise and Splunk SOAR.

The known network paths are:

| Source                       | Destination       | Port              | Purpose                        |
| ---------------------------- | ----------------- | ----------------- | ------------------------------ |
| Administrator browser        | Splunk Enterprise | TCP 8000          | Splunk Web access              |
| Splunk administration client | Splunk Enterprise | TCP 8089          | Splunk management API          |
| Linux Universal Forwarder    | Splunk Enterprise | TCP 9997          | Log forwarding                 |
| Administrator browser        | Splunk SOAR       | TCP 8443          | SOAR Web access                |
| SOAR playbook                | VirusTotal        | HTTPS 443         | IP reputation API              |
| SOAR SMTP integration        | Gmail SMTP        | TCP 587, intended | SMTP submission using STARTTLS |

The SMTP port shown is the planned Gmail configuration, not a confirmed successful connection from this laboratory.

The actual Azure NSG rules and their complete source/destination definitions are not explicitly available in the conversation.

## **5.5 Splunk Enterprise to SOAR communication**

The Splunk Enterprise server communicates with SOAR through the Splunk App for SOAR Export.

The configured SOAR endpoint is:

```
https://20.80.97.79:8443
```

The configured SOAR server name is:

```
automation
```

Authentication is performed using a configured SOAR token.

The token value is confidential and is intentionally excluded.

### **TLS configuration**

The integration was configured with:

```
verify_certs=0
```

This disables certificate verification for the laboratory integration.

This was a lab-specific workaround and should not be treated as a production security recommendation.

A production deployment should use a trusted certificate and enable certificate verification.

## **5.6 Linux to Splunk Enterprise communication**

The Linux Universal Forwarder forwards authentication logs to:

```
10.10.1.4:9997
```

The use of the private IP address indicates that the forwarding destination is the Splunk Enterprise server's private network endpoint.

The forwarding workflow is:

```
Linux authentication log
        |
        v
Universal Forwarder
        |
        | TCP 9997
        |
        v
Splunk Enterprise
        |
        v
security index
```

## **5.7 SOAR to VirusTotal communication**

The SOAR playbook uses the VirusTotal API directly through Python HTTP requests.

The API endpoint pattern is:

```
https://www.virustotal.com/api/v3/ip_addresses/{ip}
```

The request uses the following headers:

```
headers = {    "x-apikey": api_key,    "Accept": "application/json"}
```

The request timeout is:

```
timeout=20
```

The API key is retrieved from the SOAR environment settings using:

```
os.environ.get("VIRUSTOTAL_API_KEY")
```

The environment variable was verified successfully during the playbook execution.

## **5.8 SOAR playbook architecture**

The playbook performs the investigation using Python code.

Its main implementation components are:

| Component                 | Implementation                    |
| ------------------------- | --------------------------------- |
| Playbook name             | `SOC_Invalid_Login_Response`      |
| Latest active playbook ID | `177`                             |
| Latest version            | `13`                              |
| Execution model           | Custom whole-playbook Python code |
| Main processing function  | `code_2()`                        |
| Entry point               | `on_start(container)`             |
| Completion handler        | `on_finish(container, summary)`   |
| Artifact extraction       | `phantom.collect2()`              |
| API integration           | Python `requests`                 |
| Severity update           | `phantom.set_severity()`          |
| Note creation             | `phantom.add_note()`              |

The latest confirmed successful execution is:

| Property            | Value                |
| ------------------- | -------------------- |
| Playbook ID         | `177`                |
| Version             | `13`                 |
| Run ID              | `723`                |
| Container/event ID  | `723`                |
| Execution status    | Success              |
| Risk classification | HIGH                 |
| Investigation note  | Created successfully |

The complete implementation logic and execution evidence are covered in Parts 6 and 7.

## **5.9 Architecture design decisions**

### **Separation of SIEM and SOAR**

Splunk Enterprise and Splunk SOAR are deployed as separate systems.

This separation demonstrates the distinction between security monitoring and security orchestration.

Splunk Enterprise is responsible for identifying suspicious events.

SOAR is responsible for processing those events and executing automated investigation steps.

### **Centralized log collection**

The Universal Forwarder provides a centralized log collection mechanism.

Instead of manually transferring authentication log files, the forwarder continuously sends the monitored data to Splunk Enterprise.

### **External threat intelligence enrichment**

The integration with VirusTotal adds context to the source IP address.

This helps the investigation workflow move beyond simply identifying a failed login attempt.

### **Python-based playbook**

The playbook uses Python to extract event fields, call the external API, evaluate the response and update the investigation.

The implementation uses custom whole-playbook Python mode rather than a fully visual sequence of playbook blocks.

### **Investigation evidence preservation**

The playbook creates an investigation note.

This provides a record of the enrichment results and risk assessment inside the SOAR investigation.

### **Email notification extension**

The SMTP application has been installed.

The intended architecture includes email notification after risk classification.

Gmail SMTP asset configuration is the current stage; successful email delivery remains unverified.

## **5.10 Architecture limitations and items not explicitly captured**

The following information was not explicitly available in the conversation and is therefore not assumed:

| Architecture detail                            | Availability                                                                       |
| ---------------------------------------------- | ---------------------------------------------------------------------------------- |
| Azure subscription ID                          | Not explicitly available in the conversation                                       |
| Azure resource group names for these three VMs | Not explicitly available in the conversation                                       |
| Virtual network name                           | Not explicitly available in the conversation                                       |
| Subnet name and CIDR                           | Not explicitly available in the conversation                                       |
| Linux VM operating system version              | Not explicitly available in the conversation                                       |
| Linux VM size                                  | Not explicitly available in the conversation                                       |
| Splunk VM size and disk configuration          | Not explicitly available in the conversation                                       |
| SOAR VM size and disk configuration            | Not explicitly available in the conversation                                       |
| SOAR private IP                                | Not explicitly available in the conversation                                       |
| Linux VM private/public IP                     | Not explicitly available in the conversation                                       |
| Complete NSG inbound/outbound rules            | Not explicitly available in the conversation                                       |
| Azure authentication method                    | Not explicitly available in the conversation                                       |
| Exact Splunk installation commands             | To be documented in the infrastructure implementation section only where available |
| Exact Universal Forwarder configuration files  | To be documented in the log collection section only where available                |

These gaps do not change the confirmed project status. They identify information that was not preserved in the available implementation history.

## **Part 1 completion summary**

Part 1 establishes the project purpose, the security problem, the technology stack, the overall architecture and the confirmed communication paths.

The implementation has already progressed beyond the architecture stage: the detection, SOAR integration, VirusTotal enrichment, risk classification, severity update, investigation note and playbook test are all confirmed successful.

The current implementation position is the Gmail SMTP asset configuration stage.

**End of Part 1. Send 'Continue' for Part 2.**&#x20;
