# Report-CN-MINIPROJECT
Department of Computer Engineering and Technology

MINI PROJECT REPORT

Computer Networks Laboratory

(CSE20110)

Laboratory Continuous Assessment 3

 

Network Health Monitoring System

 

Submitted by

Sr. No.
Student Name
PRN
1
Pouras Thamke

1272250925

2
Arnav Bhimanwar

1262242976

3
Ameya Palamwar

1262242857

4
Abhishek Walande

1262243036

 

Group No. 23     Panel K     Batch 3

Third Year B.Tech. Computer Engineering and Technology

Under the guidance of

Dr. Uma Pujeri

Academic Year 2026–2027

Submission Date 20 October 2026





Department of Computer Engineering and Technology

CERTIFICATE

 

This is to certify that the mini-project entitled “Network Health Monitoring System” has been carried out by the following students of Third Year B.Tech. Computer Science and Engineering, Panel K, Batch 3, Group No. 23, under the guidance of Dr. Uma Pujeri during the academic year 2026–2027.

Student Name
PRN
Pouras Thamke

1272250925

Arnav Bhimanwar

1262242976

Ameya Palamwar

1262242857

Abhishek Walande

1262243036

 

The report is submitted towards the mini-project component of Laboratory Continuous Assessment 3 for the Computer Networks Laboratory course.

 

Faculty Signature __________________________

Dr. Uma Pujeri
Project Guide and Course Faculty

Date ____________________     Place: Pune



Acknowledgement
This report presents the work completed for the Network Health Monitoring System mini-project in the Computer Networks Laboratory. We acknowledge the academic framework and laboratory learning environment provided by the Department of Computer Engineering and Technology. The project work and report were prepared collaboratively by the team members listed in this report.

Group No. 23

 
 
 
 
 
 
 
 
 
 

 

Abstract
The Network Health Monitoring System is an educational application developed to automate repeated reachability checks for multiple network hosts and present the observations in one dashboard. The application uses Python, Streamlit and pandas. Its monitoring module invokes the operating system’s ping utility to send ICMP echo requests and parses the command output to estimate availability, round-trip delay and packet loss. The dashboard allows users to configure targets, monitoring cycles, packet count and alert thresholds. It reports a host as UP when at least one ping reply is received during a cycle, and creates alerts when a host does not reply, the parsed average delay exceeds the configured threshold, or packet loss meets or exceeds the selected threshold. Results are shown as status metrics, tables and history charts, with CSV export for further inspection. In the supplied dashboard screenshot, the two configured targets, 127.0.0.1 and 8.8.8.8, are shown as UP, with three of three replies each and a dashboard average delay of 3.5 ms; the displayed packet loss is 0% for both targets. Optional CPU, memory and interface-counter fields are simulated for demonstration and are not obtained from real SNMP agents. The project demonstrates core network-monitoring concepts while retaining important limitations: ICMP may be filtered, ping-output parsing is platform-dependent, and monitoring runs only during an active application run.

Keywords: ICMP, network monitoring, packet loss, round-trip time, Streamlit

 
 



Table of Contents
1. Introduction and Project Objectives
2. Proposed Design and Requirements
3. Implementation and Technical Working
4. Testing and Results
5. Discussion and Conclusion
References
AI Usage Declaration
Appendices

List of Figures
Figure 2.1 Network Health Monitoring System architecture
Figure 3.1 Live Streamlit dashboard during monitoring
Figure 4.1 Monitoring results dashboard
Figure 4.2 Threshold-triggered alerts dashboard

List of Tables
Table 1.1 Project objectives and verification
Table 2.1 Tools and resources
Table 2.2 Target addressing and roles
Table 4.1 Test plan and observed results
Table 4.2 Objective achievement

 
 



1 Introduction and Project Objectives
1.1 Introduction and Background
Network monitoring is the repeated observation of hosts and network behaviour to help identify reachability problems and changes in performance. The Internet Control Message Protocol (ICMP) supports diagnostic messages used by the commonly available ping utility. Ping sends Echo Request messages and measures the round-trip time for replies when they arrive. The application uses the operating system’s ping command rather than implementing ICMP packets directly. Python’s subprocess facilities allow the program to invoke an external command and collect its output [1]. Streamlit provides the interactive application interface and dashboard widgets [2]. The project uses these tools to connect a configurable user interface with a repeatable monitoring workflow. The protocol concept and message type are described in the ICMP specification, RFC 792 [3].

1.2 Problem Statement
Checking several hosts manually with separate ping commands is repetitive. It also makes it harder to compare observations across cycles or consistently apply alert thresholds. This project addresses that limited problem by collecting repeated ping-derived observations for user-specified targets, presenting the latest status and history in one dashboard, and exporting the collected rows for later review.

