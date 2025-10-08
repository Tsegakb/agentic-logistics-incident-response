Agentic Logistics Incident Response Instructions
<img width="1226" height="557" alt="process_overview" src="https://github.com/user-attachments/assets/a6e32fbf-9edc-4950-b5a2-4b93ed524317" />
Project Overview

The Agentic Logistics Incident Response initiative at PepsiCo is a cutting-edge AI automation system designed to streamline the resolution of truck breakdowns during supply chain deliveries. When a disruption occurs, the system autonomously evaluates the situation, initiates rerouting or dispatches a replacement vehicle, and communicates updates to all relevant stakeholders — minimizing the need for human intervention. Powered by ServiceNow’s AI Agents and Amazon Bedrock’s generative AI capabilities, the solution enables fast, intelligent decision-making across multiple platforms. The result: accelerated incident resolution, real-time delivery updates, and significantly reduced downtime across PepsiCo’s logistics network.
Company Context
PepsiCo, a global food and beverage leader, relies on a vast supply chain to deliver products to retailers like Whole Foods. Timely deliveries are critical, as a single truck breakdown can disrupt inventory and impact revenue. As part of its digital transformation, PepsiCo is deploying autonomous AI agents in ServiceNow to accelerate incident response. These agents act as “digital teammates,” coordinating with logistics partners like Schneider and retail customers to resolve delivery issues quickly and seamlessly across company boundaries.
Business Problem
Problem: When a delivery truck breaks down, PepsiCo faces delays, stockouts, SLA breaches, and costly manual coordination. The process is slow, error-prone, and doesn’t scale.
Solution: Agentic Logistics Incident Response automates the entire lifecycle. AI agents in ServiceNow detect the incident, assess impact, select the best reroute, and coordinate execution with partners like Schneider and Whole Foods. The system updates stakeholders in real time, transforming a reactive process into a proactive, scalable workflow that reduces downtime and boosts reliability.
Architecture Overview
![AD](https://github.com/user-attachments/assets/bf45e717-17e8-4776-a326-b4b57254c8cd)

The architecture features three AI agents across ServiceNow and n8n, working in a unified agentic framework. Agents 1 and 2 run in ServiceNow: one analyzes SLA impact, the other selects the best reroute using NowLLM. Agent 3, hosted in n8n, orchestrates external communications via MCP — a standardized protocol for AI-to-system integration. It sends reroute instructions to Schneider and delay notifications to Whole Foods. Key ServiceNow tables (Supply Agreement, Incident, Delivery Delays) are updated throughout the process, ensuring full visibility and traceability of the incident lifecycle.
Tables/ Outputs in ServiceNow
ServiceNow tracks the incident lifecycle using three key records: Supply Agreement (penalty rates), Incident (breakdown event), and Delivery Delays (shipment status). As orchestration progresses, Delivery Delays is updated from pending to dispatched, with notes on actions taken, delay duration, new delivery time, and the reason — ensuring full downstream visibility.
Delivery Delay Table.
![1dd](https://github.com/user-attachments/assets/c03f27a7-6148-4220-9ea2-0a3a58aaae7d)
Incident Table.
![12dd](https://github.com/user-attachments/assets/adbdcd50-cf87-4d25-8e20-42d95e0222a5)
Supply Agreement Table.
![13sa](https://github.com/user-attachments/assets/0aac66be-9318-44b4-be66-bc6f950614b3)

The system is modular and extensible. ServiceNow AI Agents handle decisions and record updates, while n8n (Agent 3) manages external orchestration via secure, standardized HTTP/MCP interfaces. As PepsiCo adds new partners, new endpoints can be integrated without changing core logic — the agents simply gain new tools to call.

Setup Requirements
To deploy this solution, ensure the following:
• 	ServiceNow Instance: Use a Vancouver or later release with AI Agent Studio and Now Assist enabled for generative AI workflows.
• 	Amazon Bedrock Access: Secure AWS credentials with Bedrock API permissions. Confirm regional access and set up cost controls.
• 	n8n Environment: Host Agent 3’s workflow in an accessible n8n instance. Install MCP Client nodes to connect with external systems.
• 	MCP Server Access: Coordinate with Schneider and Whole Foods to obtain MCP endpoint URLs and credentials. Use secure storage for API keys. Simulate endpoints if needed for testing.
• 	ServiceNow to n8n Integration: Trigger Agent 3 via outbound REST calls from ServiceNow AI Agents to n8n’s webhook, passing incident data and routing plans.
• 	Security: Store all sensitive credentials (AWS, MCP) in secure vaults or credential managers.

Usage Guide (Step-by-Step)
To handle a truck breakdown:
1. 	Trigger the Incident: Log a breakdown in ServiceNow’s Delivery Delays and Supply Agreements tables — either automatically from the truck or manually by a dispatcher.
2. 	Include Key Details: Ensure the record has the truck ID, route ID, breakdown location, and affected customer.
3. 	Activate the Workflow: Setting the delivery delay status to pending triggers the AI Use Case, launching the autonomous incident response.
New Delivery Delay.
![dd2](https://github.com/user-attachments/assets/1eecbdac-87a1-4be1-97c2-e18a0714a8a5)
Use Case
![UC](https://github.com/user-attachments/assets/ad42fe18-f778-4055-aa26-faead3b7606a)

Agent 1 – SLA Review
Once a breakdown is logged, Agent 1 activates to assess financial impact. Using NowLLM, it analyzes SLA terms and delay penalties (e.g., $250/hour). If a reroute exceeds the SLA, it calculates the breach cost, creates an incident for tracking, and logs impact details across rerouting options in the Delivery Delays table.
Agent.1
![A1](https://github.com/user-attachments/assets/0bdb6c6e-65d6-442b-8972-424f6ac3f569)
Agent1 Tools
![AT1](https://github.com/user-attachments/assets/040acf9c-83a3-4b12-a14c-b3de00ac31c0)
Agent 2 
After Agent 1’s analysis, Agent 2 selects the best reroute option based on lowest penalty and shortest delivery time. It updates the Incident’s impact and urgency, assembles a JSON payload with key details, and sends it to Agent 3 via webhook for execution.
![A2](https://github.com/user-attachments/assets/b78825d6-eea8-4eb6-a3d8-ed2c25784925)
Agent2 tools
![A2T](https://github.com/user-attachments/assets/d597bf5f-3dbc-48ba-b981-dc75a3c5774a)
Agent 3 –(n8n)
Once a reroute plan is ready, Agent 3 executes it via an n8n workflow powered by AWS Bedrock. It sends structured MCP requests to Schneider (carrier) with reroute details, and to Whole Foods (customer) with delivery updates — ensuring secure, standardized communication across systems.

![n8n](https://github.com/user-attachments/assets/16198cd9-c981-4db7-a92b-fecb7aa27db5)
ServiceNow Update  – Completion
After executing the reroute, Agent 3 calls back to ServiceNow to update the incident status to Dispatched. The Delivery Delays dashboard reflects the confirmed delay, reason, and new ETA. The ITSM view shows the incident as resolved with notes from the AI agents. The entire process runs autonomously unless an error occurs — mimicking a human team, but faster and more consistent.


![Agent2 R](https://github.com/user-attachments/assets/192e4faf-da20-4e1e-b9fe-fb5f06f81461)





After the incident workflow runs, the Delivery Delays table updates from “on-time” to “delay pending,” and finally to Delayed with a confirmed ETA and notes like “Mitigated by dispatching backup truck.” This before-and-after record confirms successful issue resolution
Delivery Delays Table After
![ddl](https://github.com/user-attachments/assets/6fd399f9-85a2-4f3a-b8db-93aa88be8f7f)

he Incident table reflects updated urgency and impact. Agent 3’s execution appears in n8n logs, and ServiceNow logs AI activity for traceability. Stakeholders like Whole Foods and PepsiCo are notified with the new ETA. The reroute is completed, records updated, and the process fully documented — ensuring minimal downtime and high transparency.


Proposed Optimizations
• 	Smart Notifications: Alert via Slack/email and tag route_id as “alerted” for 30 mins to prevent spam; check for duplicate incidents.
• 	Retry Logic: Add exponential backoff for failed MCP calls.
• 	Observability: Build dashboards to track logs, status codes, latency, failure rate, and dispatch time.
• 	Auto Assignment: Route incidents to available users in the correct group.
• 	Auto Resolution: Close incidents once delivery is confirmed by the customer.

