## 🔍 Attack-Chain-Analysis  
### **"From External Brute Force to Internal Privilege Abuse and Ransomware"**

This repository presents a comprehensive analysis of a simulated multi-stage cyber attack, illustrating a realistic intrusion scenario commonly encountered by Security Operations Centers (SOCs).

The attack begins with an **external brute force attempt** targeting an SSH service, successfully gaining unauthorized access to a vulnerable system. Once inside, the attacker escalates privileges to obtain administrative control, paving the way for the final stage: the **deployment of ransomware** that encrypts sensitive data.

This end-to-end attack chain is designed to emulate **real-world adversary tactics, techniques, and procedures (TTPs)** and highlights the critical importance of **early detection**, **event correlation**, and **incident response**. Leveraging **Wazuh** as the SIEM and endpoint security agent, this project showcases how security teams can **monitor, detect, and analyze** such attacks using **log data** and **alerting mechanisms**.

---

## 📊 Dashboard Overview

Let’s first take a look at the SOC dashboard to understand the current state of the system and gain initial insights:

![Dashboard Image](https://github.com/user-attachments/assets/dbbfd476-7797-4786-bf37-ae4c46dba3ab)

From the dashboard, we observe the following:

- A total of **687 alerts**
- Out of which, **578 are authentication failures**
- These failures occurred **within a short time interval**

### 🧠 Hypothesis:
At first glance, this pattern strongly suggests a **brute force attack**. The **Indicators of Compromise (IOCs)** supporting this include:

- High volume of failed login attempts
- Alerts associated with `sshd`, `authentication fail`, and `invalid login`
- Short time span between failed attempts

But is this information alone **enough** to confirm a brute force attack? **Probably yes**, but let’s continue the investigation to validate our hypothesis.

---

## 🧪 Log and IOC Analysis via Wazuh

We now examine the logs and alerts collected by Wazuh for further evidence:

![Log Analysis Image](https://github.com/user-attachments/assets/14cd45b7-2585-45f2-98c3-c091376d0aa0)

Wazuh generates an **alert of severity level 10**, based on default detection rules. This level indicates:

> **"Multiple user-generated errors such as repeated bad passwords and failed logins"**

### ✅ Observation:
- This aligns perfectly with the signature of a brute force attack.
- Wazuh’s internal rules have already classified this event accordingly.

### ✅ Hypothesis Verified:
We can **confidently confirm** this is a brute force attack scenario. Moreover, in the **worst-case**, the attacker may have already logged in using valid credentials and could be attempting **privilege escalation** or **lateral movement**.

---

## ⚠️ Immediate Response

Once confirmed, the SOC should take the following actions:

| Action | Description |
|--------|-------------|
| 🔐 **Block Attacker IP** | Use firewall or Wazuh active responses to block the source IP address. |
| 🧑‍💻 **Disable Targeted User Account** | Temporarily disable the account targeted in the brute force attempt. |
| 🔍 **Deeper Investigation** | Analyze system logs for signs of privilege escalation or ransomware execution. |

---


![Image](https://github.com/user-attachments/assets/be9b75c9-3cec-495b-8077-3893ef3eb0c8)

