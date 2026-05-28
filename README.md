# SIEM Alerting & Actions Pipeline with Elastic Stack
This project demonstrates the implementation of an automated Security Information and Event Management (SIEM) alerting pipeline within a controlled lab environment. By configuring **Kibana Alerting & Actions**, the system actively monitors infrastructure logs shipped via **Filebeat** to identify anomalies, security events, or system errors in real-time. Upon breaching defined operational thresholds, the system automatically triggers an outbound **SMTP mail connector** to alert Security Operations Center (SOC) analysts or system administrators.

---

## 🛠️ Architecture & Tech Stack
* **SIEM Platform:** Elastic Stack (Elasticsearch & Kibana)
* **Log Shipper:** Filebeat (collecting Linux `/var/log/auth.log` and `/var/log/syslog`)
* **Operating System:** Ubuntu Server (Lab Environment)
* **Notification Protocol:** SMTP (Secure TLS Mail Gateway via App Passwords)

---

## 🚀 Lab Walkthrough & Tasks

### Task 1: Navigating to Security Analytics Management
1. Accessed the local Kibana instance via the web interface.
2. Navigated to **Management** ➡️ **Stack Management** ➡️ **Alerts and Insights** ➡️ **Rules** to initialize the alerting workspace.

### Task 2: Building the Secure SMTP Email Connector
Configured a reliable integration to handle security notifications securely through mail gateways:
* **Connector Type:** Email / Custom SMTP
* **Network Protocol Configuration:**
    ```json
    {
      "host": "smtp.gmail.com",
      "port": 587,
      "secure": false, 
      "username": "your-username@gmail.com",
      "password": "••••••••••••••••"
    }
    ```

#### 🛡️ Verification: SMTP Integration Status
![SMTP Connector Configured](images/Screenshot%20(103).png)

---

### Task 3: Developing the Detection Rule & Threshold Logic
Constructed an **Elasticsearch Query Rule** targeting system exceptions:

* **Data Scope Data View:** `filebeat-*`
* **Detection Query (KQL):** `event.outcome : "failure"`
* **Threshold Condition Sentence:**
    > **WHEN** `count()` **IS ABOVE** `2` **FOR THE LAST** `15 minutes`
* **Evaluation Interval:** Checked every `1 minute`.

#### 🛡️ Verification: Rule Parameters & Log Compiling
![Kibana Rule Query Parameters](images/Screenshot%20(111).png)

---

### Task 4: Attack Simulation & Incident Verification
To stress-test the pipeline, security event exceptions were generated directly on the monitored host machine by intentionally forcing authentication failures and application error flags.

#### 🛡️ Verification: SIEM Rule Lifecycles (Active vs. Recovered)
Once the threshold was breached, Kibana's rule console caught the spike, flagged the alert status as **Active**, and seamlessly shifted to **Recovered** once telemetry normalized.

![Rule Firing History](images/Screenshot%20(113).png)

---

## 🔍 Final Proof of Work: Automated SOC Notification
The climax of the pipeline verification. Below is the live security email dispatched into the administrator's mailbox, parsed directly from the live telemetry payload variables via Mustache syntax:

![Live Email Alert Received](images/Screenshot%20(116).png)

---

## 💡 Key Takeaways
This lab highlights the power of setting up automated rules in security monitoring. Utilizing broad indices like `filebeat-*` allows administrators to gain visibility across multiple services simultaneously. Tuning rules correctly—by matching the schedule run times with appropriate lookback window sizes—ensures crucial events are captured without dropping critical telemetry data or overwhelming analysts with email spam.
