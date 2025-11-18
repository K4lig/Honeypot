# Honeypot Cowrie + SIEM Wazuh en GCP

# Objective:
Set up a “decoy” (a vulnerable server) on AWS, send its logs to a SIEM, and create an alert when someone attempts to attack it.

# AWS TOOLS
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

1. Create instances (VMs)
   1.1 Go to GCP Console and create a new project ("Cybersecurity Project" in this case).
   
   1.2 Go to Compute Engine > VM instances.

   1.3 Create VM 1: SIEM Server
         1.3.1 **Name:** siem-server-wazuh
         1.3.2 **Region:** us-central1 (or one near you)
         1.3.3 **Zone:** us-central1-a
         1.3.4 **Machine type:** e2-medium (Wazuh need more like RAM than micro. e2-micro may                                   be failed)

