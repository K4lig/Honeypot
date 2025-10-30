# Honeypot

# Objective: 
Set up a “decoy” (a vulnerable server) on AWS, send its logs to a SIEM, and create an alert when someone attempts to attack it.

*Phases:*
Phase 1: Prepare the Infrastructure (AWS)
Phase 2: Install the SIEM (We will use Wazuh, which is great and open-source)
Phase 3: Configure the Honeypot and Agent
Phase 4: Detect and Visualize the Attack
