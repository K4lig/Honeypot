# Honeypot Cowrie + SIEM Wazuh en GCP

# Objective:
Set up a “decoy” (a vulnerable server) on GCP, send its logs to a SIEM, and create an alert when someone attempts to attack it.

# GOOGLE CLOUD PLATFORM TOOLS
•	**EC2**: To launch a VM.

•	**VPC (Virtual Private Cloud)**: To create your network. 

• **Security Groups**: To deliberately open “insecure” ports (for example, SSH/RDP to the world 0.0.0.0/0).

# SIEM TOOLS
• **WAZUH**: Excellent open-source option. This "agent" of Wazuh was install on the honeypot and the "manager" (what is SIEM) recieved the logs

• **Splunk Free**: The industry standard. It has a daily intake limit (500MB) that is perfect for a laboratory. 

• **Elastic Stack (ELK)**: Very powerful and popular

__***Phases:***__

•	**Phase 1**: Prepare the Infrastructure (AWS)

•	**Phase 2**: Install the SIEM (We will use Wazuh, which is great and open-source)

•	**Phase 3**: Configure the Honeypot and Agent 

•	**Phase 4**: Detect and Visualize the Attack

# PHASE 1 : Prepare the Infrastructure on GCP
We will use the GCP free tier, which includes one -e2-micro- instance per month. For our lab of two VMs, the cost will be like a few cents per hour (if you turn it off when don't use) or some bucks if you keep it on all month.

__*1. Create instances (VMs)*__
   1.1 Go to GCP Console and create a new project ("Cybersecurity Project" in this case).
   
   1.2 Go to Compute Engine > VM instances.

   1.3 Create VM 1: SIEM Server
   
         1.3.1 Name: siem-server-wazuh.
   
         1.3.2 Region: us-central1 (or one near you).
   
         1.3.3 Zone: us-central1-a.
   
         1.3.4 Machine type: e2-medium (Wazuh need more like RAM than micro. e2-micro maybe failed).

         1.3.5 Boot disk: Switch to Ubuntu 22.04 LTS (Leave 10 GB on disk).

         1.3.6 Firewall: Do not mark “Allow HTTP traffic” or “HTTPS”. We will do it manually.

   1.4 Create VM 2: The Honeypot

         1.4.1 Name: honeypot-server-wazuh.

         1.4.2 Region: us-central1 (the same like siem)

         1.4.3 Zone: us-central1-a

         1.4.4 Machine type: e2-micro (the honeypot is very lightweight, so we save money here)

         1.4.5 Network tags: Important! Add a tag call @honeypot@. This will allow us to applied specific firewall rules.

         1.4.6 Firewall: Do not mark anything.

__*2. Configure SSH connection (Keys)*__

GCP manages SSH keys slightly differently than AWS. The easiest way is to use the gcloud tool or connect from browser.
   → To connect to yours VMs, simply go to the list of the instances of VM and click on the button "SSH" next to each one. This opened a terminal on your browser. It's so easy!

__*3. Configure Firewall* (Most important part!)__

Here we define who can talk with whom. Go to **VPC Network** > **Firewall**. We going to create 4 rules:

   3.1 Rule 1: 'allow-admin-ssh' (So that YOU can manage the VMs)

      → Objectives: All instances of network

      → Origin Filters: IP ranges

      → Oring Ip ranges: Go to Google and search "What is my IP", copy your Public IP and paste it here followed by /32.

      → Ports and Protocols: Ports and Specified TCPs > tcp:22 (This is the real SHH port).

   3.2 Rule 2: allow-wazuh-ui (So that see the SIEM website)

      → Objectives: All instances of network

      → Origin Filters: IP ranges > YOUR_IP/32 (just like before).

      → Ports and Protocols: Ports and Specified TCPs > tcp:443 (HTTPS).

   3.3 Rule 3: allow-honeypot-public (The door for Hackers and Script kiddies!)

      → Objectives: Specified destiny tags > honeypot (tag we create).

      → Origin Filters: IP ranges > 0.0.0.0/0 (This means "Any IP in the world")

      → Ports and Protocols: Ports and Specified TCPs > tcp:2222 (We used 2222 for the honeypot, not 22, this is a tactic).

   3.4 Rule 4: allow-agent-to-siem (For the Honeypot to talk with the SIEM)

      → Objective: All instances of network

      → Origin Filters: IP ranges > 10.128.0.0/9 (This is the range to intern IPs for default of GCP. So only your VMs can talk with each other)

      → Ports and Protocols: Ports and Specified TCPs > tcp:1514, tcp:1515.

Congratulations! Your network is ready and segmented :shipit:.
