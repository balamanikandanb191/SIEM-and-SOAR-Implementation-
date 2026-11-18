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

# **PART 2 – AZURE INFRASTRUCTURE & SPLUNK ENTERPRISE SETUP**

SOC Lab Implementation Documentation · Part 2 of 10

This part documents the Azure-hosted infrastructure and Splunk Enterprise environment used in the SOC laboratory. It preserves the actual VM names, operating systems, IP addresses, versions and access endpoints confirmed in the implementation history.

**Accuracy note:** The available conversation confirms the deployed systems and their working endpoints, but does not preserve every original Azure portal click, VM creation command or installation command. Those details are explicitly identified rather than reconstructed as facts.

# **6. Azure Infrastructure**

## **6.1 Infrastructure overview**

Microsoft Azure was used as the cloud platform to host the SOC laboratory.

Three separate virtual machines were used for the core security workflow:

1. `SOC-SPLUNK-01` – Splunk Enterprise.
2. `SOC-SOAR-01` – Splunk SOAR On-premises.
3. `splunktest` – Linux endpoint and Universal Forwarder.

This separation allows the laboratory to simulate a small enterprise security monitoring environment.

The Linux machine generates security events, Splunk Enterprise collects and detects them, and Splunk SOAR processes the resulting investigations.

## **6.2 Azure virtual machine inventory**

| VM name         | Operating system                            | Software                     | IP address                                    |
| --------------- | ------------------------------------------- | ---------------------------- | --------------------------------------------- |
| `SOC-SPLUNK-01` | Ubuntu Server 24.04                         | Splunk Enterprise 10.6.1.1   | Public: `20.112.235.59`; Private: `10.10.1.4` |
| `SOC-SOAR-01`   | RHEL 9.4                                    | Splunk SOAR 6.4.1.361        | Public: `20.80.97.79`                         |
| `splunktest`    | Linux distribution not explicitly available | Universal Forwarder 10.6.1.1 | Not explicitly available                      |

## **6.3 Splunk Enterprise virtual machine**

### **6.3.1 VM details**

| Configuration      | Confirmed value             |
| ------------------ | --------------------------- |
| VM name            | `SOC-SPLUNK-01`             |
| Cloud platform     | Microsoft Azure             |
| Operating system   | Ubuntu Server 24.04         |
| Installed software | Splunk Enterprise           |
| Splunk version     | 10.6.1.1                    |
| Private IP         | `10.10.1.4`                 |
| Public IP          | `20.112.235.59`             |
| Web interface      | `http://20.112.235.59:8000` |
| Management port    | `8089`                      |
| Receiving port     | `9997`                      |

### **6.3.2 Purpose**

This virtual machine acts as the central SIEM server.

It receives authentication logs from the Linux Universal Forwarder, indexes the incoming data and runs the failed SSH login detection search.

It also hosts the Splunk App for SOAR Export integration.

### **6.3.3 Accessing Splunk Enterprise**

The confirmed browser endpoint is:

```
http://20.112.235.59:8000
```

The management interface uses port `8089`.

The receiver port `9997` is used by the Universal Forwarder to send logs.

### **6.3.4 Azure networking**

The Universal Forwarder uses the Splunk Enterprise private IP address:

```
10.10.1.4
```

The configured forwarding destination is:

```
10.10.1.4:9997
```

This is the known internal log-forwarding path.

The exact Azure virtual network name, subnet name, subnet CIDR and network security group configuration are not explicitly available in the conversation.

## **6.4 Splunk SOAR virtual machine**

### **6.4.1 VM details**

| Configuration         | Confirmed value              |
| --------------------- | ---------------------------- |
| VM name               | `SOC-SOAR-01`                |
| Operating system      | Red Hat Enterprise Linux 9.4 |
| SOAR deployment       | On-premises                  |
| SOAR version          | 6.4.1.361                    |
| Public IP             | `20.80.97.79`                |
| Web port              | `8443`                       |
| Home directory        | `/data/phantom`              |
| Operating system user | `phantom`                    |
| User ID               | `1001`                       |
| Group ID              | `1001`                       |

### **6.4.2 Purpose**

The SOAR VM hosts the security orchestration platform.

It receives security events from Splunk Enterprise and runs the automated playbook.

The playbook communicates with VirusTotal, classifies IP risk, updates the container severity and creates an investigation note.

### **6.4.3 Accessing Splunk SOAR**

The confirmed SOAR URL is:

```
https://20.80.97.79:8443
```

The SOAR instance was identified in the interface as:

```
pink-mimas-raspberry
```

The displayed version was:

```
6.4.1.361
```

### **6.4.4 SOAR operating account**

The unprivileged SOAR user is:

```
phantom
```

Its known UID and GID are both `1001`.

The installation directory is:

```
/data/phantom
```

The start and stop scripts are:

```
/data/phantom/bin/start_phantom.sh
```

```
/data/phantom/bin/stop_phantom.sh
```

The exact Linux administrator account used to initially provision the VM is not explicitly available in the conversation.

## **6.5 Linux test virtual machine**

### **6.5.1 Purpose**

The Linux VM named `splunktest` acts as the monitored endpoint.

It generates SSH authentication events for the SOC detection workflow.

The Universal Forwarder installed on this machine collects the authentication log and forwards it to Splunk Enterprise.

### **6.5.2 Known configuration**

| Property                  | Value                      |
| ------------------------- | -------------------------- |
| Hostname                  | `splunktest`               |
| Installed software        | Splunk Universal Forwarder |
| Version                   | 10.6.1.1                   |
| Destination Splunk server | `10.10.1.4`                |
| Destination port          | `9997`                     |
| Index                     | `security`                 |
| Sourcetype                | `linux_secure`             |
| Log category              | SSH authentication         |

The Linux distribution and exact VM specifications are not explicitly available in the conversation.

## **6.6 Azure network communication**

The following communication paths were used or intended by the implementation.

| Source                           | Destination       | Protocol                       | Port        | Purpose                 |
| -------------------------------- | ----------------- | ------------------------------ | ----------- | ----------------------- |
| Administrator browser            | Splunk Enterprise | HTTP                           | 8000        | Splunk Web              |
| Splunk administration client     | Splunk Enterprise | TCP/HTTPS management interface | 8089        | Management API          |
| `splunktest` Universal Forwarder | `SOC-SPLUNK-01`   | TCP                            | 9997        | Log forwarding          |
| Administrator browser            | `SOC-SOAR-01`     | HTTPS                          | 8443        | SOAR Web                |
| `SOC-SOAR-01`                    | VirusTotal        | HTTPS                          | 443         | Threat intelligence API |
| `SOC-SOAR-01`                    | Gmail SMTP        | TCP                            | 587 planned | Email submission        |

The exact NSG rule names, source address restrictions and destination address restrictions are not explicitly available in the conversation.

## **6.7 Azure security considerations**

The laboratory uses public IP addresses for web-based administration of Splunk Enterprise and Splunk SOAR.

The known public endpoints are:

```
Splunk Enterprise:
20.112.235.59:8000
```

```
Splunk SOAR:
20.80.97.79:8443
```

For a production environment, these administrative interfaces should be restricted to approved management networks or accessed through a VPN, private connectivity or a hardened jump host.

The Universal Forwarder uses the Splunk server's private address rather than its public address.

The SOAR-to-VirusTotal connection requires outbound HTTPS connectivity.

The planned Gmail integration requires outbound SMTP submission connectivity.

# **7. Splunk Enterprise Installation and Configuration**

## **7.1 Overview**

Splunk Enterprise is the central SIEM component of this laboratory.

It is installed on the Azure virtual machine:

```
SOC-SPLUNK-01
```

The operating system is:

```
Ubuntu Server 24.04
```

The installed Splunk version is:

```
10.6.1.1
```

The primary responsibility of this server is to receive, index, search and detect security events collected from the Linux test machine.

## **7.2 Why Splunk Enterprise was required**

The Linux endpoint generates authentication logs locally.

Without a centralized log monitoring system, an administrator would need to connect to individual Linux servers and manually inspect authentication events.

Splunk Enterprise provides a central location to:

- Collect logs.
- Index events.
- Search historical authentication activity.
- Extract useful event fields.
- Identify failed login attempts.
- Create scheduled searches.
- Forward matching events to SOAR.

The implementation uses the `security` index for the Linux authentication events.

## **7.3 Installation environment**

| Parameter                | Value               |
| ------------------------ | ------------------- |
| Azure VM                 | `SOC-SPLUNK-01`     |
| Operating system         | Ubuntu Server 24.04 |
| Splunk software          | Splunk Enterprise   |
| Version                  | 10.6.1.1            |
| Web port                 | 8000                |
| Management port          | 8089                |
| Forwarder receiving port | 9997                |
| Public IP                | `20.112.235.59`     |
| Private IP               | `10.10.1.4`         |

## **7.4 Installation procedure – reconstructed implementation record**

The final installed state is confirmed. However, the exact original download URL, package filename, installation command, initial administrator creation command and license acceptance command are not preserved in the available conversation.

For that reason, this section distinguishes the confirmed result from a generic reproduction procedure.

### **Step 1 – Access the Azure virtual machine**

The Splunk Enterprise VM was provisioned in Azure and accessed for software installation.

Confirmed machine:

```
SOC-SPLUNK-01
```

Confirmed operating system:

```
Ubuntu Server 24.04
```

The exact original SSH command and administrator username are not explicitly available in the conversation.

### **Step 2 – Install Splunk Enterprise**

Splunk Enterprise was installed on the Ubuntu server.

The confirmed installed version is:

```
10.6.1.1
```

The exact package download command, package name and installation command are not explicitly available in the conversation.

### **Step 3 – Start Splunk Enterprise**

The Splunk Enterprise service was started and became accessible through its Web interface.

The exact command used for the initial startup is not explicitly available in the conversation.

A generic example of a Splunk startup command is shown below for reference only; it is not represented as a verified command from the original session:

```
sudo /opt/splunk/bin/splunk start
```

### **Step 4 – Access the Web interface**

The confirmed browser URL is:

```
http://20.112.235.59:8000
```

The availability of the Web interface confirms that the Splunk Enterprise service was running and reachable through the configured public endpoint.

### **Step 5 – Confirm the management endpoint**

The management port is:

```
8089
```

This interface is used by Splunk administrative APIs and integrations.

### **Step 6 – Configure the receiving endpoint**

The Universal Forwarder forwards events to:

```
10.10.1.4:9997
```

Therefore, the Splunk Enterprise instance is configured to receive forwarded data on port `9997`.

The exact original portal navigation or command used to enable the receiving port is not explicitly available in the conversation.

## **7.5 Splunk Enterprise interfaces**

| Interface          | Address                     | Purpose                                                      |
| ------------------ | --------------------------- | ------------------------------------------------------------ |
| Splunk Web         | `http://20.112.235.59:8000` | Search, dashboards and administration                        |
| Management API     | `https://10.10.1.4:8089`    | Administrative API endpoint, using the known private address |
| Forwarder receiver | `10.10.1.4:9997`            | Receiving forwarded log events                               |

The management URL above describes the endpoint and port. Its certificate configuration and exact browser accessibility were not separately verified in the available history.

## **7.6 Splunk data processing architecture**

Splunk Enterprise performs several operations after receiving data from the Universal Forwarder.

### **Data receiving**

The receiving endpoint accepts the forwarded events from the Linux machine.

### **Parsing and indexing**

Splunk processes the incoming event data and stores it in the configured index.

The laboratory uses:

```
security
```

### **Sourcetype identification**

The Linux authentication events are associated with:

```
linux_secure
```

This sourcetype helps Splunk identify the type and structure of the authentication data.

### **Search processing**

The detection search retrieves the events and filters for failed password authentication messages.

The source IP and username are extracted for investigation and forwarding.

## **7.7 Splunk Enterprise verification**

The following implementation facts were confirmed:

| Validation                        | Result    |
| --------------------------------- | --------- |
| Splunk Enterprise installed       | Confirmed |
| Installed version 10.6.1.1        | Confirmed |
| Ubuntu Server 24.04               | Confirmed |
| Splunk Web endpoint               | Confirmed |
| Management port 8089              | Confirmed |
| Forwarder receiver port 9997      | Confirmed |
| Private IP `10.10.1.4`            | Confirmed |
| Logs indexed in `security`        | Confirmed |
| `linux_secure` sourcetype         | Confirmed |
| Failed SSH search returned events | Confirmed |
| Events forwarded to SOAR          | Confirmed |

## **7.8 Splunk Enterprise configuration summary**

The confirmed configuration values are:

```
Server Name : SOC-SPLUNK-01
Operating System : Ubuntu Server 24.04
Splunk Version : 10.6.1.1

Public IP : 20.112.235.59
Private IP : 10.10.1.4

Web Port : 8000
Management Port : 8089
Receiving Port : 9997

Index : security
Sourcetype : linux_secure
```

## **7.9 Configuration details not preserved**

The following original installation details are not explicitly available in the conversation:

- Splunk Enterprise package filename.
- Exact download URL.
- Exact installation command.
- Initial Splunk administrator username.
- Initial Splunk administrator credential configuration.
- Exact license acceptance command.
- Exact boot-start configuration command.
- Exact service status command and output.
- Original `server.conf`.
- Original `inputs.conf`.
- Exact receiving-port configuration procedure.
- Azure VM size.
- Azure disk size and type.
- Resource group name.
- VNet and subnet names.
- Exact NSG rule configuration.

These details should not be fabricated in a technical implementation record.

## **7.10 Final implementation status**

COMPLETED

Splunk Enterprise installation and configuration are complete.

The following downstream operations confirm that the SIEM server was operational:

1. Linux authentication logs were received.
2. Events were indexed in the `security` index.
3. The `linux_secure` sourcetype was used.
4. The failed SSH login detection search returned events.
5. Matching events were forwarded to Splunk SOAR.

These successful downstream validations establish that Splunk Enterprise was functioning as the SIEM component of the laboratory.

**Part 2 completion summary:** The Azure infrastructure inventory, confirmed network endpoints, Splunk Enterprise environment and its role in the SOC architecture have been documented. Exact installation commands that were not preserved have been identified rather than invented.

**End of Part 2. Send 'Continue' for Part 3.**

PART 3 – UNIVERSAL FORWARDER, SSH FAILED-LOGIN GENERATION & LOG INGESTION
SOC Lab Implementation Documentation · Part 3 of 10

This part documents how the Linux test machine became a log source for Splunk Enterprise.

It covers the Universal Forwarder, authentication log collection, forwarding destination, SSH failed-login events, indexing and the confirmed data-ingestion results.

The implementation used the Linux VM splunktest and the Splunk Enterprise server SOC-SPLUNK-01.

8. Linux Universal Forwarder Implementation
8.1 Introduction
Splunk Universal Forwarder is a lightweight Splunk software component designed to collect data from remote systems and forward it to a central Splunk instance.

In this laboratory, the Universal Forwarder was installed on the Linux test VM.

Its primary responsibility was to monitor Linux authentication logs and send the collected data to Splunk Enterprise.

Without the Universal Forwarder, the Splunk Enterprise server would not automatically receive the Linux authentication events through the forwarding architecture implemented in this project.
8.2 Why the Universal Forwarder was required
The SOC laboratory uses a distributed architecture.

The Linux machine generates authentication events, while Splunk Enterprise performs centralized monitoring and detection.

The Universal Forwarder connects these two systems.

Its responsibilities include:
1. Monitoring the Linux authentication log.
2. Reading new log events.
3. Associating the collected events with the configured host and sourcetype metadata.
4. Forwarding the events to Splunk Enterprise.
5. Supporting continuous log collection.
The implementation uses the Universal Forwarder rather than installing the complete Splunk Enterprise platform on the Linux endpoint.
8.3 Universal Forwarder environment
Property	Confirmed value
Hostname	splunktest
Operating system	Linux
Forwarder software	Splunk Universal Forwarder
Version	10.6.1.1
Destination server	SOC-SPLUNK-01
Destination private IP	10.10.1.4
Destination receiving port	9997
Index	security
Sourcetype	linux_secure
Authentication log	/var/log/auth.log

The exact Linux distribution and the initial Universal Forwarder installation package are not explicitly available in the conversation.
8.4 Installation and configuration overview
The Universal Forwarder implementation required the following logical steps:
1. Prepare the Linux test VM.
2. Install Splunk Universal Forwarder.
3. Configure the Splunk Enterprise receiving destination.
4. Configure the authentication log input.
5. Associate the log input with the intended index and sourcetype.
6. Restart or reload the forwarder configuration.
7. Verify that the Linux events reach Splunk Enterprise.
The successful final implementation confirms that the Universal Forwarder was installed and forwarding authentication logs.

The exact original installation command, package filename and complete configuration file contents were not preserved in the available conversation.
8.5 Forwarding destination
The confirmed forwarding destination is:
10.10.1.4:9997


This address belongs to the Splunk Enterprise server.

The Universal Forwarder sends collected authentication events to the Splunk Enterprise receiving port.

The data path is:
Linux VM: splunktest
        |
        | Authentication log
        v
Splunk Universal Forwarder
        |
        | TCP 9997
        v
Splunk Enterprise: SOC-SPLUNK-01
        |
        | Private IP: 10.10.1.4
        v
Index: security


8.6 Universal Forwarder configuration files
Splunk Universal Forwarder commonly uses two important configuration files for this implementation.
Configuration file	Responsibility
inputs.conf	Defines the files and data sources to monitor
outputs.conf	Defines the destination Splunk receiving server

The actual file contents used during the original setup were not explicitly preserved in the conversation.

The following examples show the configuration structure consistent with the confirmed laboratory values. They are reference examples, not verbatim copies of recovered files.
8.6.1 inputs.conf
Purpose:

inputs.conf tells the Universal Forwarder which local log file to monitor and how to identify the collected events.

