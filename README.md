# EC2 AI Agent Enhancement System – ServiceNow Implementation  

## 📖 System Overview  

After Netflix deployed the **EC2 Monitoring and Remediation system (WL2)**, the DevOps team immediately saw value. EC2 failures triggered automatic incidents, Slack notifications carried AI-surfaced knowledge articles, and engineers could manually trigger remediation with a single click.  

But a new challenge emerged. During peak streaming hours—like when a hit new series dropped—multiple EC2 instances could fail at once. Engineers had to open each incident, read through descriptions, extract the instance ID, and click the remediation button. This manual bottleneck slowed down response times when speed mattered most.  

To solve this, we built the **EC2 AI Agent Enhancement (WL3)**:  
- An **AI-powered conversational assistant** that reads incidents, extracts EC2 instance IDs, confirms them with engineers, and executes remediation with human approval.  
- Engineers now have **two complementary workflows**: the proven WL2 manual path and the faster, AI-driven conversational path.  
- Both approaches converge on the same AWS Integration Server API, ensuring consistency, reliability, and unified logging in the Remediation Log.  

This system blends **automation with human oversight**—balancing Netflix’s need for speed with its commitment to safe, controlled operations.  

---

## 🛠️ Implementation Steps  

Building this system required carefully layering AI Agent functionality on top of the WL2 foundation:  

1. **AI Agent Creation**  
   - Name: `EC2 Remediation Assistant`  
   - Role: EC2 remediation specialist for DevOps operations  
   - Instructions:  
     1. Read incident records (short description).  
     2. Extract EC2 instance IDs (pattern: `i-xxxxxxxx`).  
     3. Confirm with engineer before proceeding.  
     4. Execute remediation script via API with human approval.  

   ![AI Agent Configuration](Images/EC2RemediationAssistant.png)  

2. **EC2 Instance Incident Report**  
   - Reads incident table (e.g., `INC0010014`) and parses the short description for valid EC2 IDs.  
   - Returns a **clean `instance_id`** to avoid passing entire text strings.  

   ![Incident Lookup Tool](Images/EC2InstanceIncidentReport.png)  

3. **EC2 Remediation Tool**  
   - Reuses the same logic from WL2’s `EC2RemediationHelper`, adapted as an AI Script Tool.  
   - Input: `instance_id`.  
   - Execution: Calls AWS Integration Server API, logs attempt in the Remediation Log.  

   ![Remediation Tool](Images/EC2RemediationTool.png)  

4. **Agent Integration**  
   - Tools (Lookup + Remediation) linked to the AI Agent.  
   - Execution mode: **Supervised** to ensure human approval is always required.  

   ![Agent Tool Integration](Images/AgentTools.png)  

5. **Testing and Validation**  
   - Conversational query: *“Help me with incident INC0010014.”*  
   - Agent extracts: `i-09ae69f1cb71f622e`.  
   - Asks: *“Do you want me to restart this instance?”*  
   - On approval, remediation executes and is logged identically to WL2.  

---

## 🗺️ Architecture Diagram  

### Combined WL2 + WL3 Flow  