1.3 Scope
The intended users are students and learners studying computer networking and basic network diagnostics. The system checks hostnames or IP addresses entered by the user and relies on the host computer’s installed ping utility. The scope includes reachability estimates, average ping round-trip delay, packet-loss estimates, threshold alerts, charts and CSV export. It does not measure application-layer service health, does not continuously run as a background service after the monitoring run ends, and does not contact real SNMP agents. SNMP-style CPU, memory and interface counters are explicitly simulated.

1.4 Objectives
To implement a configurable dashboard for monitoring multiple hosts over repeated ICMP ping cycles; to estimate availability, average round-trip delay and packet loss from ping output; and to generate threshold-based alerts while presenting historical observations and CSV exports.

Objective ID
Objective
Verification Method
O1

Monitor multiple hosts over repeated ICMP ping cycles.

Dashboard shows target rows and latest status.

O2

Estimate availability, average RTT and packet loss.

Displayed sent/received counts, delay and loss fields.

O3

Generate threshold alerts and export observations.

Code review confirms alert logic and CSV download controls; alert-trigger evidence not supplied.

 



2 Proposed Design and Requirements
2.1 Architecture and Network Topology
The user enters targets and monitoring settings in the Streamlit interface. app.py coordinates the monitoring cycles and calls functions from monitor.py. The monitoring module invokes the operating system ping utility for each target, then parses the output to estimate sent and received packets, packet loss and average round-trip delay. Results are checked against the configured thresholds and returned to the dashboard, which displays the latest status, charts, alerts and downloadable CSV files.



Figure 2.1. Architecture and data flow of the Network Health Monitoring System.

2.2 Tools and System Requirements
The project was run on Windows 11 as reported by the team. The exact Python version and hardware specifications were not provided. Python, Streamlit and pandas are the main software components; monitor.py uses Python standard-library facilities including subprocess, platform, re and datetime. The operating system’s ping command is required. No specific hardware model is required beyond a computer capable of running Python and a web browser.

Tool or Resource
Version or Specification
Purpose
Python

Version not recorded

Application logic and monitoring

Streamlit

Version not recorded

Interactive web dashboard

pandas

Version not recorded

Tabular results and CSV preparation

OS ping utility

Windows 11 reported

ICMP reachability and RTT summaries

 

2.3 Addressing and Protocol Selection
This is a host-monitoring application rather than a network-topology configuration project. The targets below are the two hosts visible in the supplied dashboard screenshot. Gateway and subnet-prefix details are not required by the application and were not recorded.

Device / Interface
IP / Prefix
Gateway
Role
127.0.0.1

Loopback; prefix N/A

Not recorded

Local host test target

8.8.8.8

IPv4; prefix not configured here

Not recorded

External target shown in screenshot

 

ICMP ping was selected because it is widely available for basic reachability diagnostics. The project calls the platform’s ping executable with platform-specific arguments and uses a best-effort parser for common Windows, Linux and macOS output. A target is marked UP if at least one reply is received in the monitoring cycle; no reply produces a DOWN result in the current implementation. This is only a ping-based estimate and does not prove that every service on the host is unavailable. Thresholds are configurable so the user can demonstrate alert behaviour. Optional SNMP-style values are labelled SIMULATED because the code generates illustrative values locally and does not query an SNMP agent.



3 Implementation and Technical Working
3.1 Methodology
The implementation is divided into the interface layer and monitoring logic. First, the user enters one or more targets and chooses the interval, number of cycles, packets per cycle, delay threshold, packet-loss threshold and whether to include simulated SNMP-style fields. When monitoring starts, the interface parses and deduplicates target strings, clears the previous run’s history, and executes the selected number of cycles. In each cycle, monitor.py runs ping for each target, parses the output and calculates the status and metrics. It creates alerts when the host does not reply, average delay exceeds the threshold, or packet loss meets or exceeds the selected threshold. The interface stores the returned records in session state, updates progress, displays the latest status and charts, and provides CSV downloads.

3.2 Configuration or Code Implementation
The main interface is app.py. It sets up the Streamlit page, accepts user settings, loops through monitoring cycles, adds simulated metrics when selected, stores observations in session state and renders metric cards, data tables, line charts and CSV downloads. The monitoring module is monitor.py. Its parse_targets function accepts comma-, newline- or semicolon-separated targets and removes duplicates while preserving order. build_ping_command selects command-line flags according to the detected operating system. parse_ping_output attempts to extract packet counts, loss and average round-trip time from common ping summaries and falls back to a coarse result if the expected summary cannot be parsed. ping_target runs the command with a timeout and reports UP when at least one reply is counted. run_monitoring_cycle applies the delay and packet-loss thresholds and creates alert records. simulate_snmp_metrics creates deterministic illustrative CPU, memory and interface-counter values marked SIMULATED.

