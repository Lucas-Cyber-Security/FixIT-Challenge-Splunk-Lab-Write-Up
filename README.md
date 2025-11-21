
# FixIt Splunk Challenge — SOC Analyst Walkthrough

## Overview
This project is my hands-on walkthrough of the **FixIt AI-Assisted Splunk Challenge**, a scenario designed to simulate real-world SOC responsibilities involving log ingestion, parsing failures, field extraction, and event investigation.

The goal of this write-up is to document:
- The technical issues I encountered
- How I diagnosed and corrected Splunk ingestion problems
- The regex and props.conf changes used to fix multi-line and improperly parsed events
- The steps I took to validate data integrity and restore analyst visibility
- Key lessons learned and how they apply to real-world SOC environments

---

## 🛠️ Environment & Data Sources
**Tools Used:**
- Splunk Free (local deployment)
- Windows event logs
- Sysmon operational logs
- Sample malformed logs provided by the FixIt challenge

**Core Focus Areas:**
- Multi-line event parsing
- Field extraction using regex
- Data normalization for search and dashboards
- Triage workflow and detection logic

---

## 🔍 Initial Problem Summary
When ingesting the provided logs, several issues were immediately clear:

1. **Events were merging into single long blobs**, breaking timestamps and splitting fields incorrectly.
2. **Key fields (username, department, domain, src_ip, country)** were not extracting due to missing props/transforms configuration.
3. **Searches returned inconsistent results**, with some logs not showing up in index-time searches.

The root cause: Incorrect event boundaries and missing field extraction rules.

---

## 🧪 Step 1: Fixing Multi-Line Event Boundaries
Splunk sometimes misinterprets Windows logs or application logs when they span multiple lines.

To correct this, I updated **props.conf** with explicit line-breaking logic:

```ini
[your_sourcetype]
SHOULD_LINEMERGE = false
LINE_BREAKER = ([
]+)
TRUNCATE = 0
TIME_FORMAT = %Y-%m-%d %H:%M:%S
TIME_PREFIX = ^\[
````

This forced Splunk to:

* Treat each event as independent
* Break lines correctly
* Parse timestamps reliably

After re-ingesting the logs, I confirmed **correct event counts** using:

```spl
| metadata type=sourcetypes
```

---

## 🧪 Step 2: Creating Regex-Based Field Extractions

The FixIt dataset required extracting the following fields:

* `username`
* `department`
* `domain`
* `src_ip`
* `country`

Example extraction written using Splunk Field Extractor + manual regex refinement:

```regex
User=(?<username>[A-Za-z0-9._-]+).*?Dept=(?<department>[A-Za-z]+).*?Domain=(?<domain>[A-Za-z0-9.-]+).*?SrcIP=(?<src_ip>[0-9.]+).*?Country=(?<country>[A-Za-z]+)
```

I validated extraction coverage with:

```spl
| stats count by username, department, domain, src_ip, country
```

---

## 🧪 Step 3: Restoring Analyst Searchability

With ingestion and fields fixed, I rebuilt dashboards and confirmed expected behavior.

Validation queries:

### Show event distribution

```spl
index=fixit_logs
| timechart count by sourcetype
```

### Check for missing or malformed fields

```spl
index=fixit_logs
| where isnull(username) OR username=""
| stats count by sourcetype
```

### Verify normalization

```spl
index=fixit_logs
| stats values(username) values(src_ip) by _time
```

This confirmed clean, searchable data for SOC triage.

---

## 🛡️ Step 4: Writing the Triage Playbook

I documented a SOC-ready triage workflow covering:

### 1. **Alert Trigger**

Identify suspicious login or anomalous activity based on:

* Impossible travel
* Department mismatch
* Failed logon spikes

### 2. **Initial Query**

```spl
index=fixit_logs username="<user>"
| stats count, values(src_ip), values(country), values(department)
```

### 3. **Contextual Enrichment**

Cross-reference with:

* Known geolocation patterns
* Department alignment
* Past login behavior

### 4. **Escalation Criteria**

Escalate if:

* Login from unusual country
* Source IP linked to other anomalies
* Privileged account activity

---

## 📈 Key Lessons Learned

### ✔ Importance of proper event boundaries

Even perfect data becomes unusable if Splunk can’t break lines correctly.

### ✔ Regex must match real-world messy log formats

Field extraction is often iterative — test, refine, test again.

### ✔ Verification queries are your safety net

Always confirm ingestion **before** building alerts.

### ✔ Documentation increases repeatability

Writing a triage playbook ensures analysts handle incidents consistently.

---

## 📦 Repository Structure

```text
FixIt-Splunk-Challenge/
├── Splunk-Lab-Writeup.pdf               # Full Writeup follow through
├── props.conf          # Line-breaking and parsing fixes
├── field-extraction-regex.txt    # Field extraction regex
├── README.md           # This document
└── queries/            # SPL queries used during the project
```


## 🧭 Final Thoughts

This project sharpened practical SOC skills that directly transfer to real-world roles:

* Debugging ingestion issues
* Writing and validating field extractions
* Normalizing logs for search and dashboards
* Designing triage workflows

