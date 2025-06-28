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
| 🔍 **Deeper Investigation** | Analyze system logs for signs of privilege escalation |

---

## 🔍 Deeper Investigation: Signs of Privilege Escalation

The next step in the investigation is to go deeper into the post-compromise activity.  
We already confirmed a **brute-force attack**, so it's reasonable to assume the attacker may attempt **lateral movement** or **privilege escalation** to gain full control over the system.

> 🧠 **At this stage, we must think like an attacker but act like a defender.**

![Privilege Escalation Alert](https://github.com/user-attachments/assets/be9b75c9-3cec-495b-8077-3893ef3eb0c8)

---

### 🚨 Triggered Alert

During log analysis, a Wazuh rule was triggered:

> **Alert:** `Successful sudo to ROOT executed`

This alert indicates that a **regular user** successfully executed a command with **root privileges** using `sudo`.

From the full logs, we extracted key metadata:
- `data.srcuser`: The compromised user account
- `data.pwd`: The working directory from which the command was executed

This confirms that the attacker has gained **root access** — a critical escalation.

---

### 🛡️ Immediate Response Actions

To contain the threat and minimize damage, the following actions were taken:

- 🔌 **Isolate the system** to prevent further lateral movement or data exfiltration
- 📢 **Notify the Incident Response (IR) team** immediately
- 🔺 **Escalate the incident** as a **high-severity breach**

---

### 📁 File Integrity Monitoring (FIM) Observations

![IR Escalation](https://github.com/user-attachments/assets/47bdd577-aa00-45c8-b7fc-4d9738c8331a)

After escalating to the IR team, further investigation using **File Integrity Monitoring (FIM)** revealed:

- 📄 A **suspicious Python file** was added to the system
- 🔐 The **checksums of critical system files** had changed shortly afterward

This strongly suggests the attacker:
- Uploaded or created a malicious script
- Modified system binaries or configuration files
- Attempted to maintain **persistence**, hide activity, or prepare for further exploitation (e.g., rootkit installation)

---

## 🧠 Conclusion

The combination of:
- Brute-force login success
- Sudo privilege escalation
- File system encryption

...confirms a **successful compromise** with **root-level access**.

> The system should now be considered untrusted and must be fully reimaged from known good backups.

Further actions include:
- 🧼 Full forensic imaging (memory + disk)
- 🔁 Rotation of credentials and SSH keys
- 📊 Updating detection rules and baselines
- 📄 Writing and sharing an incident report