3.3 Protocols and Technical Working
The application uses the system ping utility to send ICMP Echo Requests to the configured targets. If Echo Replies return, ping reports reply timing and packet counts; the application parses this text to derive round-trip delay and packet loss. The code does not construct raw ICMP packets itself. Subprocess timeouts and a missing ping executable are handled with UNKNOWN results and explanatory details. Other command failures or unusual output can still be difficult to interpret because output formats differ by operating system. The optional SNMP-style fields do not represent SNMP protocol exchanges; they are generated by a simulation function.

3.4 Execution Procedure
On Windows 11, install a supported Python version and open a terminal in the project directory. Create and activate a virtual environment, install dependencies, and launch Streamlit using the following commands:

python -m venv .venv
.venv\Scripts\activate
python -m pip install -r requirements.txt
python -m streamlit run app.py

Open the local URL printed by Streamlit, normally http://localhost:8501. Enter targets such as 127.0.0.1 and 8.8.8.8, choose monitoring settings, and click Start monitoring. The exact Python version was not recorded in the supplied details. Ping results depend on network and firewall conditions.



Figure 3.1. Live Streamlit dashboard supplied by the project team. The table shows two targets marked UP and the displayed 3.5 ms average-delay metric.



4 Testing and Results
4.1 Test Plan
The supplied dashboard screenshot records a successful monitoring run on the team’s Windows 11 environment. The initial results view shows 127.0.0.1 and 8.8.8.8 as UP, with three of three replies for each target, 0% displayed packet loss, and a dashboard average-delay metric of 3.5 ms. A separate alerts screenshot shows HIGH_DELAY entries for 8.8.8.8 at 293 ms and 155 ms while the configured delay threshold was 150 ms. The alerts view also shows HIGH_PACKET_LOSS entries while the packet-loss threshold was set to 0%, despite 0% displayed packet loss. These figures are observations from the supplied screenshots, not a long-term benchmark. A packet-loss threshold test at 30% was not supplied.

Test ID
Test and Input
Expected Result
Observed Result
Status
T1

127.0.0.1; 3 packets

Reachable; replies expected

UP; 3/3 received; 0% loss in supplied results screenshot

Pass*

T2

8.8.8.8; 3 packets

Reachable if ICMP allowed

UP; 3/3 received; 0% loss in supplied results screenshot

Pass*

T3

Delay threshold 150 ms; inspect alert history

HIGH_DELAY when average delay exceeds 150 ms

HIGH_DELAY recorded at 293 ms and 155 ms for 8.8.8.8 in supplied alert screenshot

Demonstrated

 

4.2 Results and Execution Evidence
The original result screenshot shows the latest table for 127.0.0.1 and 8.8.8.8. Both rows show UP, three packets sent, three received and 0% packet loss; the displayed average-delay metric is 3.5 ms. The additional alerts screenshot shows HIGH_DELAY for 8.8.8.8 at 293 ms and 155 ms, exceeding the configured 150 ms threshold. It also shows HIGH_PACKET_LOSS for both targets because the threshold was set to 0%, even though displayed packet loss was 0%. The screenshots support that the dashboard displays monitoring results and threshold-generated alerts. They do not demonstrate actual packet loss, long-term availability or every failure case.



Figure 4.1. Monitoring output for the displayed run: 127.0.0.1 and 8.8.8.8 each received 3/3 replies with 0% packet loss shown.

4.3 Objective Achievement
Objective O1 is demonstrated by the screenshot showing two configured targets and their latest status rows. Objective O2 is demonstrated by the sent/received counts, packet-loss fields and dashboard average-delay metric. Objective O3 is implemented in the code through threshold checks and alert records; a screenshot of a threshold-triggered alert was not provided, so its runtime behaviour should be demonstrated separately before claiming that specific test as passed.

Objective ID
Evidence or Test ID
Outcome
O1

T1, T2; Figures 3.1 and 4.1

Demonstrated in supplied dashboard screenshot

O2

T1, T2; Figure 4.1

Demonstrated for the displayed run

O3

T3; Figure 4.2

Alert generation demonstrated for delay threshold; packet-loss threshold at 30% remains unverified

 



5 Discussion and Conclusion
5.1 Discussion and Comparative Analysis
The supplied dashboard screenshot provides a point-in-time observation: two targets were shown as reachable, with all three configured ping packets received for each target, and the dashboard displayed a 3.5 ms average delay. This illustrates how the application consolidates ping-derived status and metrics in one place instead of requiring the user to compare separate command outputs manually. No controlled comparison against another monitoring tool, repeated benchmark, or long-duration measurement was supplied; therefore no broader performance superiority is claimed.

5.2 Advantages and Applications
The main benefit is a simple consolidated view of reachability observations for multiple hosts, including delay and packet-loss trends, threshold alerts and downloadable records. This can support classroom demonstrations, basic troubleshooting and small-scale diagnostic checks. It can also help learners connect ICMP concepts with observable monitoring output.

