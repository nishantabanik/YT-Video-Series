### Introduction

Welcome to our GitHub page on "Launching Scalable Web Services on GCP using MIGs (Managed Instance Groups)." This guide is designed to help you deploy scalable and resilient web services on Google Cloud Platform (GCP) using Managed Instance Groups (MIGs). By leveraging MIGs, you can ensure that your web service can automatically scale up or down based on demand, improve fault tolerance, and achieve seamless updates with minimal downtime. Our goal is to provide a comprehensive tutorial that encompasses setting up, deploying, and managing your web services on GCP.


Here's the URL Link of my YouTube GCP-MIG tutorial as a reference: [Watch the GCP-MIG Tutorial Video on YouTube](https://www.youtube.com/watch?v=GCImELp8bAU&t=76s)


### Objectives

- **Scalability:** Automatically adjust the number of instances in response to traffic demands.
- **High Availability:** Improve reliability and fault tolerance by distributing instances across multiple zones.
- **Update Management:** Roll out updates without downtime using rolling updates and canary updates.

### Best Practices

1. **Pre-configure health checks:** Ensure your instance groups are healthy and can handle user requests.
2. **Utilize startup scripts:** Automate the setup process for each instance in your managed group.
3. **Monitor and optimize costs:** Keep an eye on your scaling policies and adjust to optimize operational costs.
4. **Security:** Implement proper security rules and practices to protect your services and data.

### Commands and Explanations

| Command | Explanation |
|---------|-------------|
| `gcloud auth login` | Authenticates your Google Cloud CLI session, enabling interaction with your GCP account. |
| `gcloud auth application-default login` | Sets up default credentials for application use, simplifying development and testing on GCP. |
| `gcloud config set project mig-project` | Configures the current working project to `mig-project`, focusing all subsequent commands on this project. |
| `gcloud config set compute/zone europe-central2-b` | Sets the compute zone to `europe-central2-b` for the current project, which is important for resource location and latency. |
| `gcloud compute instances create myinstance` | Creates a new compute instance named `myinstance`, serving as a base for our web service. |
| `gcloud compute firewall-rules create allow-80 --allow tcp:80` | Sets up a firewall rule named `allow-80` to permit HTTP traffic on port 80, essential for web service accessibility. |
| `gcloud compute ssh myinstance` | Initiates an SSH connection to `myinstance`, allowing for direct command line access. |

### Initial Instance Setup

```bash
# Log into myinstance:
sudo su -
apt-get update  # Updates package lists for upgrades and new package installations.
apt-get install -y nginx  # Installs Nginx, a high-performance web server.
service nginx start  # Starts the Nginx service, making the web server active.
apt-get install -y wget  # Installs wget, a utility for downloading files from the web.
wget -q -O - localhost:80 | grep nginx  # Tests Nginx installation by fetching the default page and looking for "nginx".

We can even try browsing the external ip of "myinstance" to see the live Nginx webpage
```

### Finalizing the Setup

| Command | Explanation |
|---------|-------------|
| `gcloud compute instances create nginx --metadata-from-file startup-script=startup.sh` | Creates a new instance with Nginx installed and configured through the startup script. |
| `gcloud compute instance-templates create nginx-template --metadata-from-file startup-script=startup.sh` | Creates an instance template for deploying scalable groups of instances. |
| `gcloud compute target-pools create nginx-pool` | Sets up a target pool named `nginx-pool` for managing a collection of instances. |
| `gcloud compute instance-groups managed create nginx-group --base-instance-name nginx --template nginx-template --size 2 --target-pool nginx-pool` | Creates a managed instance group with automatic management based on the specified instance template. |
| `gcloud compute instance-groups managed resize nginx-group --zone=europe-central2-b --size 1` | Manually adjusts the size of the managed instance group, useful for immediate scaling needs. |
| `gcloud compute instance-groups managed set-autoscaling nginx-group --zone=europe-central2-b --min-num-replicas=3 --max-num-replicas=7` | Configures autoscaling for the instance group, ensuring dynamic resizing based on load. |
| `gcloud compute instances list` | Lists all compute instances, providing an overview of current resources. |
| `gcloud compute forwarding-rules create nginx-lb --ports 80 --target-pool nginx-pool` | Sets up a load balancer to distribute incoming traffic across instances in the pool. We can access the Loadbalancer IP via a Browser and see how it automatically dirtributes the Nginx load across different Nodes (keep your eyes on the suffix part with Node ID)|

### Cleanup Commands

| Command | Explanation |
|---------|-------------|
| `gcloud compute forwarding-rules delete nginx-lb --region europe-central2 --quiet` | Deletes the load balancing rule, removing external access to the pooled instances. |
| `gcloud compute instance-groups managed delete nginx-group --quiet` | Deletes the managed instance group, freeing up resources. |
| `gcloud compute target-pools delete nginx-pool --region europe-central2 --quiet` | Removes the target pool, eliminating the instance collection. |
| `gcloud compute instance-templates delete nginx-template --quiet` | Deletes the instance template to prevent further use. |
| `gcloud compute instances delete nginx --quiet` | Deletes the initial Nginx instance created for setup. |
| `gcloud compute instances delete myinstance --quiet` | Removes the base instance `myinstance`, cleaning up initial resources. |
| `gcloud compute firewall-rules delete allow-80 --quiet` | Removes the firewall rule that allowed HTTP traffic, securing the network. |

This guide provides a roadmap for deploying scalable and resilient web services using Managed Instance Groups on GCP, incorporating best practices for setup, management, and scalability. Whether you're deploying a new application or scaling an existing one, following these steps and commands will help ensure a successful and efficient deployment on Google Cloud Platform.


### Engage with Us

Your curiosity and eagerness to learn drive us to create more content that breaks down technological barriers. If you've found this guide useful, we'd love to hear your thoughts and questions. Engaging with our content helps us understand your needs better and tailor our future guides to suit your interests.




### Closing

Thank you for exploring "Launching Scalable Web Services on GCP using MIGs". We're passionate about making complex technologies accessible and hope this guide has equipped you with the knowledge to confidently use KinD for your Kubernetes endeavors. Stay tuned for more guides and tutorials designed to help you navigate the evolving tech landscape.
