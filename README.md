# 🛠️ TryHackMe: FIXIT Splunk Challenge

## Overview

This project documents my completion of the **TryHackMe: FIXIT** lab, where I acted as a SOC-L2 candidate at a fictional company, **Cybertees Ltd**. The goal of this lab was to troubleshoot and resolve issues within a Splunk deployment related to log ingestion, multi-line event boundaries, and field extraction using custom configurations and regular expressions.

This hands-on challenge reflects real-world scenarios often encountered by Security Operations Center (SOC) analysts and showcases my ability to diagnose, fix, and analyze complex log data.

## 📚 Lab Focus Areas

- Fixing improperly parsed multi-line logs in Splunk
- Configuring `props.conf`, `fields.conf`, and `transforms.conf`
- Building custom regex patterns to extract fields from unstructured logs
- Conducting basic event analysis on the ingested data

## 🧠 Skills Demonstrated

- 🔍 **Regex Mastery**: Designed expressions to extract usernames, departments, IPs, and more  
- 🧾 **Splunk Tuning**: Used `BREAK_ONLY_BEFORE`, `SHOULD_LINEMERGE`, and `TIME_FORMAT` for log structuring  
- 🛠️ **Troubleshooting**: Diagnosed parsing errors and corrected ingestion behavior  
- 📈 **Log Analysis**: Queried and interpreted parsed data for actionable insights

## 🖥️ Tools & Technologies

- Splunk Enterprise (Web Interface & Configuration Files)  
- Linux Terminal (Config file editing)  
- Regular Expressions (Regex)  
- TryHackMe Virtual Environment  
- ChatGPT (Regex and config assistance)

## 📂 Repository Structure

```bash
FixIT-Splunk-Challenge/
├── README.md                   # This file
├── FIXIT-Lab-Writeup.pdf       # Detailed project report
├── props.conf                  # Sample config used to define event boundaries
├── field-extraction-regex.txt  # Regex pattern used to extract fields