5.3 Limitations
ICMP can be blocked or rate-limited, so no reply does not prove that a device or its application services are down. The UP/DOWN label is based on ping replies during a cycle, not a complete service-health check. Ping output differs across operating systems and the parser is best-effort. A small number of packets provides only a rough estimate of delay and loss. Monitoring runs only while the application is active and a run is in progress. The SNMP-style metrics are simulated and do not come from real SNMP agents. The supplied evidence does not demonstrate long-duration reliability or every alert condition.

5.4 Challenges and Troubleshooting
No specific development error or troubleshooting incident was provided by the team. The alert screenshot exposed an important test-configuration caveat: with the packet-loss threshold set to 0%, the HIGH_PACKET_LOSS condition can be met when measured loss is also 0% if the implementation uses a greater-than-or-equal comparison. For meaningful operational testing, restore the threshold to 30% and verify an alert only if observed packet loss reaches that level. That 30% scenario has not yet been evidenced.

Issue
Cause or Diagnosis
Solution and Verification
No specific incident reported

Not provided by team

No unverified troubleshooting claim included

 

5.5 Future Scope
Future work could include integration with authorized real SNMP agents, persistent storage for monitoring history, background scheduling, notifications, stronger platform-specific ping parsers, configurable per-host thresholds, and availability summaries over longer periods. These are proposed enhancements and are not part of the current implementation.

5.6 Conclusion
The project implements a Python and Streamlit dashboard that performs repeated ping checks on user-selected targets, estimates reachability, average round-trip delay and packet loss, applies configurable thresholds to create alerts, visualizes observations and exports data to CSV. The supplied dashboard screenshot shows two targets marked UP with three replies each, 0% displayed packet loss and an average-delay metric of 3.5 ms for that run. The project demonstrates useful introductory network-monitoring concepts while acknowledging that ICMP is not definitive service-health evidence and that the optional SNMP-style fields are simulated.



References
References are limited to the official documentation and protocol specification used to explain the implemented tools and ICMP behaviour.

[1] Python Software Foundation, “subprocess — Subprocess management,” Python 3 documentation. https://docs.python.org/3/library/subprocess.html (accessed 20 October 2026).

[2] Streamlit, “API reference,” Streamlit documentation. https://docs.streamlit.io/develop/api-reference (accessed 20 October 2026).
[3] J. Postel, “Internet Control Message Protocol,” RFC 792, Internet Engineering Task Force, September 1981. https://www.rfc-editor.org/rfc/rfc792 (accessed 20 October 2026).

AI Usage Declaration
AI Usage Declaration: ChatGPT was used for code assistance, debugging guidance and explanations. The team reports that it ran the dashboard and reviewed the monitoring output shown in the supplied screenshot. AI assistance is acknowledged; responsibility for the implementation, report content and accuracy of submitted claims remains with the project team. The observed metrics in this report are transcribed from the supplied dashboard screenshot, not generated by AI.

Declaration: We take responsibility for the accuracy of this report and understand the submitted implementation and results. Sources and assistance used have been acknowledged.

Student Name
PRN
Signature
Pouras Thamke

1272250925

 

Arnav Bhimanwar

1262242976

 

Ameya Palamwar

1262242857

 

Abhishek Walande

1262243036

 

 



Appendices
Appendix A Code and Configuration
Appendix A identifies the main source files submitted with the project. app.py contains the Streamlit user interface, monitoring-cycle orchestration, charts, tables and CSV downloads. monitor.py contains target parsing, platform-specific ping command construction, output parsing, subprocess handling, alert generation and simulated SNMP-style metrics. requirements.txt lists Streamlit and pandas dependencies. The complete source files should accompany this report as the project submission; they are not duplicated in full here to keep the main report readable.

Appendix B Additional Evidence
The dashboard screenshot supplied by the team is included in Sections 3.4 and 4.2 as execution evidence. Additional logs, packet captures and separate failure-case screenshots were not provided.
The alerts screenshot below records HIGH_DELAY entries above the 150 ms threshold and HIGH_PACKET_LOSS entries generated with a 0% configured loss threshold. The latter is a threshold-boundary demonstration, not evidence of actual packet loss.


Appendix C Individual Contributions
Team contributions below reflect the roles supplied by the group. Evidence/deliverable names refer to the project modules and report sections rather than independently audited individual commits.

Student Name and PRN
Work Completed
Evidence or Deliverable
Pouras Thamke / 1272250925

Dashboard integration

app.py; dashboard screenshot

Arnav Bhimanwar / 1262242976

ICMP and availability concepts

Monitoring concepts and report

Ameya Palamwar / 1262242857

Testing and report coordination

Screenshot review and report coordination

Abhishek Walande / 1262243036

Delay/loss and alert logic

monitor.py; alert logic

 

Computer Networks Laboratory   2
