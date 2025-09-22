# EC2-AI-Agent-Enhancement
The EC2 Remediation System integrates AWS with ServiceNow to streamline incident response. When a server goes offline, the event is automatically logged in ServiceNow tables, triggering a workflow that notifies DevOps and provides remediation guidance. An AI agent further optimizes this process by assisting DevOps engineers either by retrieving remediation steps for a given incident (Example: `INC0012345`) or instance number (Example: `i-0abcd1234efgh5678`), or by executing the UI Action to restart the instance directly.

**1. Scoped application: EC2 Monitoring and Remediation**
- **Tables to create**:
  - **EC2 Instance table**: 
    - Columns include: Instance Name, Instance ID, and Instance Status <br>
![](https://github.com/CodeWithLuwam/EC2-AI-Agent-Enhancement/blob/main/Images/EC2%20Instance%20Table-%20AI%20Agent%20Studio%20-%20Update%20Set.png?raw=true)
  - **Remediation Log table**:
    - Columns: Attempted Status, Success, Timestamp, Request Payload, Response Payload, Response Time, HTTP Status Code, Error Messages
    - References the **EC2 Instance table**
  
  ---
**2. AWS integration for automated remediation calls**
- **Credential alias (AWS Integration Server C C Alias)**:
  - Defines access reference for integration <br>
![](https://github.com/CodeWithLuwam/EC2-AI-Agent-Enhancement/blob/main/Images/AWS%20Connection%20&%20Credential%20Alias%20-%20Update%20Set.png?raw=true)
- **HTTP connection (AWS Inttegration Server Connection)**
  - Connects to AWS Integration Server <br>
![](https://github.com/CodeWithLuwam/EC2-AI-Agent-Enhancement/blob/main/Images/AWS%20Connection%20record%20-%20Update%20Set.png?raw=true)
- **Basic authorization credentials (AWS Integration Server Credentials)**
  - Provides authentication for integration server <br>
![](https://github.com/CodeWithLuwam/EC2-AI-Agent-Enhancement/blob/main/Images/AWS%20Credentials%20(Basic%20Auth%20Type)%20-%20Update%20Set.png?raw=true)

---
**3. Manual remediation via UI Action**
  - **Form button (Trigger EC2 Remediation**):
    - Created as a UI Action on the EC2 Instance record form
  - **Script Include (EC2 Remediation Helper)**:
    - Called by the UI Action to send API request
- **AWS Integration Server**:
    - Receives the API call
    - Reestablishes connection with downed EC2 instance
    - Test & log results for success/error
      
![](https://github.com/CodeWithLuwam/EC2-AI-Agent-Enhancement/blob/main/Images/UI%20Action%20-%20Trigger%20EC2%20Remediation%20form%20button.png?raw=true)

---

**4. Automated remediation with notifications** <br>

The **Remediation Notification and Workflow** is designed to automatically respond whenever an EC2 instance goes offline. <br>

**Trigger:** <br>
The workflow watches the EC2 Instance table (`x_snc_ec2_monito_0_ec2_instance`). It fires whenever a record is **created or updated** and the **Instance status = OFF**. Because it is set to run on every update, the workflow will re-trigger each time the record changes while still offline (not just the first time it goes OFF). This ensures no offline event is missed, but can also generate repeated runs if the instance stays down and continues to update. <br>
![](https://github.com/CodeWithLuwam/EC2-AI-Agent-Enhancement/blob/main/Images/EC2%20Remediation%20Workfloow%20Overview.png?raw=true) <br>

**Actions:** <br>
Once the trigger condition is met, the workflow runs three key steps: <br>

**1. AI Search (Knowledge Lookup):** <br>
Automatically queries the ServiceNow Knowledge Base for EC2-related troubleshooting articles. These results are attached to the incident or provided to DevOps for immediate reference. <br>
![](https://github.com/CodeWithLuwam/EC2-AI-Agent-Enhancement/blob/main/Images/Action%201%20-%20AI%20Search%20%E2%80%93%20EC2%20Knowledge%20Articles.png?raw=true) <br>

**2. Incident Creation:**
Creates a new **Incident [incident]** record with contextual details:
  - **Short description**: names the failing EC2 instance
  - **Status**: captures the current state (e.g., OFF)
  - **Instance ID**: stores the AWS identifier
This ensures outages are logged, tracked, and visible to DevOps. <br>
![](https://github.com/CodeWithLuwam/EC2-AI-Agent-Enhancement/blob/main/Images/Action%202%20-%20Incident%20Record%20Creation.png?raw=true) <br>

**3. Slack Notification:** <br>
Sends a message via Slack webhook to notify the team. The alert can include knowledge article links retrieved by AI Search (Action 1), and can be customized with username, channel, and icon for visibility. <br>
![](https://github.com/CodeWithLuwam/EC2-AI-Agent-Enhancement/blob/main/Images/Action%203%20-%20Slack%20Notification.png?raw=true) 

 ---

**5. Issue Detected & DevOps Notified** <br> 
Slack notifications dynamically include failed instance name and IDAfter an issue is detected, the AWS Critical Incident Helper workflow triggers. The workflow identifies a remediation action and sends a Slack notification with the relevant knowledge base article (KB0010501: Trigger EC2 Remediation). The DevOps team is then immediately alerted with the remediation instructions and can run the ‘Trigger EC2 Remediation’ UI action in ServiceNow to resolve the EC2 instance issue. <br>
![](https://github.com/CodeWithLuwam/EC2-AI-Agent-Enhancement/blob/main/Images/Issue%20Detected%20&%20DevOps%20Notified.png?raw=true)