A representative configuration is:
[monitor:///var/log/auth.log]
disabled = 0
index = security
sourcetype = linux_secure


Explanation:
Setting	Purpose
monitor	Tells the forwarder to monitor a file
/var/log/auth.log	Linux authentication log path
disabled = 0	Enables the input
index = security	Associates events with the security index
sourcetype = linux_secure	Identifies the authentication event type

The final project confirms the authentication log source, index and sourcetype. The exact original configuration syntax is not available.
8.6.2 outputs.conf
Purpose:

outputs.conf defines where the Universal Forwarder sends its collected data.

A representative configuration is:
[tcpout]
defaultGroup = splunk_enterprise

[tcpout:splunk_enterprise]
server = 10.10.1.4:9997


Explanation:
Setting	Purpose
[tcpout]	Configures TCP forwarding
defaultGroup	Selects the forwarding destination group
[tcpout:splunk_enterprise]	Defines a destination group
server	Specifies the Splunk Enterprise IP and receiving port

The confirmed forwarding destination is 10.10.1.4:9997.

The original output configuration filename, group name and any additional forwarding settings are not explicitly available.
8.7 Restarting the Universal Forwarder
Changes to Universal Forwarder configuration generally require the forwarder to reload or restart its configuration.

A representative restart command is:
sudo /opt/splunkforwarder/bin/splunk restart


This command is provided as a reproduction reference only. The exact restart command used in the original implementation was not preserved in the available conversation.

The successful downstream log ingestion confirms that the forwarder was operational.
8.8 Universal Forwarder validation
The most important validation was not simply checking whether the software was installed.

The actual requirement was to confirm that Linux authentication events reached Splunk Enterprise.

The following final-state values were confirmed:
Validation item	Result
Universal Forwarder installed	Confirmed
Version 10.6.1.1	Confirmed
Linux hostname splunktest	Confirmed
Destination 10.10.1.4:9997	Confirmed
Authentication log collection	Confirmed
Index security	Confirmed
Sourcetype linux_secure	Confirmed
Failed SSH events searchable in Splunk	Confirmed


9. SSH Failed-Login Event Generation
9.1 Introduction
SSH (Secure Shell) is a protocol used to access and administer remote systems securely.

A Linux server can record successful and unsuccessful SSH authentication attempts.

This laboratory uses failed SSH password authentication events as the security detection source.

The purpose is to generate controlled authentication events so that the complete SIEM-to-SOAR workflow can be validated.
9.2 Why failed SSH logins were used
Failed SSH logins are useful for security monitoring because they can indicate suspicious authentication activity.

Examples include:
- Repeated password guessing.
- Attempts to authenticate using common usernames.
- Automated login scanning.
- Attempts originating from suspicious external addresses.
The project uses failed authentication events to demonstrate how a SOC workflow can identify an event and investigate its source IP.

Security interpretation: A failed password attempt alone does not prove that the attacker gained access. It is an indicator that requires investigation and context.
9.3 Log source
The monitored Linux machine is:
splunktest


The authentication log path is:
/var/log/auth.log


The relevant event category is SSH authentication.

The log data is collected by the Universal Forwarder and sent to Splunk Enterprise.
9.4 Example failed authentication event
The successful SOAR investigation identified the following event values:
Field	Value
Hostname	splunktest
Username	root
Source IP	193.32.162.15
Event type	Failed SSH password authentication

These values are taken from the confirmed successful playbook execution.

The exact original SSH command used to generate the failed authentication attempt is not explicitly available in the conversation.

The exact remote destination address and SSH command-line options are also not preserved.
9.5 Failed-login event generation
The laboratory generated SSH failed-login activity to test the authentication logging and detection pipeline.

The following is an illustrative example of a command that can generate a password-authentication attempt against an SSH server:
ssh -o PreferredAuthentications=password \
    -o PubkeyAuthentication=no \
    testuser@<LINUX_SERVER_IP>


Important: This is a generic reproduction example, not a verified record of the exact command executed in this project.

The actual test username and target IP used during the original event-generation procedure are not explicitly available.
Command parameter explanation
Parameter	Meaning
ssh	Starts an SSH client connection
'-o	Supplies an SSH client configuration option
PreferredAuthentications=password	Prefers password authentication
PubkeyAuthentication=no	Disables public-key authentication for the attempt
testuser	Example username
<LINUX_SERVER_IP>	Placeholder for the Linux SSH server address

A failed password attempt should produce an authentication failure record if the SSH server is configured to log the attempt.
9.6 Expected authentication log behaviour
When a failed SSH password authentication occurs, Linux records an event in the authentication log.

A typical message structure may resemble:
Failed password for <username> from <source_ip>


The exact original raw event message was not preserved in the available conversation.

However, the confirmed successful detection search filtered events containing:
Failed password


The SOAR test later extracted:
username = root
sourceAddress = 193.32.162.15
deviceHostname = splunktest


This confirms that the relevant authentication information was available in the forwarded event data.
9.7 Security considerations for test generation
Failed-login testing should be performed only against systems the engineer owns or is explicitly authorised to test.

The test should avoid:
- Repeatedly targeting third-party systems.
- Generating uncontrolled authentication traffic.
- Using real user credentials.
- Attempting to gain unauthorised access.
For a production environment, detection testing should be conducted using an approved test account and a controlled source address.

10. Linux Authentication Log Collection
10.1 Introduction
Linux authentication logs record security-related authentication activity.

In this laboratory, the authentication log provides the source data for detecting failed SSH password attempts.

The Universal Forwarder monitors the authentication log and forwards its contents to Splunk Enterprise.
10.2 Log file
The confirmed monitored log path is:
/var/log/auth.log


This file is commonly used on Debian-based Linux systems.

The exact distribution running on splunktest is not explicitly available in the conversation.

The final implementation confirms that the authentication events were collected and searchable in Splunk Enterprise.
10.3 Log collection workflow
SSH Authentication Attempt
          |
          v
Linux SSH Service
          |
          v
Authentication Log
/var/log/auth.log
          |
          v
Splunk Universal Forwarder
          |
          v
Splunk Enterprise Receiver
10.10.1.4:9997
          |
          v
Index: security
          |
          v
Sourcetype: linux_secure


10.4 Index configuration
The events were stored in:
security


An index is a logical storage location in Splunk.

It allows related events to be grouped and searched efficiently.

The laboratory uses the security index for the Linux authentication events.
10.5 Sourcetype configuration
The confirmed sourcetype is:
linux_secure


A sourcetype identifies the type and structure of incoming data.

It helps Splunk apply appropriate event processing and field extraction behaviour.

In this project, the sourcetype is associated with Linux authentication events.
10.6 Host identification
The confirmed host value is:
splunktest


The host field identifies the Linux system from which the authentication events originated.

This is important because the same index can contain events from multiple monitored systems.
10.7 Log ingestion verification
The final successful Splunk-to-SOAR workflow establishes that the log collection process was operational.

The evidence includes:
- Failed SSH events were searchable in Splunk.
- The detection search extracted the source IP.
- The detection search extracted the username.
- The host field identified splunktest.
- The saved search forwarded the matching event to SOAR.
- The SOAR playbook retrieved the forwarded event fields.
10.8 Known event metadata
Field	Value
index	security
sourcetype	linux_secure
host	splunktest
Authentication log	/var/log/auth.log
Detection phrase	Failed password
Extracted username	root in the confirmed test
Extracted source IP	193.32.162.15 in the confirmed test


11. Log Collection Troubleshooting and Validation
11.1 Troubleshooting record
The available implementation history confirms that log collection and forwarding ultimately worked. It does not preserve a specific failed Universal Forwarder installation command or a corresponding error message.

Therefore, no unrecorded failure is attributed to the original implementation.

The following table distinguishes confirmed results from unavailable historical details.
Area	Historical information
Universal Forwarder installation	Successfully completed
Universal Forwarder version	10.6.1.1
Forwarding destination	10.10.1.4:9997
Authentication log source	/var/log/auth.log
Index	security
Sourcetype	linux_secure
Host	splunktest
Original installation errors	Not explicitly available in the conversation
Original restart errors	Not explicitly available in the conversation
Original connectivity-test output	Not explicitly available in the conversation
Final log ingestion	Successfully verified

11.2 Common validation commands
The following commands are useful when independently reproducing or troubleshooting the configuration. They are reference commands, not a claim that each was executed in the original session.

Check Universal Forwarder status:
sudo /opt/splunkforwarder/bin/splunk status


Display forwarding destinations:
sudo /opt/splunkforwarder/bin/splunk list forward-server


Display Universal Forwarder configuration:
sudo cat /opt/splunkforwarder/etc/system/local/outputs.conf


sudo cat /opt/splunkforwarder/etc/system/local/inputs.conf


Check the authentication log:
sudo tail -f /var/log/auth.log


These commands help an engineer validate the local collection and forwarding configuration.
11.3 Final validation result
COMPLETED SUCCESSFULLY
The Linux-to-Splunk log collection pipeline was operational.

The strongest confirmation is that the same Linux authentication data progressed through Splunk Enterprise detection and into a successful SOAR playbook execution.

The event data ultimately contained the following values:
Hostname: splunktest
Username: root
Source IP: 193.32.162.15


This confirms that the log collection stage was not merely configured; its data was used by the downstream security investigation workflow.

11.4 Complete data collection summary
Implementation stage	Final result
Linux test VM	Completed
Universal Forwarder installation	Completed
Authentication log monitoring	Completed
Forwarding destination configuration	Completed
Splunk Enterprise receiving	Completed
Security index ingestion	Completed
Sourcetype identification	Completed
Host identification	Completed
Failed SSH event searchability	Completed
Downstream SOAR event processing	Completed

11.5 Part 3 conclusion
The Linux Universal Forwarder successfully connected the Linux test environment to the central Splunk Enterprise server.

The authentication events were collected from /var/log/auth.log, forwarded to 10.10.1.4:9997, and made available in the security index with the linux_secure sourcetype.

The failed SSH authentication events provided the input for the detection search documented in the next part.

The exact original installation and SSH-generation commands were not preserved in the available conversation, so the reference commands above are clearly separated from the confirmed implementation evidence.

End of Part 3. Send 'Continue' for Part 4.

PART 4 – SPLUNK DETECTION ENGINEERING & SPLUNK-SOAR INTEGRATION
SOC Lab Implementation Documentation · Part 4 of 10

This part documents how the collected Linux authentication logs were converted into security detections and how matching events were automatically forwarded from Splunk Enterprise to Splunk SOAR.

The implementation uses the actual saved search name, SPL query, SOAR endpoint, integration settings, event labels and field mappings recorded during the project.

12. Splunk Detection Search
12.1 Introduction
After the Linux authentication logs were successfully ingested into Splunk Enterprise, the next requirement was to identify failed SSH login attempts.

Splunk Enterprise provides Search Processing Language (SPL), which allows administrators and security engineers to search, filter, extract and analyse indexed events.

The laboratory uses a detection search to identify Linux authentication events containing the failed password message.

The search extracts the username and source IP address because these values are needed by the downstream SOAR investigation.
12.2 Detection objective
The detection search was designed to:
1. Search Linux authentication events.
2. Identify failed password authentication attempts.
3. Extract the username involved in the attempt.
4. Extract the source IP address.
5. Display the event timestamp and hostname.
6. Provide structured fields for SOAR event forwarding.
The detection is based on an authentication failure event, not a confirmed successful compromise.
12.3 Detection environment
Property	Value
Splunk server	SOC-SPLUNK-01
Splunk version	10.6.1.1
Index	security
Sourcetype	linux_secure
Host	splunktest
Detection condition	Failed password
Search language	SPL

12.4 Exact detection SPL query
The following is the detection search preserved in the project history:
index=security sourcetype=linux_secure "Failed password"
| rex "Failed password for (invalid user )?(?<username>\S+) from (?<src_ip>\S+)"
| table _time host username src_ip _raw


This query is the confirmed search used in the implementation.
12.5 Detailed SPL explanation
Search condition
index=security sourcetype=linux_secure "Failed password"


This is the base search.

It filters the indexed events based on three conditions.
Search component	Purpose
index=security	Searches only the security index
sourcetype=linux_secure	Restricts the search to Linux authentication events
"Failed password"	Finds events containing the failed password text

This limits the search to the relevant authentication events.
Field extraction
| rex "Failed password for (invalid user )?(?<username>\S+) from (?<src_ip>\S+)"


The rex command uses a regular expression to extract values from the raw event text.

The regular expression is:
Failed password for (invalid user )?(?<username>\S+) from (?<src_ip>\S+)


The extracted fields are:
- username
- src_ip
The optional section:
(invalid user )?


allows the expression to handle failed login messages that contain the phrase invalid user.

The named capture group:
(?<username>\S+)


extracts the username.

The named capture group:
(?<src_ip>\S+)


extracts the source IP address.
Table output
| table _time host username src_ip _raw


The table command displays the selected fields in a structured format.
Field	Meaning
_time	Event timestamp
host	Source hostname
username	Extracted attempted username
src_ip	Extracted source IP
_raw	Original raw event

This output is useful for security investigation and event forwarding because it provides the essential authentication details.
12.6 Detection search output
The successful SOAR investigation later confirmed the following event values:
Field	Confirmed value
host	splunktest
username	root
src_ip	193.32.162.15

These values demonstrate that the search and subsequent forwarding workflow provided the data required for the investigation.

The complete original Splunk search-results screenshot and raw authentication event are not explicitly available in the conversation.
12.7 Why source IP extraction is important
The source IP address is one of the most important fields in this detection.

The IP address is used as the input for VirusTotal enrichment.

Without the source IP, the playbook would not know which external address to investigate.

The data flow is:
Raw Authentication Event
        |
        v
Failed Password Detection
        |
        v
Regular Expression Extraction
        |
        +------> username
        |
        +------> src_ip
        |
        v
Structured Search Result
        |
        v
SOAR Event Forwarding


12.8 Why username extraction is important
The username identifies which account was targeted by the authentication attempt.

For example, the successful investigation used:
root


This information helps an analyst understand the nature of the authentication attempt.

An attempted login using root may be more relevant to an investigation than an event where the attempted username is unknown, but the username alone does not establish malicious intent.
12.9 Detection validation
The detection was validated through the successful downstream event-processing workflow.

The source IP, username and hostname were available in the SOAR artifact and were successfully retrieved by the playbook.
Detection completed

13. Saved Search and Automated Alert Configuration
13.1 Introduction
The detection search needed to run automatically rather than requiring an administrator to execute the SPL query manually.

For this purpose, a saved search was configured in Splunk Enterprise.

The saved search acts as the trigger for the event-forwarding workflow.

When matching authentication events are returned, the Splunk App for SOAR Export forwards the results to Splunk SOAR.
13.2 Saved search details
The confirmed saved search name is:
SOAR - Failed SSH Login Events


The recorded schedule is:
Every 1 minute


The search window is:
Last 15 minutes


13.3 Saved search configuration summary
Property	Confirmed configuration
Saved search name	SOAR - Failed SSH Login Events
Search purpose	Failed SSH login detection
Schedule	Every 1 minute
Search window	Last 15 minutes
Forwarding integration	Splunk App for SOAR Export
Event label	events
Artifact label	ssh_failed_login

The exact saved-search owner, permission level, cron expression and trigger-condition settings are not explicitly available in the conversation.
13.4 Scheduled search workflow
Scheduled Search
      |
      v
Run SPL Detection
      |
      v
Find Failed Password Events
      |
      v
Extract Username and Source IP
      |
      v
Forward Matching Event
      |
      v
Splunk SOAR


13.5 Search window consideration
The saved search runs every minute and searches the last 15 minutes.

This creates an overlapping search window.

For example:
Execution	Search window
10:00	09:45–10:00
10:01	09:46–10:01
10:02	09:47–10:02

An event may therefore appear in more than one scheduled search result if it remains inside multiple search windows.

This can potentially result in duplicate forwarding or multiple investigation containers, depending on the integration's event handling and deduplication behaviour.

The original implementation history records this as a potential duplication consideration. It does not establish that duplicate containers actually occurred.

A production deployment should implement an appropriate event identity and deduplication strategy.

14. Splunk Enterprise to Splunk SOAR Integration
14.1 Introduction
Splunk Enterprise and Splunk SOAR perform different responsibilities.

Splunk Enterprise identifies suspicious activity by searching indexed data.

Splunk SOAR receives security events and automates the investigation process.

The Splunk App for SOAR Export was configured to connect these systems.

This integration is one of the central components of the SOC laboratory.
14.2 Why the integration was required
Without integration, the detection search could identify failed SSH logins in Splunk Enterprise, but the security analyst would need to manually transfer the relevant event information into the SOAR investigation.

The integration automates this process.

The objective was to achieve:
Splunk Detection
       |
       v
SOAR Event Forwarding
       |
       v
SOAR Container
       |
       v
SOAR Artifact
       |
       v
Automated Playbook


14.3 Integration application
The implementation used:

Splunk App for SOAR Export

This application provides the integration mechanism for forwarding Splunk search results to Splunk SOAR.

The application was configured on the Splunk Enterprise side.

The exact installed app version is not explicitly available in the conversation.
14.4 SOAR server endpoint
The configured SOAR URL is:
https://20.80.97.79:8443


The configured SOAR server name is:
automation


The SOAR instance version is:
6.4.1.361


14.5 Authentication
The integration uses a SOAR authentication token.

The token was configured in the Splunk App for SOAR Export settings.

The actual token is confidential and is not included in this documentation.

The token provides the authentication mechanism required for Splunk Enterprise to communicate with the SOAR server.
14.6 TLS certificate verification
The integration used:
verify_certs=0


This disabled TLS certificate verification.

It was used as a temporary laboratory workaround to allow the integration to communicate with the SOAR HTTPS endpoint.

This setting is not recommended for production.

Production environments should:
- Use a trusted TLS certificate.
- Configure the correct certificate chain.
- Enable certificate verification.
- Protect the SOAR authentication token.
- Restrict network access to the integration endpoint.
14.7 Event forwarding configuration
The integration used the following event-forwarding configuration:
Property	Value
SOAR URL	https://20.80.97.79:8443
SOAR server name	automation
Authentication	Configured SOAR token
TLS verification	Disabled for lab
verify_certs	0
Event label	events
Artifact label	ssh_failed_login

14.8 Field mapping configuration
The integration maps Splunk search fields to SOAR CEF fields.

The confirmed mappings are:
Splunk field	SOAR CEF field
_time	rt
host	deviceHostname
src_ip	sourceAddress
username	destinationUserName

Explanation of mappings
Timestamp
_time → rt


The event timestamp is mapped to the SOAR event time field.

Hostname
host → deviceHostname


This identifies the Linux system that generated the authentication event.

Source IP
src_ip → sourceAddress


This is the IP address from which the SSH authentication attempt originated.

It becomes the key input for VirusTotal enrichment.

Username
username → destinationUserName


This identifies the username targeted by the failed authentication attempt.
14.9 Why field mapping is important
Field mapping ensures that data collected in Splunk Enterprise is represented in a structured format within SOAR.

For example, Splunk uses:
src_ip


The SOAR artifact uses:
sourceAddress


The field mapping allows the playbook to retrieve the IP address using the SOAR CEF field rather than needing to interpret the original Splunk search field name.

The result is a consistent data model between detection and investigation.
14.10 Integration workflow
Splunk Enterprise
       |
       v
Saved Search:
SOAR - Failed SSH Login Events
       |
       v
Splunk App for SOAR Export
       |
       v
Authentication using SOAR token
       |
       v
SOAR endpoint:
https://20.80.97.79:8443
       |
       v
Event label:
events
       |
       v
Artifact label:
ssh_failed_login
       |
       v
SOAR Container and Artifact


14.11 Integration validation
The integration was successfully validated through actual event forwarding.

The successful downstream evidence includes:
- A SOAR event/container was created.
- The event name was SOAR - Failed SSH Login Events.
- The artifact was available.
- The artifact contained the expected source IP, username and hostname.
- The playbook retrieved those values.
- VirusTotal enrichment succeeded.
- The playbook updated the container severity.
- The playbook created an investigation note.
This confirms that the Splunk-to-SOAR integration was operational.

15. Automatic Event Forwarding
15.1 Introduction
Automatic event forwarding connects the Splunk detection process with the SOAR investigation process.

The integration removes the need for an administrator to manually create a SOAR investigation for every matching failed SSH event.

The saved search detects the event, and the configured Splunk App for SOAR Export forwards the matching result to SOAR.
15.2 Event forwarding configuration
The following settings were confirmed:
Configuration	Value
Saved search	SOAR - Failed SSH Login Events
Schedule	Every 1 minute
Search window	Last 15 minutes
SOAR endpoint	https://20.80.97.79:8443
Event label	events
Artifact label	ssh_failed_login

15.3 Forwarding sequence
1. Splunk Enterprise executes the scheduled search.
2. The search identifies events containing Failed password.
3. The search extracts the username and source IP.
4. The SOAR Export integration receives the matching search results.
5. The integration forwards the event to the configured SOAR endpoint.
6. SOAR creates the investigation container and associated artifact.
7. The artifact fields become available to the playbook.
15.4 Event forwarding field mapping
The following data is transferred:
_time       → rt
host        → deviceHostname
src_ip      → sourceAddress
username    → destinationUserName


The mappings preserve the important event information needed for automated investigation.
15.5 Automatic forwarding validation
The successful event forwarding was confirmed by opening the SOAR event.

The event displayed:
SOAR - Failed SSH Login Events


The successful playbook execution subsequently retrieved:
Source IP: 193.32.162.15
Username: root
Hostname: splunktest


This establishes that the event forwarding process transferred the necessary information into SOAR.
Automatic event forwarding completed

16. Integration Troubleshooting History
16.1 SOAR application configuration and navigation
During the later SMTP configuration work, the SOAR application interface was examined to identify the correct installation and asset configuration workflow.

The following distinctions were established:
- Splunkbase applications for Splunk Enterprise are not necessarily SOAR applications.
- The Microsoft SMTP App for Splunk and Exchange SMTP add-ons shown in Splunkbase were not the correct components for sending email from SOAR.
- The SMTP application required for the project was the Splunk SMTP application inside Splunk SOAR.
This was a separate email-integration investigation, not a failure of the existing Splunk-to-SOAR event-forwarding integration.
16.2 TLS certificate verification workaround
Problem: The Splunk-to-SOAR integration used HTTPS with certificate verification disabled.

Configuration used:
verify_certs=0


Purpose: To allow the lab integration to communicate with the SOAR endpoint despite the certificate trust configuration.

Result: Event forwarding worked.

Security implication: Certificate verification should be enabled in a production environment after configuring a trusted certificate.
16.3 Potential duplicate event forwarding
Observation: The scheduled search runs every minute and searches the last 15 minutes.

Potential issue: The overlapping search windows may return the same event more than once.

Implementation status: Event forwarding worked, but duplicate event behaviour was not explicitly confirmed.

Production consideration: Introduce deduplication based on a stable event identifier or other suitable event identity.
16.4 Troubleshooting summary
Item	Initial condition	Final result
Splunk-to-SOAR integration	Required configuration	Successfully integrated
SOAR authentication	Token required	Configured
HTTPS certificate verification	Lab workaround required	verify_certs=0
Event label	Required	events
Artifact label	Required	ssh_failed_login
Field mapping	Required	Configured
Scheduled search	Required	Every minute
Search window	Required	Last 15 minutes
Container/event creation	Required	Verified
Artifact availability	Required	Verified
Duplicate handling	Not explicitly confirmed	Production consideration


17. Part 4 Final Validation
The following implementation stages were successfully completed:
Implementation component	Status
Failed SSH login SPL detection	Completed
Username extraction	Completed
Source IP extraction	Completed
Saved search creation	Completed
One-minute schedule	Completed
Last-15-minute search window	Completed
Splunk App for SOAR Export	Completed
SOAR endpoint configuration	Completed
Token authentication	Completed
Event and artifact labels	Completed
Field mappings	Completed
Automatic event forwarding	Completed
SOAR event creation	Verified
SOAR artifact availability	Verified

Part 4 conclusion
Splunk Enterprise successfully detected failed SSH authentication events and automatically forwarded them to Splunk SOAR.

The integration transferred the event timestamp, source hostname, source IP address and attempted username into the SOAR investigation.

The confirmed source IP 193.32.162.15 and username root were subsequently used by the playbook for threat intelligence enrichment.

The next part documents the SOAR container and artifact structure and how the forwarded event became an investigation record.

End of Part 4. Send 'Continue' for Part 5.

PART 5 – SPLUNK SOAR CONTAINERS, ARTIFACTS & AUTOMATIC EVENT PROCESSING
SOC Lab Implementation Documentation · Part 5 of 10

This part documents how the security events forwarded from Splunk Enterprise were represented inside Splunk SOAR.

It explains the difference between containers and artifacts, the actual investigation created during the laboratory test, the CEF field mappings and how the Python playbook retrieved the source IP address.

18. Splunk SOAR Overview
18.1 Introduction
Splunk SOAR is the Security Orchestration, Automation and Response platform used in this project.

It receives security events from Splunk Enterprise and provides an environment for automated investigation and response.

In this laboratory, SOAR is responsible for:
1. Receiving failed SSH login events.
2. Creating investigation containers.
3. Storing event information as artifacts.
4. Extracting relevant event fields.
5. Executing the Python playbook.
6. Enriching source IP addresses using VirusTotal.
7. Classifying the risk.
8. Updating container severity.
9. Creating investigation notes.
The SOAR environment runs on:
SOC-SOAR-01


Its confirmed software version is:
Splunk SOAR On-premises 6.4.1.361


The confirmed web endpoint is:
https://20.80.97.79:8443


18.2 SOAR application environment
The SOAR interface displayed the instance name:
pink-mimas-raspberry


The installation uses the unprivileged phantom operating system user.

Known installation details:
Property	Value
Platform	Splunk SOAR On-premises
Version	6.4.1.361
Operating system	RHEL 9.4
Operating user	phantom
UID	1001
GID	1001
Installation directory	/data/phantom
Web port	8443


19. SOAR Container
19.1 What is a SOAR container?
A container is the main investigation record in Splunk SOAR.

It represents a security event, alert or incident that needs investigation.

A container groups the information associated with an investigation, including:
- Event information.
- Associated artifacts.
- Investigation notes.
- Severity.
- Status.
- Owner.
- Playbook execution history.
A container provides the overall context for the investigation.

For this laboratory, a failed SSH login detection forwarded from Splunk Enterprise becomes a SOAR investigation.
19.2 Why containers were required
A raw Splunk event contains information about an authentication attempt.

However, the SOC investigation requires more than simply storing the raw event.

The analyst needs a place to review:
- What happened.
- Which system generated the event.
- Which account was targeted.
- Which IP address originated the attempt.
- What VirusTotal reported.
- What risk classification was assigned.
- What automated actions were performed.
The container provides this investigation context.
19.3 Actual SOAR container created during the test
The successful playbook test used the following container:
Property	Confirmed value
Container ID	723
Event ID in execution record	723
Event name	SOAR - Failed SSH Login Events
Owner	Unassigned
Status	New
Initial/observed artifact severity	Medium
Updated container severity	High
Investigation notes	1

The container was opened in the SOAR interface after the successful playbook execution.

The interface showed the event name:
SOAR - Failed SSH Login Events


The container also displayed one investigation note with the title:
Automated SSH Login Investigation


19.4 Container severity
Container severity represents the assessed seriousness of the investigation.

In this project, the playbook updated the container severity based on the VirusTotal malicious detection count.

The confirmed test produced a HIGH risk classification.

The playbook then called:
phantom.set_severity(container, risk_level.lower())



The execution log confirmed that the container severity update succeeded.

The SOAR interface showed the container severity as HIGH.
19.5 Container severity versus artifact severity
An important observation was made during the verification process.

The container displayed HIGH severity, while the artifact row displayed MEDIUM severity.

These are separate fields.
Field	Observed value
Container severity	HIGH
Artifact severity	MEDIUM

The playbook's verified operation updated the container severity.

It did not establish that the artifact severity was also changed.

Therefore, the two values should not be described as identical or as having both been updated by the playbook.

This distinction is important when reviewing SOAR investigations.

20. SOAR Artifact
20.1 What is an artifact?
An artifact is an individual piece of evidence associated with a SOAR container.

A container represents the overall investigation.

An artifact contains the structured information associated with an event.

For example, a failed SSH login event can provide:
- Source IP address.
- Destination username.
- Source hostname.
- Event timestamp.
- Raw event details.
These values become available to the playbook for processing.
20.2 Why artifacts were required
The VirusTotal enrichment workflow requires a source IP address.

The playbook must retrieve the IP address from the event data before calling the VirusTotal API.

The SOAR artifact provides the structured CEF fields used by the playbook.

The processing sequence is:
SOAR Container
      |
      v
SOAR Artifact
      |
      v
CEF Fields
      |
      v
Extract Source IP
      |
      v
VirusTotal API


20.3 Actual event information
The confirmed successful investigation used the following event information:
Event field	Value
Source IP	193.32.162.15
Username	root
Hostname	splunktest
Event name	SOAR - Failed SSH Login Events
Container ID	723

20.4 CEF field mapping
CEF stands for Common Event Format.

It is a structured event representation used to standardise security event information.

The integration maps Splunk search fields to SOAR CEF fields.

The confirmed mapping is:
Splunk field	SOAR CEF field	Purpose
_time	rt	Event timestamp
host	deviceHostname	Source hostname
src_ip	sourceAddress	Source IP address
username	destinationUserName	Targeted username

The source IP is especially important because it becomes the input for VirusTotal.
20.5 Artifact labels
The configured artifact label is:
ssh_failed_login


The event label is:
events


These labels help identify the type of event and artifact being forwarded into SOAR.

The exact full artifact JSON representation is not explicitly available in the conversation.
20.6 How the playbook retrieves the artifact data
The playbook uses phantom.collect2() to retrieve the artifact CEF values.

The confirmed extraction paths are:
artifact:*.cef.sourceAddressartifact:*.cef.destinationUserNameartifact:*.cef.deviceHostnameartifact:*.cef.destinationUserId



These paths identify the artifact fields required by the investigation logic.
Source IP extraction
artifact:*.cef.sourceAddress



This retrieves the source IP address.
Username extraction
artifact:*.cef.destinationUserName



This retrieves the targeted username.
Hostname extraction
artifact:*.cef.deviceHostname



This retrieves the hostname of the Linux machine.
Destination user ID extraction
artifact:*.cef.destinationUserId



This retrieves the destination user ID if the corresponding artifact field is populated.

The actual test confirmed the source IP, username and hostname values. A populated destination user ID was not explicitly confirmed.

21. Artifact Collection Using phantom.collect2()
21.1 Purpose of phantom.collect2()
phantom.collect2() is a Splunk SOAR playbook utility used to collect data from containers and artifacts.

In this implementation, it retrieves the structured CEF fields from the artifacts associated with the investigation.

The playbook uses:
phantom.collect2(..., scope="all")



The scope="all" setting instructs the collection operation to include the relevant available artifact data across the scope of the container.
21.2 Data extraction requirements
The playbook needs four fields:
Field	Required for
sourceAddress	VirusTotal IP lookup
destinationUserName	Investigation context
deviceHostname	Identifying the monitored Linux system
destinationUserId	Additional user context when available

The extraction paths recorded in the implementation are:
artifact:*.cef.sourceAddress
artifact:*.cef.destinationUserName
artifact:*.cef.deviceHostname
artifact:*.cef.destinationUserId


21.3 Actual extracted data
The latest successful playbook execution recorded:
Source IP: [['193.32.162.15']]
Username: [['root']]
Hostname: [['splunktest']]


The nested list representation is part of the execution log output.

The playbook uses the collected values to perform the VirusTotal lookup and prepare the investigation note.
21.4 Why extraction happens before enrichment
The playbook cannot perform an IP reputation lookup until it knows which IP address to investigate.

Therefore, artifact collection is a prerequisite for VirusTotal enrichment.

The order is:
1. Receive the SOAR container.
2. Read the associated artifacts.
3. Extract the source IP.
4. Extract the username.
5. Extract the hostname.
6. Retrieve the VirusTotal API key.
7. Call the VirusTotal API.
This sequence ensures that the API request is associated with the correct security event.
21.5 Handling missing event information
The available successful execution confirms that the required source IP, username and hostname were present.

The complete missing-field handling logic is not explicitly available in the conversation.

For production implementations, missing or malformed source IP values should be handled explicitly before making an external API request.

22. SOAR Investigation Record
22.1 Investigation overview
The SOAR investigation record brings together the event, artifact, playbook results, severity and analyst-facing evidence.

The successful test used container ID 723.

The playbook completed the VirusTotal lookup and created an investigation note inside that container.
22.2 Actual container information
Container ID: 723

Event Name:
SOAR - Failed SSH Login Events

Status:
New

Owner:
Unassigned


22.3 Actual artifact information
Hostname:
splunktest

Username:
root

Source IP:
193.32.162.15


22.4 Investigation evidence
The automated investigation note contained:
- Source IP address.
- Username.
- Hostname.
- VirusTotal reputation.
- Malicious detection count.
- Suspicious detection count.
- Harmless detection count.
- Undetected count.
- Risk level.
- Risk reason.
- Recommendation for additional analyst review.
22.5 Why this investigation structure is useful
A SOC analyst should not need to reconstruct every detail manually.

The container provides the investigation record.

The artifact preserves the event data.

The investigation note summarises the enrichment results.

The severity provides a quick indication of the assessed risk.

This structure helps analysts prioritise their work and understand why an event was classified as HIGH risk.

23. SOAR Container and Artifact Verification
23.1 Verification procedure
The verification was performed through the Splunk SOAR interface.

The relevant event/container was opened after the successful playbook execution.

The interface displayed:
- Event name: SOAR - Failed SSH Login Events.
- Container status: New.
- Owner: Unassigned.
- Notes count: 1.
- Investigation note title: Automated SSH Login Investigation.
The artifact row also displayed the event evidence.
23.2 Verified container and artifact values
Validation item	Result
SOAR container created	Confirmed
Container ID 723	Confirmed
Event name	Confirmed
Artifact available	Confirmed
Source IP extracted	Confirmed
Username extracted	Confirmed
Hostname extracted	Confirmed
Container severity updated to HIGH	Confirmed
Investigation note count	1
Investigation note title	Confirmed

23.3 Severity distinction observed during verification
The SOAR interface showed:
- Container severity: HIGH.
- Artifact severity: MEDIUM.
The playbook log explicitly confirmed the container severity update.

The artifact severity remained a separate value and was not verified as updated.

This distinction must be preserved when reviewing the implementation.
23.4 Final result
Container and artifact processing completed
The SOAR container and artifact workflow operated successfully.

The playbook retrieved the expected event fields and used the source IP for the external VirusTotal lookup.

24. Part 5 Final Implementation Summary
The following components were successfully implemented and verified:
Component	Final status
SOAR event reception	Completed
Container creation	Completed
Artifact availability	Completed
Event label events	Configured
Artifact label ssh_failed_login	Configured
Source IP field mapping	Configured
Username field mapping	Configured
Hostname field mapping	Configured
phantom.collect2() extraction	Executed successfully
Container ID 723	Verified
Source IP extraction	Verified
Username extraction	Verified
Hostname extraction	Verified

Part 5 conclusion
Splunk SOAR successfully received the failed SSH login event and created the investigation record.

The event data was represented through a container and artifact structure. The Python playbook collected the CEF fields using phantom.collect2().

The actual test event contained:
Source IP : 193.32.162.15
Username  : root
Hostname  : splunktest


These values became the inputs for the VirusTotal threat intelligence integration.

The next part documents the VirusTotal API configuration, the successful IP reputation lookup and the threat intelligence results.

End of Part 5. Send 'Continue' for Part 6.

# **PART 6 – VIRUSTOTAL INTEGRATION & THREAT INTELLIGENCE ENRICHMENT**

SOC Lab Implementation Documentation · Part 6 of 10

This part documents the VirusTotal integration implemented in Splunk SOAR.

It explains how the API key was configured, how the Python playbook communicated with VirusTotal, how the source IP address was extracted and submitted to the API, and how the returned threat intelligence information was used for risk classification.

The documentation reflects the actual implementation: the VirusTotal API was called directly from the Python playbook using the `requests` library. A dedicated VirusTotal SOAR application was not installed in this implementation.

# **25. VirusTotal Integration**

## **25.1 Introduction**

VirusTotal is an online threat intelligence platform that provides information about files, URLs, domains and IP addresses.

In this SOC laboratory, VirusTotal is used to investigate the source IP address associated with a failed SSH authentication event.

The purpose is to enrich the original authentication event with external threat intelligence.

A failed SSH login tells the SOC team that an authentication attempt was unsuccessful.

However, it does not independently establish whether the source IP is associated with malicious activity.

VirusTotal provides additional reputation and analysis information that can help the playbook determine the investigation risk level.

## **25.2 Why VirusTotal was integrated**

The original Linux authentication event contains information such as:

```
Username: root
Source IP: 193.32.162.15
Hostname: splunktest
```

The source IP is the key input for the threat intelligence lookup.

The playbook uses this address to query VirusTotal.

The returned information includes:

- IP reputation.
- Malicious analysis count.
- Suspicious analysis count.
- Harmless analysis count.
- Undetected analysis count.

These values provide additional context for the security investigation.

## **25.3 Integration architecture**

The implementation uses direct REST API communication from the SOAR playbook.

```
Splunk Enterprise
       |
       v
SOAR Container
       |
       v
SOAR Artifact
       |
       v
Extract sourceAddress
       |
       v
Python Playbook
       |
       v
VirusTotal REST API
       |
       v
IP Reputation Response
       |
       v
Risk Classification
```

This architecture does not require the playbook to depend on a separate VirusTotal SOAR action block.

Instead, the playbook sends an HTTP request directly to the VirusTotal API.

## **25.4 VirusTotal API endpoint**

The confirmed API endpoint pattern is:

```
https://www.virustotal.com/api/v3/ip_addresses/{ip}
```

The `{ip}` portion is replaced with the source IP extracted from the SOAR artifact.

For the successful test, the IP address was:

```
193.32.162.15
```

Therefore, the API request targeted the corresponding IP address resource.

The endpoint uses HTTPS, protecting the API request in transit when certificate validation is correctly configured.

## **25.5 API authentication**

VirusTotal API authentication uses an API key.

The playbook includes the API key in the HTTP request header.

The confirmed request headers are:

```
headers = {    "x-apikey": api_key,    "Accept": "application/json"}
```

Explanation:

| Header                     | Purpose                       |
| -------------------------- | ----------------------------- |
| `x-apikey`                 | Authenticates the API request |
| `Accept: application/json` | Requests a JSON response      |

The actual API key is confidential and is intentionally excluded from this document.

# **26. VirusTotal API Key Configuration in SOAR**

## **26.1 Secret storage**

The API key was stored in the Splunk SOAR environment settings.

The environment variable name is:

```
VIRUSTOTAL_API_KEY
```

The actual value is:

```
[REDACTED]
```

This approach avoids hardcoding the API key directly into the playbook source code.

## **26.2 Retrieving the API key**

The playbook reads the environment variable using:

```
os.environ.get("VIRUSTOTAL_API_KEY")
```

This retrieves the configured API key from the environment.

The playbook also checks whether the key is available.

The successful execution log confirmed:

```
VirusTotal API key configured: True
```

This confirms that the playbook was able to retrieve the configured environment variable.

## **26.3 Why environment-based secret storage was used**

API keys are sensitive authentication credentials.

Storing the key directly in source code can expose it through:

- Playbook code reviews.
- Screenshots.
- Source-code exports.
- Debugging output.
- Shared documentation.

Using an environment variable separates the secret from the application logic.

The actual API key is not displayed in the execution log.

## **26.4 API key validation**

The following validation was recorded during the successful playbook execution:

```
VirusTotal API key configured: True
```

This indicates that the environment variable was available to the running playbook.

A successful API response provided additional confirmation that the configured credential was accepted by VirusTotal.

# **27. VirusTotal API Request Implementation**

## **27.1 Python requests library**

The playbook uses Python's `requests` library to communicate with the VirusTotal API.

The request method is:

```
requests.get()
```

This sends an HTTP GET request to retrieve the reputation information for the supplied IP address.

## **27.2 Request implementation**

The confirmed API interaction uses the following structure:

```
import osimport requestsapi_key = os.environ.get("VIRUSTOTAL_API_KEY")ip = "SOURCE_IP"url = f"https://www.virustotal.com/api/v3/ip_addresses/{ip}"headers = {    "x-apikey": api_key,    "Accept": "application/json"}response = requests.get(    url,    headers=headers,    timeout=20)
```

This is a simplified representation of the API interaction, using a placeholder IP rather than a hardcoded production value.

The actual playbook obtains the IP from the SOAR artifact.

## **27.3 Request parameters**

| Parameter             | Confirmed implementation    |
| --------------------- | --------------------------- |
| HTTP method           | GET                         |
| API host              | `www.virustotal.com`        |
| API path              | `/api/v3/ip_addresses/{ip}` |
| Authentication        | API key                     |
| Authentication header | `x-apikey`                  |
| Response format       | JSON                        |
| Request timeout       | 20 seconds                  |

## **27.4 Timeout configuration**

The request uses:

```
timeout=20
```

This limits the time the playbook waits for the API response.

Timeout handling is important because an external API may be unavailable or slow.

The exact error-handling implementation for every possible timeout or connection failure is not explicitly available in the conversation.

## **27.5 HTTP response validation**

The successful execution log recorded:

```
VirusTotal HTTP Status: 200
```

HTTP status `200` indicates that the API request was successfully processed.

The playbook then extracted the relevant information from the JSON response.

## **27.6 Response structure**

VirusTotal returns JSON data for the requested IP address.

The playbook reads the analysis statistics and reputation information.

The relevant response values used in the confirmed test were:

```
Reputation: -9
Malicious: 16
Suspicious: 3
Harmless: 44
Undetected: 29
```

The complete raw JSON response was not preserved in the conversation.

# **28. VirusTotal Response Handling**

## **28.1 Information returned by VirusTotal**

The playbook extracts the following information from the VirusTotal response:

| Response field | Meaning                                                                          |
| -------------- | -------------------------------------------------------------------------------- |
| `reputation`   | Aggregate reputation score associated with the IP                                |
| `malicious`    | Number of analysis engines reporting malicious activity                          |
| `suspicious`   | Number of analysis engines reporting suspicious activity                         |
| `harmless`     | Number of analysis engines reporting harmless activity                           |
| `undetected`   | Number of analysis engines that did not detect the IP as malicious or suspicious |

These statistics provide different perspectives on the reputation of the source IP.

## **28.2 Actual VirusTotal response from the successful test**

The confirmed playbook execution produced the following output:

INVESTIGATED SOURCE IP

# **193.32.162.15**

HIGH risk

Reputation

# **-9**

Malicious

# **16**

Suspicious

# **3**

Harmless

# **44**

Undetected

# **29**

HTTP response

# **200**

These values are taken from the latest successful playbook execution.

## **28.3 Interpretation of the response**

### **Reputation: -9**

The API returned a reputation value of:

```
-9
```

This value indicates negative reputation information in the returned VirusTotal data.

The playbook records this value in its investigation results.

### **Malicious: 16**

The API returned:

```
16
```

malicious detections.

This was the primary value used by the implemented risk classification thresholds.

Because the malicious count was greater than or equal to 10, the playbook classified the IP as HIGH risk.

### **Suspicious: 3**

The API returned:

```
3
```

suspicious detections.

This information was included in the investigation results.

The exact implementation does not establish a separate suspicious-count threshold for risk classification.

### **Harmless: 44**

The API returned:

```
44
```

harmless detections.

This value was retained as part of the enrichment information.

### **Undetected: 29**

The API returned:

```
29
```

undetected results.

This value was also included in the investigation results.

## **28.4 Why all statistics matter**

A threat intelligence response should not be interpreted using only one value without considering the broader context.

For this implementation:

- Malicious detections drive the configured risk classification.
- Suspicious detections provide additional context.
- Harmless detections show the number of engines reporting no malicious finding.
- Undetected detections show the number of engines that did not provide a malicious or suspicious detection.
- Reputation provides an additional aggregate indicator.

The implemented classification uses the malicious count as its primary threshold.

This does not mean that a VirusTotal count independently proves that a system was compromised.

The final security assessment should consider the authentication event, network context, other telemetry and analyst review.

## **28.5 VirusTotal API execution result**

The latest successful execution log contained:

```
VirusTotal HTTP Status: 200
Reputation: -9
Malicious: 16
Suspicious: 3
Harmless: 44
Undetected: 29
```

This confirms that:

1. The playbook retrieved the API key.
2. The source IP was extracted.
3. The API request was submitted.
4. VirusTotal returned an HTTP success response.
5. The playbook parsed the relevant statistics.

VirusTotal enrichment completed successfully

# **29. VirusTotal Integration Troubleshooting and Security**

## **29.1 Python requests library validation warning**

During playbook development, the Python validation interface displayed a warning indicating that the `requests` library was not recommended.

The library was nevertheless used in the custom Python playbook.

The latest execution confirmed:

```
VirusTotal HTTP Status: 200
```

The API call completed successfully, and the playbook continued through classification and investigation note creation.

Therefore, the warning did not prevent successful execution in the final tested version.

The exact original warning text is not explicitly available in the conversation.

## **29.2 VirusTotal application versus direct API integration**

An important implementation distinction is that the VirusTotal integration was not performed using a dedicated VirusTotal SOAR app action.

Instead, the Python playbook directly called the VirusTotal REST API.

| Integration method               | Project implementation |
| -------------------------------- | ---------------------- |
| Dedicated VirusTotal SOAR app    | Not installed          |
| VirusTotal asset-based action    | Not used               |
| Python `requests`                | Used                   |
| Direct REST API                  | Used                   |
| Environment variable for API key | Used                   |
| API key in request header        | Used                   |
| Successful API response          | HTTP 200               |

This distinction is important for technical documentation and interviews.

The project demonstrates a direct API integration from SOAR, not a prebuilt VirusTotal application workflow.

## **29.3 Secret management**

The API key was stored as:

```
VIRUSTOTAL_API_KEY
```

The value is redacted.

The playbook retrieves the key through the environment rather than storing the credential directly in the source code.

Production recommendations include:

- Use a dedicated secret-management system where appropriate.
- Restrict who can access SOAR environment settings.
- Avoid logging credentials.
- Rotate API keys periodically.
- Apply suitable API usage limits.
- Monitor API failures and rate-limit responses.

## **29.4 External API dependency**

The VirusTotal enrichment process depends on outbound connectivity from the SOAR VM.

The API uses HTTPS on TCP port `443`.

The playbook request has a 20-second timeout.

If VirusTotal is unavailable, the enrichment step may not return the expected data.

The exact fallback logic for API timeouts, HTTP error responses and rate-limit conditions is not fully preserved in the conversation.

For a production implementation, such failures should be handled explicitly so that the overall investigation remains available even when external threat intelligence services are unavailable.

# **30. VirusTotal Integration Validation**

## **30.1 End-to-end validation sequence**

The successful playbook test verified the following sequence:

| Step | Validation                    | Actual result   |
| ---- | ----------------------------- | --------------- |
| 1    | Read API key from environment | `True`          |
| 2    | Extract source IP             | `193.32.162.15` |
| 3    | Extract username              | `root`          |
| 4    | Extract hostname              | `splunktest`    |
| 5    | Submit VirusTotal request     | Completed       |
| 6    | Receive HTTP response         | `200`           |
| 7    | Extract reputation            | `-9`            |
| 8    | Extract malicious count       | `16`            |
| 9    | Extract suspicious count      | `3`             |
| 10   | Extract harmless count        | `44`            |
| 11   | Extract undetected count      | `29`            |
| 12   | Determine risk level          | HIGH            |

## **30.2 Final VirusTotal integration status**

COMPLETED SUCCESSFULLY

The VirusTotal integration is fully operational in the tested playbook.

The API key was available to the playbook, the external API returned HTTP 200, and the response statistics were successfully extracted.

The malicious detection count of 16 was passed into the risk classification logic and resulted in HIGH risk.

# **31. Part 6 Final Summary**

The VirusTotal integration provides external threat intelligence enrichment for the failed SSH login investigation.

The playbook extracts the source IP address from the SOAR artifact and queries the VirusTotal IP address API using Python.

The API key is stored in the SOAR environment settings under `VIRUSTOTAL_API_KEY` and is retrieved at runtime.

The successful test investigated:

```
193.32.162.15
```

The returned results were:

```
HTTP Status : 200
Reputation  : -9
Malicious   : 16
Suspicious  : 3
Harmless    : 44
Undetected  : 29
```

The malicious detection count resulted in a HIGH risk classification.

The VirusTotal integration was completed and successfully validated before the project moved to the remaining Gmail SMTP configuration stage.

## **Part 6 validation checklist**

- VirusTotal API integration implemented.
- API key stored in SOAR environment settings.
- API key retrieved using `os.environ.get()`.
- Source IP extracted from SOAR artifact.
- Direct VirusTotal REST API request implemented.
- API request authenticated using `x-apikey`.
- HTTP response received successfully.
- Reputation value extracted.
- Malicious detection count extracted.
- Suspicious detection count extracted.
- Harmless detection count extracted.
- Undetected count extracted.
- HIGH risk result produced from the malicious detection threshold.

**End of Part 6. Send 'Continue' for Part 7.**

# **PART 7 – SOAR PLAYBOOK DEVELOPMENT & AUTOMATED RESPONSE**

## **7.1. Introduction**

The main objective of this phase was to automate the investigation of failed SSH login events received from Splunk Enterprise.

Instead of manually reviewing every suspicious source IP, the Splunk SOAR playbook performs the following operations:

1. Receives the forwarded SSH failed-login event.
2. Extracts the source IP, username, and hostname from the SOAR artifact.
3. Queries the VirusTotal API using the source IP.
4. Retrieves the IP reputation and detection statistics.
5. Classifies the source IP as HIGH, MEDIUM, or LOW risk.
6. Updates the SOAR container severity.
7. Creates an automated investigation note.
8. Records the playbook execution result.

The playbook was successfully executed and verified.

## **7.2. Playbook Overview**

**Playbook configuration**

Execution successful

| Property           | Confirmed value              |
| ------------------ | ---------------------------- |
| Playbook name      | `SOC_Invalid_Login_Response` |
| Latest playbook ID | `177`                        |
| Latest version     | `13`                         |
| Execution mode     | Custom whole-playbook Python |
| Successful run ID  | `723`                        |
| Container ID       | `723`                        |
| Execution date     | 18 November 2026             |
| Execution time     | 23:02:35                     |
| Result             | Success                      |

The latest recorded successful execution was on 18 November 2026 at 23:02:35.

The playbook was developed using the custom Python execution mode in Splunk SOAR.

### **Why was custom Python mode used?**

Splunk SOAR supports different approaches to playbook development. In this implementation, the playbook logic was written in Python rather than assembled using visual action blocks.

When the entire playbook is implemented in custom Python mode, visual canvas controls may be unavailable or greyed out. This is expected for the selected development mode.

Therefore, the automation was implemented and maintained through Python code. Drag-and-drop visual actions were not required for this implementation.

## **7.3. Playbook Execution Architecture**

The following diagram represents the confirmed automation sequence.

\#chatgpt-mermaid-\_r_dfp\_{font-family:-apple-system-body,ui-sans-serif,-apple-system,system-ui,"Segoe UI",Helvetica,"Apple Color Emoji",Arial,sans-serif,"Segoe UI Emoji","Segoe UI Symbol";font-size:16px;fill:rgb(237, 237, 237);}@keyframes edge-animation-frame{from{stroke-dashoffset:0;}}@keyframes dash{to{stroke-dashoffset:0;}}#chatgpt-mermaid-\_r_dfp\_ .edge-animation-slow{stroke-dasharray:9,5!important;stroke-dashoffset:900;animation:dash 50s linear infinite;stroke-linecap:round;}#chatgpt-mermaid-\_r_dfp\_ .edge-animation-fast{stroke-dasharray:9,5!important;stroke-dashoffset:900;animation:dash 20s linear infinite;stroke-linecap:round;}#chatgpt-mermaid-\_r_dfp\_ .error-icon{fill:rgb(48, 48, 48);}#chatgpt-mermaid-\_r_dfp\_ .error-text{fill:rgb(237, 237, 237);stroke:rgb(237, 237, 237);}#chatgpt-mermaid-\_r_dfp\_ .edge-thickness-normal{stroke-width:1px;}#chatgpt-mermaid-\_r_dfp\_ .edge-thickness-thick{stroke-width:3.5px;}#chatgpt-mermaid-\_r_dfp\_ .edge-pattern-solid{stroke-dasharray:0;}#chatgpt-mermaid-\_r_dfp\_ .edge-thickness-invisible{stroke-width:0;fill:none;}#chatgpt-mermaid-\_r_dfp\_ .edge-pattern-dashed{stroke-dasharray:3;}#chatgpt-mermaid-\_r_dfp\_ .edge-pattern-dotted{stroke-dasharray:2;}#chatgpt-mermaid-\_r_dfp\_ .marker{fill:rgb(175, 175, 175);stroke:rgb(175, 175, 175);}#chatgpt-mermaid-\_r_dfp\_ .marker.cross{stroke:rgb(175, 175, 175);}#chatgpt-mermaid-\_r_dfp\_ svg{font-family:-apple-system-body,ui-sans-serif,-apple-system,system-ui,"Segoe UI",Helvetica,"Apple Color Emoji",Arial,sans-serif,"Segoe UI Emoji","Segoe UI Symbol";font-size:16px;}#chatgpt-mermaid-\_r_dfp\_ p{margin:0;}#chatgpt-mermaid-\_r_dfp\_ .label{font-family:-apple-system-body,ui-sans-serif,-apple-system,system-ui,"Segoe UI",Helvetica,"Apple Color Emoji",Arial,sans-serif,"Segoe UI Emoji","Segoe UI Symbol";color:rgb(237, 237, 237);}#chatgpt-mermaid-\_r_dfp\_ .cluster-label text{fill:rgb(237, 237, 237);}#chatgpt-mermaid-\_r_dfp\_ .cluster-label span{color:rgb(237, 237, 237);}#chatgpt-mermaid-\_r_dfp\_ .cluster-label span p{background-color:transparent;}#chatgpt-mermaid-\_r_dfp\_ .label text,#chatgpt-mermaid-\_r_dfp\_ span{fill:rgb(237, 237, 237);color:rgb(237, 237, 237);}#chatgpt-mermaid-\_r_dfp\_ .node rect,#chatgpt-mermaid-\_r_dfp\_ .node circle,#chatgpt-mermaid-\_r_dfp\_ .node ellipse,#chatgpt-mermaid-\_r_dfp\_ .node polygon,#chatgpt-mermaid-\_r_dfp\_ .node path{fill:rgb(9, 23, 44);stroke:rgb(31, 78, 148);stroke-width:1px;}#chatgpt-mermaid-\_r_dfp\_ .rough-node .label text,#chatgpt-mermaid-\_r_dfp\_ .node .label text,#chatgpt-mermaid-\_r_dfp\_ .image-shape .label,#chatgpt-mermaid-\_r_dfp\_ .icon-shape .label{text-anchor:middle;}#chatgpt-mermaid-\_r_dfp\_ .node .katex path{fill:#000;stroke:#000;stroke-width:1px;}#chatgpt-mermaid-\_r_dfp\_ .rough-node .label,#chatgpt-mermaid-\_r_dfp\_ .node .label,#chatgpt-mermaid-\_r_dfp\_ .image-shape .label,#chatgpt-mermaid-\_r_dfp\_ .icon-shape .label{text-align:center;}#chatgpt-mermaid-\_r_dfp\_ .node.clickable{cursor:pointer;}#chatgpt-mermaid-\_r_dfp\_ .root .anchor path{fill:rgb(175, 175, 175)!important;stroke-width:0;stroke:rgb(175, 175, 175);}#chatgpt-mermaid-\_r_dfp\_ .arrowheadPath{fill:rgb(175, 175, 175);}#chatgpt-mermaid-\_r_dfp\_ .edgePath .path{stroke:rgb(175, 175, 175);stroke-width:1px;}#chatgpt-mermaid-\_r_dfp\_ .flowchart-link{stroke:rgb(175, 175, 175);fill:none;}#chatgpt-mermaid-\_r_dfp\_ .edgeLabel{background-color:rgb(0, 0, 0);text-align:center;}#chatgpt-mermaid-\_r_dfp\_ .edgeLabel p{background-color:rgb(0, 0, 0);}#chatgpt-mermaid-\_r_dfp\_ .edgeLabel rect{opacity:0.5;background-color:rgb(0, 0, 0);fill:rgb(0, 0, 0);}#chatgpt-mermaid-\_r_dfp\_ .labelBkg{background-color:rgba(0, 0, 0, 0.5);}#chatgpt-mermaid-\_r_dfp\_ .cluster rect{fill:rgb(48, 48, 48);stroke:rgba(255, 255, 255, 0.15);stroke-width:1px;}#chatgpt-mermaid-\_r_dfp\_ .cluster text{fill:rgb(237, 237, 237);}#chatgpt-mermaid-\_r_dfp\_ .cluster span{color:rgb(237, 237, 237);}#chatgpt-mermaid-\_r_dfp\_ div.mermaidTooltip{position:absolute;text-align:center;max-width:200px;padding:2px;font-family:-apple-system-body,ui-sans-serif,-apple-system,system-ui,"Segoe UI",Helvetica,"Apple Color Emoji",Arial,sans-serif,"Segoe UI Emoji","Segoe UI Symbol";font-size:12px;background:rgb(48, 48, 48);border:1px solid rgba(255, 255, 255, 0.15);border-radius:2px;pointer-events:none;z-index:100;}#chatgpt-mermaid-\_r_dfp\_ .flowchartTitleText{text-anchor:middle;font-size:18px;fill:rgb(237, 237, 237);}#chatgpt-mermaid-\_r_dfp\_ rect.text{fill:none;stroke-width:0;}#chatgpt-mermaid-\_r_dfp\_ .icon-shape,#chatgpt-mermaid-\_r_dfp\_ .image-shape{background-color:rgb(0, 0, 0);text-align:center;}#chatgpt-mermaid-\_r_dfp\_ .icon-shape p,#chatgpt-mermaid-\_r_dfp\_ .image-shape p{background-color:rgb(0, 0, 0);padding:2px;}#chatgpt-mermaid-\_r_dfp\_ .icon-shape .label rect,#chatgpt-mermaid-\_r_dfp\_ .image-shape .label rect{opacity:0.5;background-color:rgb(0, 0, 0);fill:rgb(0, 0, 0);}#chatgpt-mermaid-\_r_dfp\_ .label-icon{display:inline-block;height:1em;overflow:visible;vertical-align:-0.125em;}#chatgpt-mermaid-\_r_dfp\_ .node .label-icon path{fill:currentColor;stroke:revert;stroke-width:revert;}#chatgpt-mermaid-\_r_dfp\_ .node .neo-node{stroke:rgb(31, 78, 148);}#chatgpt-mermaid-\_r_dfp\_ [data-look="neo"].node rect,#chatgpt-mermaid-\_r_dfp\_ [data-look="neo"].cluster rect,#chatgpt-mermaid-\_r_dfp\_ [data-look="neo"].node polygon{stroke:url(#chatgpt-mermaid-\_r_dfp\_-gradient);filter:drop-shadow( 1px 2px 2px rgba(185,185,185,1));}#chatgpt-mermaid-\_r_dfp\_ [data-look="neo"].swimlane.cluster rect{filter:none;}#chatgpt-mermaid-\_r_dfp\_ [data-look="neo"].node path{stroke:url(#chatgpt-mermaid-\_r_dfp\_-gradient);stroke-width:1px;}#chatgpt-mermaid-\_r_dfp\_ [data-look="neo"].node .outer-path{filter:drop-shadow( 1px 2px 2px rgba(185,185,185,1));}#chatgpt-mermaid-\_r_dfp\_ [data-look="neo"].node .neo-line path{stroke:rgb(31, 78, 148);filter:none;}#chatgpt-mermaid-\_r_dfp\_ [data-look="neo"].node circle{stroke:url(#chatgpt-mermaid-\_r_dfp\_-gradient);filter:drop-shadow( 1px 2px 2px rgba(185,185,185,1));}#chatgpt-mermaid-\_r_dfp\_ [data-look="neo"].node circle .state-start{fill:#000000;}#chatgpt-mermaid-\_r_dfp\_ [data-look="neo"].icon-shape .icon{fill:url(#chatgpt-mermaid-\_r_dfp\_-gradient);filter:drop-shadow( 1px 2px 2px rgba(185,185,185,1));}#chatgpt-mermaid-\_r_dfp\_ [data-look="neo"].icon-shape .icon-neo path{stroke:url(#chatgpt-mermaid-\_r_dfp\_-gradient);filter:drop-shadow( 1px 2px 2px rgba(185,185,185,1));}#chatgpt-mermaid-\_r_dfp\_ .node text{font-size:14px;font-weight:600;letter-spacing:normal;fill:rgb(153, 206, 255);}#chatgpt-mermaid-\_r_dfp\_ .edgeLabels text{font-size:13px;font-weight:600;letter-spacing:-0.08px;fill:rgb(153, 206, 255);}#chatgpt-mermaid-\_r_dfp\_ .node tspan[font-weight="normal"],#chatgpt-mermaid-\_r_dfp\_ .edgeLabels tspan[font-weight="normal"]{font-weight:600;}#chatgpt-mermaid-\_r_dfp\_ .edgeLabel .label rect{opacity:1;rx:13px;ry:13px;fill:rgb(0, 14, 26);stroke:rgb(26, 62, 95);stroke-width:1px;}#chatgpt-mermaid-\_r_dfp\_ .node rect,#chatgpt-mermaid-\_r_dfp\_ .node circle,#chatgpt-mermaid-\_r_dfp\_ .node ellipse,#chatgpt-mermaid-\_r_dfp\_ .node polygon,#chatgpt-mermaid-\_r_dfp\_ .node path{fill:rgb(0, 40, 77);stroke:rgba(255, 255, 255, 0.1);stroke-width:1px;}#chatgpt-mermaid-\_r_dfp\_ .node rect{rx:16px;ry:16px;}#chatgpt-mermaid-\_r_dfp\_ .mermaid-decision .label-container{fill:rgb(0, 14, 26);stroke:rgb(26, 62, 95);stroke-dasharray:2,2;}#chatgpt-mermaid-\_r_dfp\_ .edgePaths .flowchart-link{stroke:rgb(175, 175, 175);stroke-width:1px;stroke-linecap:round;stroke-linejoin:round;}#chatgpt-mermaid-\_r_dfp\_ .marker{fill:rgb(175, 175, 175);stroke:rgb(175, 175, 175);}#chatgpt-mermaid-\_r_dfp\_ :root{--mermaid-font-family:-apple-system-body,ui-sans-serif,-apple-system,system-ui,"Segoe UI",Helvetica,"Apple Color Emoji",Arial,sans-serif,"Segoe UI Emoji","Segoe UI Symbol";}Splunk forwards SSH eventSOAR receives event andartifactPlaybook startsExtract artifact fieldsRetrieve VirusTotal API keyQuery VirusTotal APIParse IP reputationClassify riskUpdate container severityCreate investigation noteComplete playbook execution

The email notification process is not included in this successfully verified sequence. SMTP application installation was completed separately, and Gmail SMTP asset configuration is the next stage.

## **7.4. Playbook Execution Functions**

The playbook uses the following main functions.

| Function                        | Responsibility                        |
| ------------------------------- | ------------------------------------- |
| `on_start(container)`           | Starts the playbook execution         |
| `code_2(container=container)`   | Executes the main investigation logic |
| `phantom.collect2()`            | Collects artifact information         |
| `requests.get()`                | Sends the VirusTotal API request      |
| `phantom.set_severity()`        | Updates the SOAR container severity   |
| `phantom.add_note()`            | Creates the investigation note        |
| `on_finish(container, summary)` | Handles execution completion          |

### **7.4.1. Playbook starting function**

The `on_start(container)` function is the starting point of the playbook.

Its responsibilities include:

- Receiving the SOAR container.
- Checking whether the VirusTotal API key is configured.
- Calling the main Python function.

The API key is retrieved from the SOAR environment settings. The actual API key is confidential and must not be hardcoded into the playbook or exposed in documentation.

The confirmed environment variable name is:

```
VIRUSTOTAL_API_KEY
```

The playbook retrieves it using:

```
api_key = os.environ.get("VIRUSTOTAL_API_KEY")
```

The successful execution log confirmed:

```
VirusTotal API key configured: True
```

This indicates that the playbook could retrieve the configured API key.

## **7.5. Extracting Artifact Information**

Before performing the VirusTotal lookup, the playbook needs to identify the source IP responsible for the failed SSH login.

The event artifact contains the information forwarded from Splunk Enterprise.

The confirmed artifact fields are:

| SOAR artifact field   | Value           |
| --------------------- | --------------- |
| `sourceAddress`       | `193.32.162.15` |
| `destinationUserName` | `root`          |
| `deviceHostname`      | `splunktest`    |

The playbook uses the SOAR artifact collection mechanism:

```
phantom.collect2(    container=container,    datapath=[        "artifact:*.cef.sourceAddress",        "artifact:*.cef.destinationUserName",        "artifact:*.cef.deviceHostname"    ],    scope="all")
```

This is a reference representation of the confirmed collection paths, not a claim that the original complete function call was preserved verbatim.

### **Understanding&#x20;**`phantom.collect2()`

`phantom.collect2()` retrieves information from the artifacts associated with a SOAR container.

For this investigation:

- `sourceAddress` identifies the remote IP address.
- `destinationUserName` identifies the targeted account.
- `deviceHostname` identifies the Linux machine that generated the event.

The collected source IP is then used as the input for the VirusTotal investigation.

**Extracted investigation data**

Source IP

**193.32.162.15**

Target username

**root**

Hostname

**splunktest**

## **7.6. VirusTotal API Investigation**

After extracting the source IP, the playbook queries VirusTotal.

The API endpoint pattern used was:

```
https://www.virustotal.com/api/v3/ip_addresses/{ip}
```

The HTTP request uses the API key in the request headers.

```
headers = {    "x-apikey": api_key,    "Accept": "application/json"}
```

The request is performed using Python's `requests` library with a timeout of 20 seconds.

A representative request is:

```
response = requests.get(    f"https://www.virustotal.com/api/v3/ip_addresses/{ip}",    headers=headers,    timeout=20)
```

The response is checked before its JSON content is processed.

### **Actual VirusTotal response**

The successful playbook execution returned the following values:

| Field                 | Actual result   |
| --------------------- | --------------- |
| HTTP status           | `200`           |
| Source IP             | `193.32.162.15` |
| Reputation            | `-9`            |
| Malicious detections  | `16`            |
| Suspicious detections | `3`             |
| Harmless detections   | `44`            |
| Undetected            | `29`            |

These are the values recorded in the successful playbook execution log.

### **Understanding the results**

- **HTTP 200:** The API request completed successfully.
- **Reputation -9:** VirusTotal returned a negative reputation score for the IP.
- **Malicious 16:** Sixteen security engines classified the IP as malicious.
- **Suspicious 3:** Three engines classified it as suspicious.
- **Harmless 44:** Forty-four engines classified it as harmless.
- **Undetected 29:** Twenty-nine engines did not report a detection.

A negative reputation score and multiple malicious detections provide indicators for further investigation. They do not, by themselves, prove that the IP successfully compromised the Linux server.

## **7.7. Risk Classification Logic**

After receiving the VirusTotal response, the playbook classifies the source IP based on the malicious detection count.

The confirmed thresholds are:

| Malicious detection count | Risk classification |
| ------------------------- | ------------------- |
| 10 or more                | HIGH                |
| 3–9                       | MEDIUM              |
| 0–2                       | LOW                 |

The logic can be represented as:

```
if malicious >= 10:    risk_level = "HIGH"elif malicious >= 3:    risk_level = "MEDIUM"else:    risk_level = "LOW"
```

### **Actual classification**

The VirusTotal response contained 16 malicious detections.

Therefore:

\\[ 16 \geq 10 \\]

HIGH

**The source IP was classified as HIGH risk.**

The successful execution log also recorded:

```
Risk Level: HIGH
Risk Reason: High malicious detection count
```

This confirms that the playbook applied the expected HIGH-risk classification.

## **7.8. Updating SOAR Container Severity**

After calculating the risk level, the playbook updates the severity of the SOAR container.

The confirmed function used is:

```
phantom.set_severity(    container,    risk_level.lower())
```

Since the calculated risk level was `HIGH`, the playbook passed the lowercase value `high` to the severity update function.

### **Important distinction: Container severity vs. artifact severity**

The SOAR investigation displayed different severity values at the container and artifact levels.

| Component      | Displayed severity |
| -------------- | ------------------ |
| SOAR container | HIGH               |
| Artifact       | MEDIUM             |

The playbook successfully updated the **container severity to HIGH**.

The artifact row displayed MEDIUM. There is no confirmation that the artifact severity was also changed.

These values should not be treated as interchangeable because container severity represents the overall investigation, while artifact severity is associated with an individual artifact.

## **7.9. Automated Investigation Note Creation**

After updating the container severity, the playbook creates an investigation note.

The confirmed function is:

```
phantom.add_note(    container=container,    note_type="general",    title="Automated SSH Login Investigation",    content=note_content,    note_format="markdown")
```

The note title used was:

**Automated SSH Login Investigation**

The note records the relevant investigation information, including:

- Source IP address.
- Target username.
- Linux hostname.
- VirusTotal reputation.
- Malicious, suspicious, harmless and undetected counts.
- Risk classification.
- Risk classification reason.
- Recommendation for analyst review.

### **Why is an investigation note important?**

A security analyst should be able to understand why an event was classified as HIGH without manually repeating the entire investigation.

The automated note provides a record of the investigation results directly inside the SOAR container.

It also helps with:

- Incident investigation.
- Analyst handover.
- Security audit reviews.
- Investigation history.
- Documentation of automated decisions.

### **Verification**

The investigation note was successfully created and verified in SOAR container `723`.

The successful execution log recorded:

```
===== CREATING SOAR INVESTIGATION NOTE =====
phantom.add_note() called
phantom.add_note() completed successfully
Investigation note success: True
Investigation note message: note added
Investigation note ID: 1
```



Verified

&#x20;Investigation note creation completed successfully.



## **7.10. Playbook Execution Verification**

The following is the recorded successful execution output.

```
VirusTotal API key configured: True
Source IP: [['193.32.162.15']]
Username: [['root']]
Hostname: [['splunktest']]

VirusTotal HTTP Status: 200
Reputation: -9
Malicious: 16
Suspicious: 3
Harmless: 44
Undetected: 29

Risk Level: HIGH
Risk Reason: High malicious detection count

phantom.set_severity() called
SOAR case severity updated to: HIGH

===== CREATING SOAR INVESTIGATION NOTE =====

phantom.add_note() called
phantom.add_note() completed successfully

Investigation note success: True
Investigation note message: note added
Investigation note ID: 1

Playbook execution status: success
```

### **Final verification checklist**

| Validation                       | Result     |
| -------------------------------- | ---------- |
| API key retrieved                | Successful |
| Artifact information extracted   | Successful |
| Source IP identified             | Successful |
| VirusTotal API request           | HTTP 200   |
| Reputation information retrieved | Successful |
| Risk classification              | HIGH       |
| Container severity updated       | HIGH       |
| Investigation note created       | Successful |
| Playbook execution               | Successful |

## **7.11. Understanding the Python Execution Log**

During development, the playbook execution output displayed:

```
Total actions executed: 0
```

This does not mean the playbook failed.

The implementation uses custom whole-playbook Python code rather than visual action blocks. The investigation logic executes through Python functions, so the visual action counter can remain at zero.

The actual execution result, API response, severity update, and investigation note creation are the relevant verification points.

## **7.12. Development and Troubleshooting Observations**

### **Python&#x20;**`requests`**&#x20;library warning**

During development, a validation warning related to the `requests` library was encountered.

However, the final successful execution log confirmed:

- The API key was retrieved.
- The VirusTotal endpoint returned HTTP 200.
- The response was processed.
- The risk classification was completed.
- The SOAR container severity was updated.
- The investigation note was created.

Therefore, the warning did not prevent the verified playbook execution.

For production deployment, dependency compatibility and supported application-library versions should be reviewed.

### **Visual editor controls were greyed out**

The visual playbook controls were unavailable because the implementation used custom whole-playbook Python mode.

The playbook was therefore continued using Python code. Switching to a visual workflow was not necessary to complete the verified automation.

### **TLS certificate verification**

The Splunk-to-SOAR integration used `verify_certs=0` as a temporary lab workaround.

This setting is not recommended for production because it disables certificate verification. A production deployment should use a trusted TLS certificate and enable certificate verification.

## **7.13. Current Automation Scope and Remaining Work**

The following table separates completed automation from the next planned stage.

| Capability                                | Current status       |
| ----------------------------------------- | -------------------- |
| Receive SSH failed-login events           | Completed            |
| Extract source IP and account information | Completed            |
| Query VirusTotal                          | Completed            |
| Classify IP reputation                    | Completed            |
| Update SOAR container severity            | Completed            |
| Create investigation note                 | Completed            |
| Execute and verify playbook               | Completed            |
| Install SMTP application                  | Completed            |
| Configure Gmail SMTP asset                | In progress          |
| Confirm successful email delivery         | Pending confirmation |

**Important:** SMTP application installation is complete, but Gmail SMTP asset configuration and successful email delivery have not yet been confirmed. Therefore, email notification must not be represented as a completed response action.

## **7.14. Part 7 Summary**

The `SOC_Invalid_Login_Response` playbook successfully automated the investigation of a forwarded SSH failed-login event.

It extracted the source IP `193.32.162.15`, queried VirusTotal, received 16 malicious detections, classified the IP as HIGH risk, updated the SOAR container severity, and created an investigation note.

The successful execution was recorded under run ID `723`, with the final status reported as `success`.

This completes the verified VirusTotal-based automated investigation and severity-update workflow.

**End of Part 7. Send 'Continue' for Part 8.**

# **PART 8 – TROUBLESHOOTING, VALIDATION & TECHNICAL OPERATIONS**

## **8.1. Introduction**

This section documents the troubleshooting observations, validation results, and operational considerations encountered during the development of the SOC lab.

The objective is to provide a technical reference for understanding how the environment was validated, which issues were encountered, how they were addressed, and what should be checked if similar issues occur again.

This section distinguishes between:

- **Confirmed project observations:** Issues and outcomes explicitly recorded during the implementation.
- **General troubleshooting guidance:** Additional checks that can be used to diagnose similar problems. These are recommendations, not claims that the steps were performed in this lab.

## **8.2. Troubleshooting Scope**

The project involved several components that depend on one another.

| Component                  | Main validation requirement                   |
| -------------------------- | --------------------------------------------- |
| Azure VMs                  | Instances accessible and operational          |
| Splunk Enterprise          | Web interface and receiving service available |
| Universal Forwarder        | Linux authentication logs forwarded           |
| Splunk detection search    | Failed-login events returned                  |
| Splunk-to-SOAR integration | Events and artifacts forwarded                |
| SOAR                       | Containers and artifacts created              |
| VirusTotal API             | API request returns valid response            |
| Python playbook            | Investigation logic completes                 |
| Severity update            | Container severity updated                    |
| Investigation note         | Note successfully created                     |
| SMTP application           | Installed and ready for asset configuration   |

The confirmed successful end-to-end workflow reached the VirusTotal investigation, severity update, and investigation note creation stages.

## **8.3. Azure VM and Service Availability**

The lab uses two primary Azure-hosted virtual machines and one Linux test machine.

| VM              | Operating system                             | Main purpose                                 |
| --------------- | -------------------------------------------- | -------------------------------------------- |
| `SOC-SPLUNK-01` | Ubuntu Server 24.04                          | Splunk Enterprise                            |
| `SOC-SOAR-01`   | RHEL 9.4                                     | Splunk SOAR                                  |
| `splunktest`    | Not explicitly available in the conversation | Linux log generation and Universal Forwarder |

### **Confirmed VM endpoints**

| Service                           | Endpoint                    |
| --------------------------------- | --------------------------- |
| Splunk Enterprise Web UI          | `http://20.112.235.59:8000` |
| Splunk Enterprise management port | `8089`                      |
| Splunk Enterprise receiving port  | `9997`                      |
| Splunk SOAR Web UI                | `https://20.80.97.79:8443`  |

### **General troubleshooting guidance**

If a service becomes inaccessible, validate the following:

1. Confirm the VM is running in Azure.
2. Check whether the expected private and public IP addresses are assigned.
3. Verify the relevant network security group rules.
4. Confirm that the application service is running.
5. Check the application logs for startup or connectivity errors.
6. Test access to the expected application port.

The exact Azure NSG configuration, subnet settings, and firewall rules used in this lab were not explicitly available in the conversation.

## **8.4. Splunk Enterprise Troubleshooting**

Splunk Enterprise is responsible for receiving Linux authentication logs and running the failed-login detection search.

### **Important ports**

| Port | Purpose                     |
| ---- | --------------------------- |
| 8000 | Splunk Web interface        |
| 8089 | Splunk management interface |
| 9997 | Receiving forwarded data    |

### **General validation commands**

The following commands are reference checks for a Linux-based Splunk Enterprise installation. They are not confirmed as commands executed during the original implementation.

Check the Splunk service:

```
sudo /opt/splunk/bin/splunk status
```

Check listening ports:

```
sudo ss -tulnp
```

Check whether Splunk is listening on the receiving port:

```
sudo ss -tulnp | grep 9997
```

### **Common troubleshooting scenarios**

| Symptom                         | Possible cause                  | Recommended check                  |
| ------------------------------- | ------------------------------- | ---------------------------------- |
| Web UI unavailable              | Service stopped or port blocked | Check service and port 8000        |
| Forwarder data missing          | Receiving port unavailable      | Check port 9997                    |
| Management connectivity failure | Port 8089 unavailable           | Check management service           |
| Search returns no events        | Incorrect index or time range   | Validate index and search criteria |

These are general diagnostic possibilities, not confirmed failures in the lab.

## **8.5. Universal Forwarder and Linux Log Collection**

The Linux Universal Forwarder was configured to monitor:

```
/var/log/auth.log
```

The confirmed forwarding configuration values were:

| Parameter                 | Value               |
| ------------------------- | ------------------- |
| Monitored file            | `/var/log/auth.log` |
| Destination Splunk server | `10.10.1.4`         |
| Receiving port            | `9997`              |
| Index                     | `security`          |
| Sourcetype                | `linux_secure`      |

### **General validation commands**

Check the Universal Forwarder service:

```
sudo /opt/splunkforwarder/bin/splunk status
```

Check the configured forwarding destination:

```
sudo /opt/splunkforwarder/bin/splunk list forward-server
```

Check the monitored inputs:

```
sudo /opt/splunkforwarder/bin/splunk list monitor
```

These are standard reference commands. The exact command history from the original setup was not preserved.

### **Validate the Linux authentication log**

The monitored log file was:

```
/var/log/auth.log
```

A general method for checking recent authentication events is:

```
sudo tail -f /var/log/auth.log
```

If the operating system uses a different authentication logging configuration, the actual log file may differ. The lab's confirmed monitored file was `/var/log/auth.log`.

## **8.6. Failed SSH Login Detection Troubleshooting**

The confirmed detection search was:

```
index=security sourcetype=linux_secure "Failed password"
| rex "Failed password for (invalid user )?(?<username>\S+) from (?<src_ip>\S+)"
| table _time host username src_ip _raw
```

### **Expected extracted fields**

| Field      | Actual test value |
| ---------- | ----------------- |
| `host`     | `splunktest`      |
| `username` | `root`            |
| `src_ip`   | `193.32.162.15`   |

### **Troubleshooting scenarios**

**Scenario 1: Search returns no events**

Possible checks:

- Confirm that the Linux log file contains failed-login events.
- Confirm the Universal Forwarder is running.
- Check whether the receiver is listening on port `9997`.
- Verify the index is `security`.
- Verify the sourcetype is `linux_secure`.
- Expand the search time range.

**Scenario 2: Events appear, but&#x20;**`username`**&#x20;is missing**

Check whether the event contains the expected `Failed password for ...` format and whether the regular expression matches it.

**Scenario 3: Events appear, but&#x20;**`src_ip`**&#x20;is missing**

Check the raw event for the `from` portion and validate the extraction expression.

The actual detection search successfully extracted the username and source IP during the project validation.

## **8.7. Splunk-to-SOAR Integration Troubleshooting**

The Splunk App for SOAR Export was configured to forward events to the SOAR instance.

### **Confirmed integration values**

| Parameter        | Value                            |
| ---------------- | -------------------------------- |
| SOAR URL         | `https://20.80.97.79:8443`       |
| SOAR server name | `automation`                     |
| Saved search     | `SOAR - Failed SSH Login Events` |
| Schedule         | Every 1 minute                   |
| Search window    | Last 15 minutes                  |
| Event label      | `events`                         |
| Artifact label   | `ssh_failed_login`               |

The integration used a configured token. The actual token is confidential and is not included in this document.

### **Field mapping**

| Splunk field | SOAR field            |
| ------------ | --------------------- |
| `_time`      | `rt`                  |
| `host`       | `deviceHostname`      |
| `src_ip`     | `sourceAddress`       |
| `username`   | `destinationUserName` |

### **TLS verification consideration**

The integration used:

```
verify_certs=0
```

This was a temporary lab workaround.

For production, use a trusted TLS certificate and enable certificate verification. Disabling certificate verification weakens protection against certificate impersonation.

### **Overlapping search windows**

The saved search ran every minute and used a 15-minute search window.

This means consecutive searches can inspect overlapping time periods. Depending on the integration's deduplication and event handling configuration, this may create duplicate forwarded events.

The conversation does not confirm that duplicate events occurred. Therefore, this is an operational consideration rather than a recorded lab failure.

## **8.8. SOAR Container and Artifact Troubleshooting**

The integration successfully created a SOAR container and forwarded the associated artifact.

### **Confirmed container information**

| Property       | Value                            |
| -------------- | -------------------------------- |
| Container ID   | `723`                            |
| Event name     | `SOAR - Failed SSH Login Events` |
| Status         | New                              |
| Owner          | Unassigned                       |
| Artifact label | `ssh_failed_login`               |

### **Confirmed artifact data**

| Field                 | Value           |
| --------------------- | --------------- |
| `sourceAddress`       | `193.32.162.15` |
| `destinationUserName` | `root`          |
| `deviceHostname`      | `splunktest`    |

### **Troubleshooting checks**

If a container is not created:

1. Check whether the Splunk saved search returned events.
2. Verify the SOAR connection configuration.
3. Check the integration token.
4. Verify the event label and artifact label.
5. Inspect the SOAR integration logs.

If a container exists but artifact fields are missing:

1. Inspect the received artifact.
2. Verify the Splunk field extraction.
3. Check the field mappings.
4. Confirm the artifact CEF paths used by the playbook.

These checks are general guidance; the actual project successfully forwarded and verified the artifact.

## **8.9. VirusTotal API Troubleshooting**

The playbook queried VirusTotal using the source IP address.

The confirmed endpoint pattern was:

```
https://www.virustotal.com/api/v3/ip_addresses/{ip}
```

The successful execution returned:

```
HTTP Status: 200
```

### **Common API failure conditions**

| HTTP response or symptom | Possible reason                  | Recommended check                        |
| ------------------------ | -------------------------------- | ---------------------------------------- |
| 200                      | Successful API response          | Parse response JSON                      |
| 401                      | Invalid or missing API key       | Verify environment variable and key      |
| 403                      | Access or permission restriction | Check API access and account entitlement |
| 404                      | Invalid or unavailable resource  | Verify the IP address and endpoint       |
| 429                      | Rate limit exceeded              | Review API usage and request frequency   |
| Timeout                  | Network or API response delay    | Check connectivity and timeout handling  |

These are general HTTP/API troubleshooting references. Only HTTP 200 was confirmed in the successful lab execution.

### **API key validation**

The playbook retrieved the key using:

```
os.environ.get("VIRUSTOTAL_API_KEY")
```

The successful log recorded:

```
VirusTotal API key configured: True
```

Never print the actual API key in application logs or share it in documentation.

## **8.10. Splunk SOAR Playbook Troubleshooting**

The playbook name was:

```
SOC_Invalid_Login_Response
```

The latest recorded version was version `13`, with playbook ID `177`.

The successful run ID was `723`.

### **Confirmed execution results**

| Operation                   | Result     |
| --------------------------- | ---------- |
| API key retrieval           | Successful |
| Artifact extraction         | Successful |
| VirusTotal API request      | HTTP 200   |
| Risk classification         | HIGH       |
| Container severity update   | HIGH       |
| Investigation note creation | Successful |
| Final playbook status       | Success    |

### `requests`**&#x20;library warning**

During development, a validation warning related to the Python `requests` library was encountered.

Despite the warning, the final playbook execution succeeded. The API request returned HTTP 200, and the investigation note was created.

For future deployments, review supported Python dependencies and the SOAR application's recommended library versions.

### `Total actions executed: 0`

The execution output also displayed:

```
Total actions executed: 0
```

This is consistent with the custom whole-playbook Python implementation.

The Python logic executed directly rather than through visual action blocks. Therefore, this counter should not be interpreted independently as evidence of failure.

The successful execution status and the actual API, severity, and note results provide the relevant validation.

## **8.11. Splunk SOAR Service Restart**

On 18 November 2026, the SOAR VM was restarted using the `phantom` operating-system user.

The confirmed script paths were:

```
/data/phantom/bin/stop_phantom.sh
/data/phantom/bin/start_phantom.sh
```

The operating-system user was:

```
phantom
```

The SOAR instance was successfully restarted.

A warning appeared during startup concerning the missing file:

```
/opt/phantom/etc/phantom_install.conf
```

However, the recorded outcome was that all services started successfully.

### **Operational note**

The presence of a warning does not necessarily mean that the application failed to start. Always validate the final service status and application accessibility rather than relying on one warning message.

The exact complete restart command sequence was not preserved in the conversation.

## **8.12. SMTP Installation and Configuration Troubleshooting**

The SMTP application was installed in Splunk SOAR.

### **Confirmed SMTP application details**

| Property            | Value                                     |
| ------------------- | ----------------------------------------- |
| Application         | SMTP                                      |
| Publisher           | Splunk                                    |
| Application version | 3.3.9                                     |
| SOAR version        | 6.4.1.361                                 |
| Application state   | Installed; listed under Unconfigured Apps |

The next stage involved configuring a Gmail SMTP asset.

### **Gmail SMTP settings provided for configuration**

| Setting        | Recommended value   |
| -------------- | ------------------- |
| SMTP server    | `smtp.gmail.com`    |
| Port           | `587`               |
| SSL method     | StartTLS            |
| Username       | Full Gmail address  |
| Password       | Google App Password |
| Sender address | Same Gmail address  |

These are the settings provided as the intended Gmail configuration. Successful asset saving, connectivity testing, and email delivery have not yet been confirmed.

### **Important security consideration**

Do not use a normal Gmail account password as an SMTP credential where Google App Password authentication is required.

The App Password should be stored securely in the SOAR asset configuration and never exposed in playbook logs or documentation.

## **8.13. Operational Validation Checklist**

The following checklist can be used during future lab validation.

| Validation item           | Expected condition                  |
| ------------------------- | ----------------------------------- |
| Azure Splunk VM           | Running                             |
| Azure SOAR VM             | Running                             |
| Splunk Web                | Accessible on port 8000             |
| Splunk receiving service  | Listening on port 9997              |
| Universal Forwarder       | Running                             |
| Linux authentication logs | Being monitored                     |
| Splunk index              | `security`                          |
| Detection search          | Returns failed-login events         |
| SOAR integration          | Configured                          |
| SOAR container            | Created                             |
| Artifact fields           | Available                           |
| VirusTotal API            | Returns valid response              |
| Playbook                  | Executes successfully               |
| Container severity        | Updated according to risk           |
| Investigation note        | Created                             |
| SMTP asset                | Gmail configuration to be completed |
| Email delivery            | Awaiting confirmation               |

## **8.14. Information Not Explicitly Available**

To maintain technical accuracy, the following implementation details are not reconstructed or invented:

| Detail                                                | Documentation status                         |
| ----------------------------------------------------- | -------------------------------------------- |
| Azure subscription ID                                 | Not explicitly available in the conversation |
| Resource group names                                  | Not explicitly available in the conversation |
| VNet and subnet configuration                         | Not explicitly available in the conversation |
| VM sizes and disk specifications                      | Not explicitly available in the conversation |
| Exact NSG inbound/outbound rules                      | Not explicitly available in the conversation |
| Full original Universal Forwarder configuration files | Not explicitly available in the conversation |
| Full original playbook source code                    | Not explicitly available in the conversation |
| Exact original VM provisioning commands               | Not explicitly available in the conversation |
| Gmail asset save and connectivity test result         | Not explicitly available in the conversation |
| Successful SMTP email delivery                        | Not explicitly available in the conversation |

Reference commands and configuration examples in this section are intended to help reproduce or troubleshoot the environment. They should not be treated as a verbatim historical command log.

## **8.15. Part 8 Summary**

The troubleshooting and validation review confirms that the main SOC automation workflow was successfully implemented and tested.

The Linux authentication logs were forwarded to Splunk Enterprise, the detection search identified failed SSH login events, and Splunk SOAR received the associated event and artifact information.

The SOAR playbook successfully queried VirusTotal, classified the source IP as HIGH risk, updated the container severity, and created an investigation note.

The remaining operational stage is Gmail SMTP asset configuration and confirmation of successful email delivery.

**End of Part 8. Send 'Continue' for Part 9.**

# **PART 9 – SMTP EMAIL NOTIFICATIONS, SECURITY & PRODUCTION RECOMMENDATIONS**

## **9.1. Introduction**

The previous stages established an automated security investigation workflow using Splunk Enterprise, Splunk SOAR, and VirusTotal.

The next objective is to extend this workflow with email notifications so that security analysts can receive alerts when suspicious SSH login activity is identified.

The SMTP application was successfully installed in Splunk SOAR. Gmail SMTP asset configuration was the current implementation stage.

**Important implementation status:** SMTP application installation is completed. Gmail asset configuration, connectivity testing, playbook email integration, and successful email delivery have not yet been confirmed.

This section documents the confirmed progress and the recommended next steps without representing unverified activities as completed.

## **9.2. SMTP Integration Overview**

SMTP stands for Simple Mail Transfer Protocol.

It is a standard protocol used by applications and systems to send email messages through an SMTP server.

In this SOC lab, SMTP is intended to provide email notifications after the SOAR playbook investigates a suspicious SSH login event.

The proposed workflow is:

\#chatgpt-mermaid-\_r_dml\_{font-family:-apple-system-body,ui-sans-serif,-apple-system,system-ui,"Segoe UI",Helvetica,"Apple Color Emoji",Arial,sans-serif,"Segoe UI Emoji","Segoe UI Symbol";font-size:16px;fill:rgb(237, 237, 237);}@keyframes edge-animation-frame{from{stroke-dashoffset:0;}}@keyframes dash{to{stroke-dashoffset:0;}}#chatgpt-mermaid-\_r_dml\_ .edge-animation-slow{stroke-dasharray:9,5!important;stroke-dashoffset:900;animation:dash 50s linear infinite;stroke-linecap:round;}#chatgpt-mermaid-\_r_dml\_ .edge-animation-fast{stroke-dasharray:9,5!important;stroke-dashoffset:900;animation:dash 20s linear infinite;stroke-linecap:round;}#chatgpt-mermaid-\_r_dml\_ .error-icon{fill:rgb(48, 48, 48);}#chatgpt-mermaid-\_r_dml\_ .error-text{fill:rgb(237, 237, 237);stroke:rgb(237, 237, 237);}#chatgpt-mermaid-\_r_dml\_ .edge-thickness-normal{stroke-width:1px;}#chatgpt-mermaid-\_r_dml\_ .edge-thickness-thick{stroke-width:3.5px;}#chatgpt-mermaid-\_r_dml\_ .edge-pattern-solid{stroke-dasharray:0;}#chatgpt-mermaid-\_r_dml\_ .edge-thickness-invisible{stroke-width:0;fill:none;}#chatgpt-mermaid-\_r_dml\_ .edge-pattern-dashed{stroke-dasharray:3;}#chatgpt-mermaid-\_r_dml\_ .edge-pattern-dotted{stroke-dasharray:2;}#chatgpt-mermaid-\_r_dml\_ .marker{fill:rgb(175, 175, 175);stroke:rgb(175, 175, 175);}#chatgpt-mermaid-\_r_dml\_ .marker.cross{stroke:rgb(175, 175, 175);}#chatgpt-mermaid-\_r_dml\_ svg{font-family:-apple-system-body,ui-sans-serif,-apple-system,system-ui,"Segoe UI",Helvetica,"Apple Color Emoji",Arial,sans-serif,"Segoe UI Emoji","Segoe UI Symbol";font-size:16px;}#chatgpt-mermaid-\_r_dml\_ p{margin:0;}#chatgpt-mermaid-\_r_dml\_ .label{font-family:-apple-system-body,ui-sans-serif,-apple-system,system-ui,"Segoe UI",Helvetica,"Apple Color Emoji",Arial,sans-serif,"Segoe UI Emoji","Segoe UI Symbol";color:rgb(237, 237, 237);}#chatgpt-mermaid-\_r_dml\_ .cluster-label text{fill:rgb(237, 237, 237);}#chatgpt-mermaid-\_r_dml\_ .cluster-label span{color:rgb(237, 237, 237);}#chatgpt-mermaid-\_r_dml\_ .cluster-label span p{background-color:transparent;}#chatgpt-mermaid-\_r_dml\_ .label text,#chatgpt-mermaid-\_r_dml\_ span{fill:rgb(237, 237, 237);color:rgb(237, 237, 237);}#chatgpt-mermaid-\_r_dml\_ .node rect,#chatgpt-mermaid-\_r_dml\_ .node circle,#chatgpt-mermaid-\_r_dml\_ .node ellipse,#chatgpt-mermaid-\_r_dml\_ .node polygon,#chatgpt-mermaid-\_r_dml\_ .node path{fill:rgb(9, 23, 44);stroke:rgb(31, 78, 148);stroke-width:1px;}#chatgpt-mermaid-\_r_dml\_ .rough-node .label text,#chatgpt-mermaid-\_r_dml\_ .node .label text,#chatgpt-mermaid-\_r_dml\_ .image-shape .label,#chatgpt-mermaid-\_r_dml\_ .icon-shape .label{text-anchor:middle;}#chatgpt-mermaid-\_r_dml\_ .node .katex path{fill:#000;stroke:#000;stroke-width:1px;}#chatgpt-mermaid-\_r_dml\_ .rough-node .label,#chatgpt-mermaid-\_r_dml\_ .node .label,#chatgpt-mermaid-\_r_dml\_ .image-shape .label,#chatgpt-mermaid-\_r_dml\_ .icon-shape .label{text-align:center;}#chatgpt-mermaid-\_r_dml\_ .node.clickable{cursor:pointer;}#chatgpt-mermaid-\_r_dml\_ .root .anchor path{fill:rgb(175, 175, 175)!important;stroke-width:0;stroke:rgb(175, 175, 175);}#chatgpt-mermaid-\_r_dml\_ .arrowheadPath{fill:rgb(175, 175, 175);}#chatgpt-mermaid-\_r_dml\_ .edgePath .path{stroke:rgb(175, 175, 175);stroke-width:1px;}#chatgpt-mermaid-\_r_dml\_ .flowchart-link{stroke:rgb(175, 175, 175);fill:none;}#chatgpt-mermaid-\_r_dml\_ .edgeLabel{background-color:rgb(0, 0, 0);text-align:center;}#chatgpt-mermaid-\_r_dml\_ .edgeLabel p{background-color:rgb(0, 0, 0);}#chatgpt-mermaid-\_r_dml\_ .edgeLabel rect{opacity:0.5;background-color:rgb(0, 0, 0);fill:rgb(0, 0, 0);}#chatgpt-mermaid-\_r_dml\_ .labelBkg{background-color:rgba(0, 0, 0, 0.5);}#chatgpt-mermaid-\_r_dml\_ .cluster rect{fill:rgb(48, 48, 48);stroke:rgba(255, 255, 255, 0.15);stroke-width:1px;}#chatgpt-mermaid-\_r_dml\_ .cluster text{fill:rgb(237, 237, 237);}#chatgpt-mermaid-\_r_dml\_ .cluster span{color:rgb(237, 237, 237);}#chatgpt-mermaid-\_r_dml\_ div.mermaidTooltip{position:absolute;text-align:center;max-width:200px;padding:2px;font-family:-apple-system-body,ui-sans-serif,-apple-system,system-ui,"Segoe UI",Helvetica,"Apple Color Emoji",Arial,sans-serif,"Segoe UI Emoji","Segoe UI Symbol";font-size:12px;background:rgb(48, 48, 48);border:1px solid rgba(255, 255, 255, 0.15);border-radius:2px;pointer-events:none;z-index:100;}#chatgpt-mermaid-\_r_dml\_ .flowchartTitleText{text-anchor:middle;font-size:18px;fill:rgb(237, 237, 237);}#chatgpt-mermaid-\_r_dml\_ rect.text{fill:none;stroke-width:0;}#chatgpt-mermaid-\_r_dml\_ .icon-shape,#chatgpt-mermaid-\_r_dml\_ .image-shape{background-color:rgb(0, 0, 0);text-align:center;}#chatgpt-mermaid-\_r_dml\_ .icon-shape p,#chatgpt-mermaid-\_r_dml\_ .image-shape p{background-color:rgb(0, 0, 0);padding:2px;}#chatgpt-mermaid-\_r_dml\_ .icon-shape .label rect,#chatgpt-mermaid-\_r_dml\_ .image-shape .label rect{opacity:0.5;background-color:rgb(0, 0, 0);fill:rgb(0, 0, 0);}#chatgpt-mermaid-\_r_dml\_ .label-icon{display:inline-block;height:1em;overflow:visible;vertical-align:-0.125em;}#chatgpt-mermaid-\_r_dml\_ .node .label-icon path{fill:currentColor;stroke:revert;stroke-width:revert;}#chatgpt-mermaid-\_r_dml\_ .node .neo-node{stroke:rgb(31, 78, 148);}#chatgpt-mermaid-\_r_dml\_ [data-look="neo"].node rect,#chatgpt-mermaid-\_r_dml\_ [data-look="neo"].cluster rect,#chatgpt-mermaid-\_r_dml\_ [data-look="neo"].node polygon{stroke:url(#chatgpt-mermaid-\_r_dml\_-gradient);filter:drop-shadow( 1px 2px 2px rgba(185,185,185,1));}#chatgpt-mermaid-\_r_dml\_ [data-look="neo"].swimlane.cluster rect{filter:none;}#chatgpt-mermaid-\_r_dml\_ [data-look="neo"].node path{stroke:url(#chatgpt-mermaid-\_r_dml\_-gradient);stroke-width:1px;}#chatgpt-mermaid-\_r_dml\_ [data-look="neo"].node .outer-path{filter:drop-shadow( 1px 2px 2px rgba(185,185,185,1));}#chatgpt-mermaid-\_r_dml\_ [data-look="neo"].node .neo-line path{stroke:rgb(31, 78, 148);filter:none;}#chatgpt-mermaid-\_r_dml\_ [data-look="neo"].node circle{stroke:url(#chatgpt-mermaid-\_r_dml\_-gradient);filter:drop-shadow( 1px 2px 2px rgba(185,185,185,1));}#chatgpt-mermaid-\_r_dml\_ [data-look="neo"].node circle .state-start{fill:#000000;}#chatgpt-mermaid-\_r_dml\_ [data-look="neo"].icon-shape .icon{fill:url(#chatgpt-mermaid-\_r_dml\_-gradient);filter:drop-shadow( 1px 2px 2px rgba(185,185,185,1));}#chatgpt-mermaid-\_r_dml\_ [data-look="neo"].icon-shape .icon-neo path{stroke:url(#chatgpt-mermaid-\_r_dml\_-gradient);filter:drop-shadow( 1px 2px 2px rgba(185,185,185,1));}#chatgpt-mermaid-\_r_dml\_ .node text{font-size:14px;font-weight:600;letter-spacing:normal;fill:rgb(153, 206, 255);}#chatgpt-mermaid-\_r_dml\_ .edgeLabels text{font-size:13px;font-weight:600;letter-spacing:-0.08px;fill:rgb(153, 206, 255);}#chatgpt-mermaid-\_r_dml\_ .node tspan[font-weight="normal"],#chatgpt-mermaid-\_r_dml\_ .edgeLabels tspan[font-weight="normal"]{font-weight:600;}#chatgpt-mermaid-\_r_dml\_ .edgeLabel .label rect{opacity:1;rx:13px;ry:13px;fill:rgb(0, 14, 26);stroke:rgb(26, 62, 95);stroke-width:1px;}#chatgpt-mermaid-\_r_dml\_ .node rect,#chatgpt-mermaid-\_r_dml\_ .node circle,#chatgpt-mermaid-\_r_dml\_ .node ellipse,#chatgpt-mermaid-\_r_dml\_ .node polygon,#chatgpt-mermaid-\_r_dml\_ .node path{fill:rgb(0, 40, 77);stroke:rgba(255, 255, 255, 0.1);stroke-width:1px;}#chatgpt-mermaid-\_r_dml\_ .node rect{rx:16px;ry:16px;}#chatgpt-mermaid-\_r_dml\_ .mermaid-decision .label-container{fill:rgb(0, 14, 26);stroke:rgb(26, 62, 95);stroke-dasharray:2,2;}#chatgpt-mermaid-\_r_dml\_ .edgePaths .flowchart-link{stroke:rgb(175, 175, 175);stroke-width:1px;stroke-linecap:round;stroke-linejoin:round;}#chatgpt-mermaid-\_r_dml\_ .marker{fill:rgb(175, 175, 175);stroke:rgb(175, 175, 175);}#chatgpt-mermaid-\_r_dml\_ :root{--mermaid-font-family:-apple-system-body,ui-sans-serif,-apple-system,system-ui,"Segoe UI",Helvetica,"Apple Color Emoji",Arial,sans-serif,"Segoe UI Emoji","Segoe UI Symbol";}SSH failed-login eventSplunk EnterpriseSplunk SOARVirusTotal investigationRisk classificationUpdate SOAR containerseverityCreate investigation noteSend SMTP email notificationSecurity analyst receives alert



Planned integration

&#x20;The SMTP email notification stage is not yet verified as part of the running playbook.



## **9.3. SMTP Application Installation**

The SMTP application was installed from the Splunk SOAR application catalogue.

### **Confirmed application information**

| Property            | Value       |
| ------------------- | ----------- |
| Application name    | SMTP        |
| Publisher           | Splunk      |
| Application version | 3.3.9       |
| Splunk SOAR version | 6.4.1.361   |
| Installation status | Completed   |
| Asset configuration | In progress |

The application appeared under **Unconfigured Apps** after installation.

This indicates that the application was installed, but its SMTP connection asset still required configuration.

### **Asset configuration sections**

The SMTP configuration interface displayed the following sections:

1. Asset Info
2. Asset Settings
3. Approval Settings
4. Access Control

The asset configuration process was initiated using **Configure New Asset**.

## **9.4. Gmail SMTP Configuration**

Gmail was selected as the intended SMTP provider.

The recommended settings discussed during the configuration process were:

| Configuration field | Value                    |
| ------------------- | ------------------------ |
| SMTP server         | `smtp.gmail.com`         |
| SMTP port           | `587`                    |
| SSL method          | StartTLS                 |
| Username            | Full Gmail email address |
| Password            | Google App Password      |
| Sender address      | Same Gmail email address |

The exact Gmail account address and asset name are not included because they were not confirmed in the available project details.

### **Why port 587?**

Port 587 is commonly used for SMTP message submission with STARTTLS.

STARTTLS upgrades an initially unencrypted connection to an encrypted TLS connection.

This allows the SMTP client to establish a protected connection before transmitting authentication credentials and email content.

## **9.5. Google App Password**

For the intended Gmail SMTP configuration, a Google App Password was recommended rather than the user's regular Gmail account password.

An App Password is a separate credential generated through Google Account security settings for applications that require this authentication method.

### **Configuration preparation**

The intended setup process is:

1. Open Google Account settings.
2. Navigate to Security.
3. Enable 2-Step Verification if it is not already enabled.
4. Locate the App Passwords option, if available for the account.
5. Generate an App Password for the SMTP integration.
6. Enter the generated credential in the SOAR SMTP asset configuration.
7. Save the asset.
8. Test the connection using the application's supported test functionality.

**Credential security**

- Never share the Google App Password in ChatGPT messages.
- Do not hardcode it in playbook source code.
- Do not print it in execution logs.
- Store it only in the appropriate protected asset configuration.

Google account policies may affect whether App Passwords are available. Their availability should be checked in the account's current security settings.

## **9.6. SMTP Asset Validation**

After entering the SMTP settings, the asset should be saved and its connectivity validated.

The following checks are recommended:

| Validation          | Expected result          |
| ------------------- | ------------------------ |
| SMTP hostname       | Correctly configured     |
| Port                | 587                      |
| Connection security | STARTTLS                 |
| Username            | Valid Gmail address      |
| Authentication      | Accepted                 |
| Sender address      | Valid configured address |
| Asset save          | Successful               |
| Connection test     | Successful               |

**Current project status:** The conversation confirms that the Gmail SMTP configuration screen was reached and the recommended values were provided. It does not confirm that the asset was saved or that a connection test succeeded.

## **9.7. Planned Email Notification Design**

Once SMTP asset configuration is completed, the next implementation step is to connect the SMTP application to the SOAR investigation workflow.

The intended notification should communicate the important investigation details to the security analyst.

### **Recommended email subject**

`[HIGH] Suspicious SSH Login Detected – 193.32.162.15`

### **Recommended email body**

Hello Security Team,

A suspicious SSH login event has been identified and investigated by the SOC automation workflow.

**Incident Details**

- Source IP: 193.32.162.15
- Target Username: root
- Affected Host: splunktest
- Risk Level: HIGH

**VirusTotal Analysis**

- Reputation: -9
- Malicious Detections: 16
- Suspicious Detections: 3
- Harmless Detections: 44
- Undetected: 29

**Automated Response**

- VirusTotal investigation completed.
- SOAR container severity updated to HIGH.
- Automated investigation note created.

Please review the corresponding SOAR investigation and perform any additional validation or response actions required.

Regards,\
SOC Automation System

What is this?

This is a proposed notification template based on the actual investigation values. It is not a record of an email that was sent.

## **9.8. Recommended Playbook Integration Sequence**

The existing successful playbook performs the following operations:

1. Extracts the SSH failed-login artifact.
2. Retrieves the VirusTotal API key.
3. Queries VirusTotal.
4. Calculates the risk classification.
5. Updates the container severity.
6. Creates an investigation note.

The proposed next step is to add SMTP notification after the investigation information is available.

A recommended logical sequence is:

\#chatgpt-mermaid-\_r_dn5\_{font-family:-apple-system-body,ui-sans-serif,-apple-system,system-ui,"Segoe UI",Helvetica,"Apple Color Emoji",Arial,sans-serif,"Segoe UI Emoji","Segoe UI Symbol";font-size:16px;fill:rgb(237, 237, 237);}@keyframes edge-animation-frame{from{stroke-dashoffset:0;}}@keyframes dash{to{stroke-dashoffset:0;}}#chatgpt-mermaid-\_r_dn5\_ .edge-animation-slow{stroke-dasharray:9,5!important;stroke-dashoffset:900;animation:dash 50s linear infinite;stroke-linecap:round;}#chatgpt-mermaid-\_r_dn5\_ .edge-animation-fast{stroke-dasharray:9,5!important;stroke-dashoffset:900;animation:dash 20s linear infinite;stroke-linecap:round;}#chatgpt-mermaid-\_r_dn5\_ .error-icon{fill:rgb(48, 48, 48);}#chatgpt-mermaid-\_r_dn5\_ .error-text{fill:rgb(237, 237, 237);stroke:rgb(237, 237, 237);}#chatgpt-mermaid-\_r_dn5\_ .edge-thickness-normal{stroke-width:1px;}#chatgpt-mermaid-\_r_dn5\_ .edge-thickness-thick{stroke-width:3.5px;}#chatgpt-mermaid-\_r_dn5\_ .edge-pattern-solid{stroke-dasharray:0;}#chatgpt-mermaid-\_r_dn5\_ .edge-thickness-invisible{stroke-width:0;fill:none;}#chatgpt-mermaid-\_r_dn5\_ .edge-pattern-dashed{stroke-dasharray:3;}#chatgpt-mermaid-\_r_dn5\_ .edge-pattern-dotted{stroke-dasharray:2;}#chatgpt-mermaid-\_r_dn5\_ .marker{fill:rgb(175, 175, 175);stroke:rgb(175, 175, 175);}#chatgpt-mermaid-\_r_dn5\_ .marker.cross{stroke:rgb(175, 175, 175);}#chatgpt-mermaid-\_r_dn5\_ svg{font-family:-apple-system-body,ui-sans-serif,-apple-system,system-ui,"Segoe UI",Helvetica,"Apple Color Emoji",Arial,sans-serif,"Segoe UI Emoji","Segoe UI Symbol";font-size:16px;}#chatgpt-mermaid-\_r_dn5\_ p{margin:0;}#chatgpt-mermaid-\_r_dn5\_ .label{font-family:-apple-system-body,ui-sans-serif,-apple-system,system-ui,"Segoe UI",Helvetica,"Apple Color Emoji",Arial,sans-serif,"Segoe UI Emoji","Segoe UI Symbol";color:rgb(237, 237, 237);}#chatgpt-mermaid-\_r_dn5\_ .cluster-label text{fill:rgb(237, 237, 237);}#chatgpt-mermaid-\_r_dn5\_ .cluster-label span{color:rgb(237, 237, 237);}#chatgpt-mermaid-\_r_dn5\_ .cluster-label span p{background-color:transparent;}#chatgpt-mermaid-\_r_dn5\_ .label text,#chatgpt-mermaid-\_r_dn5\_ span{fill:rgb(237, 237, 237);color:rgb(237, 237, 237);}#chatgpt-mermaid-\_r_dn5\_ .node rect,#chatgpt-mermaid-\_r_dn5\_ .node circle,#chatgpt-mermaid-\_r_dn5\_ .node ellipse,#chatgpt-mermaid-\_r_dn5\_ .node polygon,#chatgpt-mermaid-\_r_dn5\_ .node path{fill:rgb(9, 23, 44);stroke:rgb(31, 78, 148);stroke-width:1px;}#chatgpt-mermaid-\_r_dn5\_ .rough-node .label text,#chatgpt-mermaid-\_r_dn5\_ .node .label text,#chatgpt-mermaid-\_r_dn5\_ .image-shape .label,#chatgpt-mermaid-\_r_dn5\_ .icon-shape .label{text-anchor:middle;}#chatgpt-mermaid-\_r_dn5\_ .node .katex path{fill:#000;stroke:#000;stroke-width:1px;}#chatgpt-mermaid-\_r_dn5\_ .rough-node .label,#chatgpt-mermaid-\_r_dn5\_ .node .label,#chatgpt-mermaid-\_r_dn5\_ .image-shape .label,#chatgpt-mermaid-\_r_dn5\_ .icon-shape .label{text-align:center;}#chatgpt-mermaid-\_r_dn5\_ .node.clickable{cursor:pointer;}#chatgpt-mermaid-\_r_dn5\_ .root .anchor path{fill:rgb(175, 175, 175)!important;stroke-width:0;stroke:rgb(175, 175, 175);}#chatgpt-mermaid-\_r_dn5\_ .arrowheadPath{fill:rgb(175, 175, 175);}#chatgpt-mermaid-\_r_dn5\_ .edgePath .path{stroke:rgb(175, 175, 175);stroke-width:1px;}#chatgpt-mermaid-\_r_dn5\_ .flowchart-link{stroke:rgb(175, 175, 175);fill:none;}#chatgpt-mermaid-\_r_dn5\_ .edgeLabel{background-color:rgb(0, 0, 0);text-align:center;}#chatgpt-mermaid-\_r_dn5\_ .edgeLabel p{background-color:rgb(0, 0, 0);}#chatgpt-mermaid-\_r_dn5\_ .edgeLabel rect{opacity:0.5;background-color:rgb(0, 0, 0);fill:rgb(0, 0, 0);}#chatgpt-mermaid-\_r_dn5\_ .labelBkg{background-color:rgba(0, 0, 0, 0.5);}#chatgpt-mermaid-\_r_dn5\_ .cluster rect{fill:rgb(48, 48, 48);stroke:rgba(255, 255, 255, 0.15);stroke-width:1px;}#chatgpt-mermaid-\_r_dn5\_ .cluster text{fill:rgb(237, 237, 237);}#chatgpt-mermaid-\_r_dn5\_ .cluster span{color:rgb(237, 237, 237);}#chatgpt-mermaid-\_r_dn5\_ div.mermaidTooltip{position:absolute;text-align:center;max-width:200px;padding:2px;font-family:-apple-system-body,ui-sans-serif,-apple-system,system-ui,"Segoe UI",Helvetica,"Apple Color Emoji",Arial,sans-serif,"Segoe UI Emoji","Segoe UI Symbol";font-size:12px;background:rgb(48, 48, 48);border:1px solid rgba(255, 255, 255, 0.15);border-radius:2px;pointer-events:none;z-index:100;}#chatgpt-mermaid-\_r_dn5\_ .flowchartTitleText{text-anchor:middle;font-size:18px;fill:rgb(237, 237, 237);}#chatgpt-mermaid-\_r_dn5\_ rect.text{fill:none;stroke-width:0;}#chatgpt-mermaid-\_r_dn5\_ .icon-shape,#chatgpt-mermaid-\_r_dn5\_ .image-shape{background-color:rgb(0, 0, 0);text-align:center;}#chatgpt-mermaid-\_r_dn5\_ .icon-shape p,#chatgpt-mermaid-\_r_dn5\_ .image-shape p{background-color:rgb(0, 0, 0);padding:2px;}#chatgpt-mermaid-\_r_dn5\_ .icon-shape .label rect,#chatgpt-mermaid-\_r_dn5\_ .image-shape .label rect{opacity:0.5;background-color:rgb(0, 0, 0);fill:rgb(0, 0, 0);}#chatgpt-mermaid-\_r_dn5\_ .label-icon{display:inline-block;height:1em;overflow:visible;vertical-align:-0.125em;}#chatgpt-mermaid-\_r_dn5\_ .node .label-icon path{fill:currentColor;stroke:revert;stroke-width:revert;}#chatgpt-mermaid-\_r_dn5\_ .node .neo-node{stroke:rgb(31, 78, 148);}#chatgpt-mermaid-\_r_dn5\_ [data-look="neo"].node rect,#chatgpt-mermaid-\_r_dn5\_ [data-look="neo"].cluster rect,#chatgpt-mermaid-\_r_dn5\_ [data-look="neo"].node polygon{stroke:url(#chatgpt-mermaid-\_r_dn5\_-gradient);filter:drop-shadow( 1px 2px 2px rgba(185,185,185,1));}#chatgpt-mermaid-\_r_dn5\_ [data-look="neo"].swimlane.cluster rect{filter:none;}#chatgpt-mermaid-\_r_dn5\_ [data-look="neo"].node path{stroke:url(#chatgpt-mermaid-\_r_dn5\_-gradient);stroke-width:1px;}#chatgpt-mermaid-\_r_dn5\_ [data-look="neo"].node .outer-path{filter:drop-shadow( 1px 2px 2px rgba(185,185,185,1));}#chatgpt-mermaid-\_r_dn5\_ [data-look="neo"].node .neo-line path{stroke:rgb(31, 78, 148);filter:none;}#chatgpt-mermaid-\_r_dn5\_ [data-look="neo"].node circle{stroke:url(#chatgpt-mermaid-\_r_dn5\_-gradient);filter:drop-shadow( 1px 2px 2px rgba(185,185,185,1));}#chatgpt-mermaid-\_r_dn5\_ [data-look="neo"].node circle .state-start{fill:#000000;}#chatgpt-mermaid-\_r_dn5\_ [data-look="neo"].icon-shape .icon{fill:url(#chatgpt-mermaid-\_r_dn5\_-gradient);filter:drop-shadow( 1px 2px 2px rgba(185,185,185,1));}#chatgpt-mermaid-\_r_dn5\_ [data-look="neo"].icon-shape .icon-neo path{stroke:url(#chatgpt-mermaid-\_r_dn5\_-gradient);filter:drop-shadow( 1px 2px 2px rgba(185,185,185,1));}#chatgpt-mermaid-\_r_dn5\_ .node text{font-size:14px;font-weight:600;letter-spacing:normal;fill:rgb(153, 206, 255);}#chatgpt-mermaid-\_r_dn5\_ .edgeLabels text{font-size:13px;font-weight:600;letter-spacing:-0.08px;fill:rgb(153, 206, 255);}#chatgpt-mermaid-\_r_dn5\_ .node tspan[font-weight="normal"],#chatgpt-mermaid-\_r_dn5\_ .edgeLabels tspan[font-weight="normal"]{font-weight:600;}#chatgpt-mermaid-\_r_dn5\_ .edgeLabel .label rect{opacity:1;rx:13px;ry:13px;fill:rgb(0, 14, 26);stroke:rgb(26, 62, 95);stroke-width:1px;}#chatgpt-mermaid-\_r_dn5\_ .node rect,#chatgpt-mermaid-\_r_dn5\_ .node circle,#chatgpt-mermaid-\_r_dn5\_ .node ellipse,#chatgpt-mermaid-\_r_dn5\_ .node polygon,#chatgpt-mermaid-\_r_dn5\_ .node path{fill:rgb(0, 40, 77);stroke:rgba(255, 255, 255, 0.1);stroke-width:1px;}#chatgpt-mermaid-\_r_dn5\_ .node rect{rx:16px;ry:16px;}#chatgpt-mermaid-\_r_dn5\_ .mermaid-decision .label-container{fill:rgb(0, 14, 26);stroke:rgb(26, 62, 95);stroke-dasharray:2,2;}#chatgpt-mermaid-\_r_dn5\_ .edgePaths .flowchart-link{stroke:rgb(175, 175, 175);stroke-width:1px;stroke-linecap:round;stroke-linejoin:round;}#chatgpt-mermaid-\_r_dn5\_ .marker{fill:rgb(175, 175, 175);stroke:rgb(175, 175, 175);}#chatgpt-mermaid-\_r_dn5\_ :root{--mermaid-font-family:-apple-system-body,ui-sans-serif,-apple-system,system-ui,"Segoe UI",Helvetica,"Apple Color Emoji",Arial,sans-serif,"Segoe UI Emoji","Segoe UI Symbol";}Receive SOAR containerExtract artifact informationVirusTotal API lookupCalculate risk levelUpdate container severityCreate investigation noteRisk level?Prepare high-risk emailPrepare medium-risk emailApply configured low-risknotification policySMTP applicationAnalyst notificationHIGHMEDIUMLOW

This diagram represents a recommended future design. The current playbook's confirmed classification thresholds are:

- HIGH: malicious detections greater than or equal to 10.
- MEDIUM: malicious detections from 3 through 9.
- LOW: malicious detections from 0 through 2.

The exact SMTP action name, input parameters, and playbook integration code are not explicitly available in the conversation. They must be taken from the installed SMTP application's actual action documentation rather than guessed.

## **9.9. Email Notification Security Considerations**

Email notifications can contain security-sensitive information.

The following practices are recommended.

### **9.9.1. Protect credentials**

- Store SMTP credentials in the SOAR asset configuration.
- Avoid credentials in Python source code.
- Restrict access to the SMTP asset.
- Rotate credentials if exposure is suspected.

### **9.9.2. Protect investigation information**

- Send alerts only to authorized recipients.
- Avoid including API keys, passwords, tokens, or other secrets.
- Include only the information required for investigation.
- Avoid distributing sensitive incident information to unnecessary recipients.

### **9.9.3. Prevent excessive notifications**

A detection workflow can generate many alerts during repeated failed-login activity.

Recommended controls include:

- Notification only for configured severity levels.
- Duplicate-event handling.
- Alert grouping.
- Appropriate notification frequency.
- Monitoring SMTP delivery failures.

These are production recommendations, not features confirmed as implemented in this lab.

## **9.10. Production Security Recommendations**

The lab demonstrates an automated investigation workflow. Before adapting it for production, the following improvements should be considered.

| Area                | Current lab observation            | Production recommendation                             |
| ------------------- | ---------------------------------- | ----------------------------------------------------- |
| TLS verification    | `verify_certs=0` used              | Use trusted certificates and enable verification      |
| API credentials     | Environment variable used          | Apply protected secret management and rotation        |
| SMTP credentials    | Gmail asset configuration underway | Store credentials in protected SOAR assets            |
| VirusTotal API      | Direct Python REST integration     | Implement response validation and rate-limit handling |
| Risk classification | Malicious detection thresholds     | Review thresholds against organizational policy       |
| Event scheduling    | Every minute, 15-minute window     | Review duplicate handling and event deduplication     |
| Investigation notes | Automated note creation            | Maintain clear evidence and audit records             |
| Email notification  | Not yet verified                   | Test delivery and failure handling                    |
| Access control      | Full configuration not available   | Apply least-privilege access                          |
| Logging             | Playbook execution logs available  | Avoid logging sensitive secrets                       |

## **9.11. Additional Improvements for the SOC Workflow**

The current implementation focuses on identifying failed SSH logins and investigating their source IP addresses.

The following enhancements could be considered in future versions.

### **A. Repeated failed-login detection**

Detect repeated failed authentication attempts from the same source IP over a defined period.

For example, an organization could establish a threshold based on its normal authentication activity and security policy.

### **B. Historical IP investigation**

Maintain records of previously investigated IP addresses to identify recurring sources of suspicious activity.

### **C. Analyst review workflow**

Assign HIGH-risk containers to the appropriate security analyst or team.

### **D. Incident lifecycle management**

Introduce structured incident statuses such as:

- New
- In Progress
- Pending Review
- Resolved
- Closed

The exact status workflow should follow the organization's incident management requirements.

### **E. Notification delivery monitoring**

Record whether the SMTP operation succeeded or failed, and provide a controlled retry mechanism where appropriate.

None of these enhancements should be treated as completed project features without implementation and verification.

## **9.12. Current Project Status**

The following table summarizes the confirmed implementation state at the end of this documentation section.

| Component                            | Status                |
| ------------------------------------ | --------------------- |
| Azure Splunk Enterprise VM           | Completed             |
| Azure Splunk SOAR VM                 | Completed             |
| Linux Universal Forwarder            | Completed             |
| SSH failed-login log forwarding      | Completed             |
| Splunk detection search              | Completed             |
| Splunk-to-SOAR integration           | Completed             |
| Automatic event forwarding           | Completed             |
| Artifact forwarding and verification | Completed             |
| VirusTotal API integration           | Completed             |
| IP reputation analysis               | Completed             |
| Risk classification                  | Completed             |
| HIGH-risk container severity update  | Completed             |
| Automated investigation note         | Completed             |
| Playbook execution verification      | Completed             |
| SMTP application installation        | Completed             |
| Gmail SMTP asset configuration       | In progress           |
| Successful email delivery            | Awaiting confirmation |

## **9.13. Part 9 Summary**

The SMTP application was installed successfully in Splunk SOAR, and Gmail was selected as the intended email provider.

The recommended Gmail configuration uses `smtp.gmail.com`, port `587`, STARTTLS, and a Google App Password.

The existing SOAR playbook already completes VirusTotal investigation, risk classification, container severity updates, and automated investigation note creation.

The next planned enhancement is to configure the Gmail SMTP asset, validate connectivity, integrate the appropriate SMTP action into the playbook, and verify successful email delivery.

**End of Part 9. Send 'Continue' for Part 10.**

# **PART 10 – COMPLETE IMPLEMENTATION GUIDE, ARCHITECTURE REVIEW & INTERVIEW PREPARATION**

## **10.1. Project Overview**

**Project Name:** SOC Lab: Splunk Enterprise + Splunk SOAR + VirusTotal on Azure

This project demonstrates the implementation of a Security Operations Center (SOC) automation workflow using Splunk Enterprise, Splunk SOAR, and VirusTotal, hosted on Microsoft Azure.

The primary objective is to detect failed SSH login attempts on a Linux machine, forward the security events to Splunk Enterprise, automate investigation through Splunk SOAR, and assess the source IP reputation using VirusTotal.

The automation classifies the source IP based on malicious detection counts, updates the SOAR container severity, and creates an investigation note.

The project also includes SMTP application installation as preparation for email notifications. Gmail asset configuration and successful email delivery remain unverified.

## **10.2. Final Architecture**

[Splunk SOAR Playbook of the Month: Tackling Phishing Attempts with Identifier Reputation Analysis | Splunk](https://images.openai.com/static-rsc-4/bhLzgpXmlA3al80X4_8DjIhkg4CIYKUIXGigumfTGx_2cz1wKfjjx-k_Owr3FpN3rtdRv3NS-6YHY-q8q2Imgc8QUcUynFnycBswjXi5NrcxaPlReb-qmYOGq0Ynq9NqhQRbFDPK3sXhityOtKC542w0TD9pKIm0fnyPeU_3mbc?purpose=inline)

[splunk.com](https://www.splunk.com/en_us/blog/security/splunk-soar-playbook-of-the-month-tackling-phishing-attempts-with-dynamic-identifier-reputation-analysis.html?utm_source=chatgpt.com)

The architecture consists of the following major components:

| Component                  | Responsibility                                                 |
| -------------------------- | -------------------------------------------------------------- |
| Azure infrastructure       | Hosts the lab virtual machines                                 |
| Linux test VM              | Generates SSH authentication events                            |
| Splunk Universal Forwarder | Collects and forwards Linux authentication logs                |
| Splunk Enterprise          | Receives logs, indexes events, and runs detection searches     |
| Splunk SOAR                | Receives security events and executes automated investigations |
| VirusTotal                 | Provides IP reputation and detection information               |
| Python playbook            | Automates investigation and risk classification                |
| SMTP application           | Intended for analyst email notifications                       |

### **End-to-end workflow**

\#chatgpt-mermaid-\_r_do9\_{font-family:-apple-system-body,ui-sans-serif,-apple-system,system-ui,"Segoe UI",Helvetica,"Apple Color Emoji",Arial,sans-serif,"Segoe UI Emoji","Segoe UI Symbol";font-size:16px;fill:rgb(237, 237, 237);}@keyframes edge-animation-frame{from{stroke-dashoffset:0;}}@keyframes dash{to{stroke-dashoffset:0;}}#chatgpt-mermaid-\_r_do9\_ .edge-animation-slow{stroke-dasharray:9,5!important;stroke-dashoffset:900;animation:dash 50s linear infinite;stroke-linecap:round;}#chatgpt-mermaid-\_r_do9\_ .edge-animation-fast{stroke-dasharray:9,5!important;stroke-dashoffset:900;animation:dash 20s linear infinite;stroke-linecap:round;}#chatgpt-mermaid-\_r_do9\_ .error-icon{fill:rgb(48, 48, 48);}#chatgpt-mermaid-\_r_do9\_ .error-text{fill:rgb(237, 237, 237);stroke:rgb(237, 237, 237);}#chatgpt-mermaid-\_r_do9\_ .edge-thickness-normal{stroke-width:1px;}#chatgpt-mermaid-\_r_do9\_ .edge-thickness-thick{stroke-width:3.5px;}#chatgpt-mermaid-\_r_do9\_ .edge-pattern-solid{stroke-dasharray:0;}#chatgpt-mermaid-\_r_do9\_ .edge-thickness-invisible{stroke-width:0;fill:none;}#chatgpt-mermaid-\_r_do9\_ .edge-pattern-dashed{stroke-dasharray:3;}#chatgpt-mermaid-\_r_do9\_ .edge-pattern-dotted{stroke-dasharray:2;}#chatgpt-mermaid-\_r_do9\_ .marker{fill:rgb(175, 175, 175);stroke:rgb(175, 175, 175);}#chatgpt-mermaid-\_r_do9\_ .marker.cross{stroke:rgb(175, 175, 175);}#chatgpt-mermaid-\_r_do9\_ svg{font-family:-apple-system-body,ui-sans-serif,-apple-system,system-ui,"Segoe UI",Helvetica,"Apple Color Emoji",Arial,sans-serif,"Segoe UI Emoji","Segoe UI Symbol";font-size:16px;}#chatgpt-mermaid-\_r_do9\_ p{margin:0;}#chatgpt-mermaid-\_r_do9\_ .label{font-family:-apple-system-body,ui-sans-serif,-apple-system,system-ui,"Segoe UI",Helvetica,"Apple Color Emoji",Arial,sans-serif,"Segoe UI Emoji","Segoe UI Symbol";color:rgb(237, 237, 237);}#chatgpt-mermaid-\_r_do9\_ .cluster-label text{fill:rgb(237, 237, 237);}#chatgpt-mermaid-\_r_do9\_ .cluster-label span{color:rgb(237, 237, 237);}#chatgpt-mermaid-\_r_do9\_ .cluster-label span p{background-color:transparent;}#chatgpt-mermaid-\_r_do9\_ .label text,#chatgpt-mermaid-\_r_do9\_ span{fill:rgb(237, 237, 237);color:rgb(237, 237, 237);}#chatgpt-mermaid-\_r_do9\_ .node rect,#chatgpt-mermaid-\_r_do9\_ .node circle,#chatgpt-mermaid-\_r_do9\_ .node ellipse,#chatgpt-mermaid-\_r_do9\_ .node polygon,#chatgpt-mermaid-\_r_do9\_ .node path{fill:rgb(9, 23, 44);stroke:rgb(31, 78, 148);stroke-width:1px;}#chatgpt-mermaid-\_r_do9\_ .rough-node .label text,#chatgpt-mermaid-\_r_do9\_ .node .label text,#chatgpt-mermaid-\_r_do9\_ .image-shape .label,#chatgpt-mermaid-\_r_do9\_ .icon-shape .label{text-anchor:middle;}#chatgpt-mermaid-\_r_do9\_ .node .katex path{fill:#000;stroke:#000;stroke-width:1px;}#chatgpt-mermaid-\_r_do9\_ .rough-node .label,#chatgpt-mermaid-\_r_do9\_ .node .label,#chatgpt-mermaid-\_r_do9\_ .image-shape .label,#chatgpt-mermaid-\_r_do9\_ .icon-shape .label{text-align:center;}#chatgpt-mermaid-\_r_do9\_ .node.clickable{cursor:pointer;}#chatgpt-mermaid-\_r_do9\_ .root .anchor path{fill:rgb(175, 175, 175)!important;stroke-width:0;stroke:rgb(175, 175, 175);}#chatgpt-mermaid-\_r_do9\_ .arrowheadPath{fill:rgb(175, 175, 175);}#chatgpt-mermaid-\_r_do9\_ .edgePath .path{stroke:rgb(175, 175, 175);stroke-width:1px;}#chatgpt-mermaid-\_r_do9\_ .flowchart-link{stroke:rgb(175, 175, 175);fill:none;}#chatgpt-mermaid-\_r_do9\_ .edgeLabel{background-color:rgb(0, 0, 0);text-align:center;}#chatgpt-mermaid-\_r_do9\_ .edgeLabel p{background-color:rgb(0, 0, 0);}#chatgpt-mermaid-\_r_do9\_ .edgeLabel rect{opacity:0.5;background-color:rgb(0, 0, 0);fill:rgb(0, 0, 0);}#chatgpt-mermaid-\_r_do9\_ .labelBkg{background-color:rgba(0, 0, 0, 0.5);}#chatgpt-mermaid-\_r_do9\_ .cluster rect{fill:rgb(48, 48, 48);stroke:rgba(255, 255, 255, 0.15);stroke-width:1px;}#chatgpt-mermaid-\_r_do9\_ .cluster text{fill:rgb(237, 237, 237);}#chatgpt-mermaid-\_r_do9\_ .cluster span{color:rgb(237, 237, 237);}#chatgpt-mermaid-\_r_do9\_ div.mermaidTooltip{position:absolute;text-align:center;max-width:200px;padding:2px;font-family:-apple-system-body,ui-sans-serif,-apple-system,system-ui,"Segoe UI",Helvetica,"Apple Color Emoji",Arial,sans-serif,"Segoe UI Emoji","Segoe UI Symbol";font-size:12px;background:rgb(48, 48, 48);border:1px solid rgba(255, 255, 255, 0.15);border-radius:2px;pointer-events:none;z-index:100;}#chatgpt-mermaid-\_r_do9\_ .flowchartTitleText{text-anchor:middle;font-size:18px;fill:rgb(237, 237, 237);}#chatgpt-mermaid-\_r_do9\_ rect.text{fill:none;stroke-width:0;}#chatgpt-mermaid-\_r_do9\_ .icon-shape,#chatgpt-mermaid-\_r_do9\_ .image-shape{background-color:rgb(0, 0, 0);text-align:center;}#chatgpt-mermaid-\_r_do9\_ .icon-shape p,#chatgpt-mermaid-\_r_do9\_ .image-shape p{background-color:rgb(0, 0, 0);padding:2px;}#chatgpt-mermaid-\_r_do9\_ .icon-shape .label rect,#chatgpt-mermaid-\_r_do9\_ .image-shape .label rect{opacity:0.5;background-color:rgb(0, 0, 0);fill:rgb(0, 0, 0);}#chatgpt-mermaid-\_r_do9\_ .label-icon{display:inline-block;height:1em;overflow:visible;vertical-align:-0.125em;}#chatgpt-mermaid-\_r_do9\_ .node .label-icon path{fill:currentColor;stroke:revert;stroke-width:revert;}#chatgpt-mermaid-\_r_do9\_ .node .neo-node{stroke:rgb(31, 78, 148);}#chatgpt-mermaid-\_r_do9\_ [data-look="neo"].node rect,#chatgpt-mermaid-\_r_do9\_ [data-look="neo"].cluster rect,#chatgpt-mermaid-\_r_do9\_ [data-look="neo"].node polygon{stroke:url(#chatgpt-mermaid-\_r_do9\_-gradient);filter:drop-shadow( 1px 2px 2px rgba(185,185,185,1));}#chatgpt-mermaid-\_r_do9\_ [data-look="neo"].swimlane.cluster rect{filter:none;}#chatgpt-mermaid-\_r_do9\_ [data-look="neo"].node path{stroke:url(#chatgpt-mermaid-\_r_do9\_-gradient);stroke-width:1px;}#chatgpt-mermaid-\_r_do9\_ [data-look="neo"].node .outer-path{filter:drop-shadow( 1px 2px 2px rgba(185,185,185,1));}#chatgpt-mermaid-\_r_do9\_ [data-look="neo"].node .neo-line path{stroke:rgb(31, 78, 148);filter:none;}#chatgpt-mermaid-\_r_do9\_ [data-look="neo"].node circle{stroke:url(#chatgpt-mermaid-\_r_do9\_-gradient);filter:drop-shadow( 1px 2px 2px rgba(185,185,185,1));}#chatgpt-mermaid-\_r_do9\_ [data-look="neo"].node circle .state-start{fill:#000000;}#chatgpt-mermaid-\_r_do9\_ [data-look="neo"].icon-shape .icon{fill:url(#chatgpt-mermaid-\_r_do9\_-gradient);filter:drop-shadow( 1px 2px 2px rgba(185,185,185,1));}#chatgpt-mermaid-\_r_do9\_ [data-look="neo"].icon-shape .icon-neo path{stroke:url(#chatgpt-mermaid-\_r_do9\_-gradient);filter:drop-shadow( 1px 2px 2px rgba(185,185,185,1));}#chatgpt-mermaid-\_r_do9\_ .node text{font-size:14px;font-weight:600;letter-spacing:normal;fill:rgb(153, 206, 255);}#chatgpt-mermaid-\_r_do9\_ .edgeLabels text{font-size:13px;font-weight:600;letter-spacing:-0.08px;fill:rgb(153, 206, 255);}#chatgpt-mermaid-\_r_do9\_ .node tspan[font-weight="normal"],#chatgpt-mermaid-\_r_do9\_ .edgeLabels tspan[font-weight="normal"]{font-weight:600;}#chatgpt-mermaid-\_r_do9\_ .edgeLabel .label rect{opacity:1;rx:13px;ry:13px;fill:rgb(0, 14, 26);stroke:rgb(26, 62, 95);stroke-width:1px;}#chatgpt-mermaid-\_r_do9\_ .node rect,#chatgpt-mermaid-\_r_do9\_ .node circle,#chatgpt-mermaid-\_r_do9\_ .node ellipse,#chatgpt-mermaid-\_r_do9\_ .node polygon,#chatgpt-mermaid-\_r_do9\_ .node path{fill:rgb(0, 40, 77);stroke:rgba(255, 255, 255, 0.1);stroke-width:1px;}#chatgpt-mermaid-\_r_do9\_ .node rect{rx:16px;ry:16px;}#chatgpt-mermaid-\_r_do9\_ .mermaid-decision .label-container{fill:rgb(0, 14, 26);stroke:rgb(26, 62, 95);stroke-dasharray:2,2;}#chatgpt-mermaid-\_r_do9\_ .edgePaths .flowchart-link{stroke:rgb(175, 175, 175);stroke-width:1px;stroke-linecap:round;stroke-linejoin:round;}#chatgpt-mermaid-\_r_do9\_ .marker{fill:rgb(175, 175, 175);stroke:rgb(175, 175, 175);}#chatgpt-mermaid-\_r_do9\_ :root{--mermaid-font-family:-apple-system-body,ui-sans-serif,-apple-system,system-ui,"Segoe UI",Helvetica,"Apple Color Emoji",Arial,sans-serif,"Segoe UI Emoji","Segoe UI Symbol";}Linux Test VMSSH Failed LoginUniversal ForwarderSplunk EnterpriseDetection SearchSplunk SOAR ExportSOAR Container and ArtifactPython PlaybookVirusTotal APIIP Reputation AnalysisRisk ClassificationUpdate Container SeverityCreate Investigation NoteSMTP Notification - Pending

## **10.3. Final Infrastructure Reference**

### **Azure virtual machines**

| VM              | Operating system                             | Purpose                          | Public IP                |
| --------------- | -------------------------------------------- | -------------------------------- | ------------------------ |
| `SOC-SPLUNK-01` | Ubuntu Server 24.04                          | Splunk Enterprise                | `20.112.235.59`          |
| `SOC-SOAR-01`   | RHEL 9.4                                     | Splunk SOAR                      | `20.80.97.79`            |
| `splunktest`    | Not explicitly available in the conversation | Linux log collection and testing | Not explicitly available |

### **Application versions**

| Application                | Version   |
| -------------------------- | --------- |
| Splunk Enterprise          | 10.6.1.1  |
| Splunk Universal Forwarder | 10.6.1.1  |
| Splunk SOAR On-premises    | 6.4.1.361 |
| SMTP application           | 3.3.9     |

### **Access endpoints**

| Application       | Endpoint                    |
| ----------------- | --------------------------- |
| Splunk Enterprise | `http://20.112.235.59:8000` |
| Splunk SOAR       | `https://20.80.97.79:8443`  |

The Splunk Enterprise management port is `8089`, and the receiving port is `9997`.

Credentials, subscription identifiers, resource group names, VM sizes, and complete network configuration are not included because they were not explicitly available in the conversation.

## **10.4. Final Implementation Checklist**

15 implementation milestones confirmed

The following baseline reflects the project status explicitly provided and confirmed during implementation.

| No. | Implementation milestone                            | Status    |
| --- | --------------------------------------------------- | --------- |
| 1   | Azure Splunk Enterprise VM setup                    | Completed |
| 2   | Azure Splunk SOAR VM setup                          | Completed |
| 3   | Linux Universal Forwarder configuration             | Completed |
| 4   | SSH failed-login log forwarding                     | Completed |
| 5   | Splunk detection search creation                    | Completed |
| 6   | Splunk SOAR integration configuration               | Completed |
| 7   | Automatic event forwarding                          | Completed |
| 8   | Automatic artifact forwarding and verification      | Completed |
| 9   | VirusTotal API integration                          | Completed |
| 10  | IP reputation analysis                              | Completed |
| 11  | Risk classification                                 | Completed |
| 12  | HIGH-risk severity update                           | Completed |
| 13  | Automated investigation note creation               | Completed |
| 14  | Playbook execution successfully tested and verified | Completed |
| 15  | SMTP application installation                       | Completed |

**Additional configuration status:**

- Gmail SMTP asset configuration: In progress.
- SMTP connectivity testing: Not confirmed.
- Email notification integration into the playbook: Not confirmed.
- Successful email delivery: Not confirmed.

## **10.5. Final Technical Configuration Reference**

### **Splunk Universal Forwarder**

| Configuration  | Value               |
| -------------- | ------------------- |
| Monitored file | `/var/log/auth.log` |
| Destination    | `10.10.1.4`         |
| Port           | `9997`              |
| Index          | `security`          |
| Sourcetype     | `linux_secure`      |

### **Splunk detection search**

```
index=security sourcetype=linux_secure "Failed password"
| rex "Failed password for (invalid user )?(?<username>\S+) from (?<src_ip>\S+)"
| table _time host username src_ip _raw
```

### **SOAR integration**

| Parameter      | Value                            |
| -------------- | -------------------------------- |
| SOAR URL       | `https://20.80.97.79:8443`       |
| Server name    | `automation`                     |
| Saved search   | `SOAR - Failed SSH Login Events` |
| Schedule       | Every 1 minute                   |
| Search window  | Last 15 minutes                  |
| Event label    | `events`                         |
| Artifact label | `ssh_failed_login`               |

### **Artifact field mapping**

| Splunk field | SOAR field            |
| ------------ | --------------------- |
| `_time`      | `rt`                  |
| `host`       | `deviceHostname`      |
| `src_ip`     | `sourceAddress`       |
| `username`   | `destinationUserName` |

### **VirusTotal API**

Endpoint pattern:

```
https://www.virustotal.com/api/v3/ip_addresses/{ip}
```

Environment variable:

```
VIRUSTOTAL_API_KEY
```

Authentication header:

```
headers = {    "x-apikey": api_key,    "Accept": "application/json"}
```

### **Risk classification**

| Malicious detection count | Classification |
| ------------------------- | -------------- |
| 10 or more                | HIGH           |
| 3–9                       | MEDIUM         |
| 0–2                       | LOW            |

## **10.6. Actual Investigation Results**

The project successfully investigated the following source IP:

**Investigation result**

HIGH risk

# **193.32.162.15**

Source IP identified in the failed SSH login event

| Investigation field   | Result       |
| --------------------- | ------------ |
| Username              | `root`       |
| Hostname              | `splunktest` |
| VirusTotal reputation | -9           |
| Malicious             | 16           |
| Suspicious            | 3            |
| Harmless              | 44           |
| Undetected            | 29           |
| HTTP response         | 200          |

**Automated actions**

- SOAR container severity updated to HIGH.
- Investigation note successfully created.
- Playbook execution completed successfully.

### **SOAR execution details**

| Property              | Confirmed value              |
| --------------------- | ---------------------------- |
| Container ID          | `723`                        |
| Playbook name         | `SOC_Invalid_Login_Response` |
| Latest playbook ID    | `177`                        |
| Latest version        | `13`                         |
| Successful run ID     | `723`                        |
| Execution date        | 18 November 2026             |
| Execution time        | 23:02:35                     |
| Final status          | Success                      |
| Investigation note ID | `1`                          |

The container severity was HIGH, while the artifact row displayed MEDIUM. These are separate severity values and should not be represented as identical.

## **10.7. Understanding the Security Technologies**

A strong understanding of the technologies is important when explaining the project during technical interviews.

| Technology          | What it does in this project                             |
| ------------------- | -------------------------------------------------------- |
| SIEM                | Collects, indexes, searches, and detects security events |
| SOAR                | Automates investigation and response workflows           |
| Universal Forwarder | Collects and forwards machine logs                       |
| SPL                 | Searches and analyzes indexed Splunk events              |
| REST API            | Enables communication with VirusTotal                    |
| Python              | Implements the automated investigation logic             |
| IOC                 | Represents an indicator such as a suspicious IP address  |
| IP reputation       | Provides information about an IP's security history      |
| Incident severity   | Represents the priority of an investigation              |
| SMTP                | Provides the intended email notification mechanism       |

### **SIEM vs. SOAR**

**SIEM – Security Information and Event Management**

SIEM is responsible for collecting and analyzing security logs from different systems.

In this project, Splunk Enterprise receives Linux authentication logs and identifies failed SSH login attempts.

**SOAR – Security Orchestration, Automation and Response**

SOAR automates security investigation and response activities.

In this project, Splunk SOAR receives the detection event, queries VirusTotal, classifies the source IP, updates the container severity, and creates an investigation note.

In simple terms:

- SIEM identifies suspicious activity.
- SOAR automates the investigation and configured response.

## **10.8. Technical Interview Preparation**

The following questions are based on the actual project implementation.

### **Q1. Explain your SOC automation project.**

**Answer:**

I implemented a SOC automation lab using Splunk Enterprise, Splunk SOAR, and VirusTotal on Microsoft Azure.

I configured a Linux Universal Forwarder to collect authentication logs and forward them to Splunk Enterprise. I created a detection search to identify failed SSH login attempts and integrated Splunk with Splunk SOAR.

When SOAR receives the event, a Python playbook extracts the source IP address and queries the VirusTotal API. Based on malicious detection counts, it classifies the IP as HIGH, MEDIUM, or LOW risk. The playbook then updates the SOAR container severity and creates an automated investigation note.

### **Q2. What is the difference between Splunk Enterprise and Splunk SOAR?**

**Answer:**

Splunk Enterprise is used for log collection, indexing, searching, and security detection.

Splunk SOAR is used to automate security investigation and response workflows.

In my implementation, Splunk Enterprise detected failed SSH login events, while Splunk SOAR investigated the source IP through VirusTotal.

### **Q3. Why did you use Universal Forwarder?**

**Answer:**

Universal Forwarder is a lightweight Splunk component used to collect machine data and send it to a Splunk receiving instance.

I used it on the Linux test machine to monitor `/var/log/auth.log` and forward authentication events to Splunk Enterprise.

### **Q4. How did you detect failed SSH login attempts?**

**Answer:**

I created an SPL search using the `Failed password` pattern.

I used the `rex` command to extract the username and source IP address from the raw authentication event.

This allowed me to identify which account was targeted and which IP address generated the failed login attempt.

### **Q5. How does Splunk communicate with SOAR?**

**Answer:**

I configured the Splunk App for SOAR Export.

The integration forwards matching saved-search results to Splunk SOAR. I configured the event and artifact labels and mapped the Splunk fields to the corresponding SOAR artifact fields.

### **Q6. What is VirusTotal?**

**Answer:**

VirusTotal is a threat intelligence platform that aggregates security analysis information from multiple security engines.

I integrated its IP address API into my SOAR Python playbook to investigate the source IP address identified in a failed SSH login event.

### **Q7. How did you integrate VirusTotal with SOAR?**

**Answer:**

I used Python's `requests` library to send an HTTP GET request to the VirusTotal IP address endpoint.

The API key was stored in the SOAR environment settings using the `VIRUSTOTAL_API_KEY` environment variable.

The playbook retrieved the API response, extracted reputation information and detection statistics, and classified the source IP.

### **Q8. How did you classify an IP as HIGH risk?**

**Answer:**

I implemented a threshold-based classification system using VirusTotal's malicious detection count.

- Ten or more malicious detections: HIGH.
- Three to nine: MEDIUM.
- Zero to two: LOW.

In the actual test, the IP address `193.32.162.15` had 16 malicious detections, so the playbook classified it as HIGH risk.

### **Q9. How did you update the SOAR severity?**

**Answer:**

I used the `phantom.set_severity()` function to update the SOAR container severity according to the risk classification.

The successful execution updated the container severity to HIGH.

### **Q10. How did you document the investigation?**

**Answer:**

I used `phantom.add_note()` to create an automated investigation note inside the SOAR container.

The note contained the source IP, targeted username, hostname, VirusTotal statistics, risk classification, and investigation recommendation.

### **Q11. Did you use a VirusTotal SOAR application?**

**Answer:**

No. I implemented the VirusTotal integration directly using Python and the VirusTotal REST API.

I retrieved the API key from the SOAR environment settings and used the `requests` library to perform the API lookup.

### **Q12. Why did you use custom Python playbook mode?**

**Answer:**

I implemented the investigation logic in custom whole-playbook Python mode because it allowed me to handle artifact extraction, API communication, risk classification, severity updates, and investigation notes in one Python-based workflow.

The visual editor controls were unavailable in this mode, which was expected for the selected implementation approach.

### **Q13. What was the result of your successful test?**

**Answer:**

The playbook received the source IP `193.32.162.15`, successfully queried VirusTotal, and received HTTP status 200.

VirusTotal reported 16 malicious detections. The playbook classified the IP as HIGH risk, updated the SOAR container severity to HIGH, and successfully created an investigation note.

### **Q14. What is the purpose of SMTP in your project?**

**Answer:**

SMTP is intended to send automated security notifications to the SOC team after an investigation.

I installed the SMTP application in Splunk SOAR and started configuring Gmail as the SMTP provider. The successful email delivery test is still pending.

### **Q15. What security improvements would you make before production?**

**Answer:**

I would enable TLS certificate verification, protect API and SMTP credentials, implement duplicate-event handling, validate API responses, apply appropriate access controls, monitor notification failures, and introduce analyst approval for sensitive response actions.

## **10.9. Project Demonstration Sequence**

The following sequence is recommended when demonstrating the lab to a technical interviewer or engineering team.

1. **Show Azure infrastructure**

   Demonstrate the Splunk Enterprise and Splunk SOAR virtual machines and explain their respective responsibilities.
2. **Show Splunk Enterprise**

   Open the Splunk Web interface and explain the receiving and indexing of Linux authentication logs.
3. **Show the detection search**

   Run the failed SSH login SPL search and demonstrate extraction of the username and source IP.
4. **Show Splunk-to-SOAR integration**

   Explain the saved search, forwarding schedule, event label, artifact label, and field mappings.
5. **Show the SOAR container**

   Open container `723` and demonstrate the forwarded artifact information.
6. **Show the playbook**

   Open `SOC_Invalid_Login_Response` and explain the Python-based automation logic.
7. **Show VirusTotal results**

   Explain the API request, reputation score, and malicious detection statistics.
8. **Show the automated response**

   Demonstrate the HIGH container severity and the investigation note.
9. **Explain SMTP progress**

   Show the installed SMTP application and explain that Gmail asset configuration and email delivery validation are the remaining steps.

## **10.10. Final Project Deliverables**

The project has the following confirmed implementation outputs:

1. Azure-hosted Splunk Enterprise environment.
2. Azure-hosted Splunk SOAR environment.
3. Linux Universal Forwarder configuration.
4. SSH failed-login event collection.
5. Splunk SPL detection search.
6. Splunk-to-SOAR event forwarding.
7. SOAR container and artifact creation.
8. VirusTotal REST API integration.
9. IP reputation analysis.
10. Automated risk classification.
11. SOAR container severity update.
12. Automated investigation note.
13. Successful playbook execution verification.
14. SMTP application installation.
15. Gmail SMTP configuration preparation.

## **10.11. Final Conclusion**

The SOC Lab: Splunk Enterprise + Splunk SOAR + VirusTotal on Azure demonstrates the implementation of an automated security investigation workflow.

The project connects Linux authentication log collection, SIEM-based detection, SOAR-based automation, and external threat intelligence.

The most important verified result is the successful automated investigation of a failed SSH login event.

The playbook extracted the source IP address, queried VirusTotal, received a successful API response, classified the IP as HIGH risk, updated the SOAR container severity, and created an investigation note.

This demonstrates practical implementation experience with:

- Azure virtual machines.
- Linux authentication log collection.
- Splunk Enterprise and SPL.
- Splunk SOAR integration.
- Python automation.
- REST API integration.
- Threat intelligence investigation.
- Automated security classification.
- Incident documentation.

The remaining enhancement is to complete Gmail SMTP asset configuration, integrate the supported email action into the playbook, and verify successful email delivery.

**Final project status:** The core automated investigation workflow is completed and successfully verified. SMTP email notification remains the outstanding enhancement.

Documentation Parts 1–10 completed

**End of Part 10 – Complete SOC Lab Technical Documentation.**
