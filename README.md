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

**4. Manual remediation with notifications**
- **Manual remediation**:
  - Performed on EC2 instance whenever it goes down
- **Workflow automation**:
  - Creates a new incident record
  - Sends Slack notification to appropriate DevOps team members
  - Includes AI-powered search for relevant Knowledge Base documentation
 
![](https://github.com/CodeWithLuwam/EC2-AI-Agent-Enhancement/blob/main/Images/EC2%20Remediation%20Workfloow%20Overview.png?raw=true)
![](https://github.com/CodeWithLuwam/EC2-AI-Agent-Enhancement/blob/main/Images/Action%201%20-%20AI%20Search%20%E2%80%93%20EC2%20Knowledge%20Articles.png?raw=true)
![](https://github.com/CodeWithLuwam/EC2-AI-Agent-Enhancement/blob/main/Images/Action%202%20-%20Incident%20Record%20Creation.png?raw=true)

 ---

5. 
- Slack notifications dynamically include failed instance name and ID
