# Configuring an Internal Load Balancer
## Login with the qwiklab credentials
    gcloud auth login
    gcloud config set project qwiklabs-gcp-00-022aa5706761
## Task 1. Configure internal traffic and health check firewall rules.
Create the firewall rule to allow traffic from any sources in the 10.10.0.0/16 range :
    gcloud compute --project=qwiklabs-gcp-00-022aa5706761 firewall-rules create fw-allow-lb-access --direction=INGRESS --priority=1000 --network=my-internal-app --action=ALLOW --rules=all --source-ranges=10.10.0.0/16 --target-tags=backend-service

Create the health check rule
    gcloud compute --project=qwiklabs-gcp-00-022aa5706761 firewall-rules create fw-allow-health-checks --description="Firewall rules that allow health checks" --direction=INGRESS --priority=1000 --network=my-internal-app --action=ALLOW --rules=tcp:80 --source-ranges=130.211.0.0/22,35.191.0.0/16 --target-tags=backend-service

## Task 2: Create a NAT configuration using Cloud Router
Create the Cloud Router instance
    
## Task 3. Configure instance templates and create instance groups
Configure the instance templates
    gcloud beta compute --project=qwiklabs-gcp-00-022aa5706761 instance-templates create instance-template-1 --machine-type=e2-medium --subnet=projects/qwiklabs-gcp-00-022aa5706761/regions/us-central1/subnetworks/subnet-a --no-address --metadata=startup-script-url=gs://cloud-training/gcpnet/ilb/startup.sh --maintenance-policy=MIGRATE --service-account=778999252080-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --region=us-central1 --tags=backend-service --image=debian-10-buster-v20200902 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=instance-template-1 --no-shielded-secure-boot --no-shielded-vtpm --no-shielded-integrity-monitoring --reservation-affinity=any

Create another instance template for subnet-b by copying instance-template-1:
    gcloud beta compute --project=qwiklabs-gcp-00-022aa5706761 instance-templates create instance-template-2 --machine-type=e2-medium --subnet=projects/qwiklabs-gcp-00-022aa5706761/regions/us-central1/subnetworks/subnet-b --no-address --metadata=startup-script-url=gs://cloud-training/gcpnet/ilb/startup.sh --maintenance-policy=MIGRATE --service-account=778999252080-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --region=us-central1 --tags=backend-service --image=debian-10-buster-v20200902 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=instance-template-2 --no-shielded-secure-boot --no-shielded-vtpm --no-shielded-integrity-monitoring --reservation-affinity=any

Create the managed instance groups

Create instance-group-1 in us-central1-a
    gcloud compute --project=qwiklabs-gcp-00-022aa5706761 instance-groups managed create instance-group-1 --base-instance-name=instance-group-1 --template=instance-template-1 --size=1 --zone=us-central1-a
    gcloud beta compute --project "qwiklabs-gcp-00-022aa5706761" instance-groups managed set-autoscaling "instance-group-1" --zone "us-central1-a" --cool-down-period "45" --max-num-replicas "5" --min-num-replicas "1" --target-cpu-utilization "0.8" --mode "on"

Create instance-group-2 in us-central1-b
    gcloud compute --project=qwiklabs-gcp-00-022aa5706761 instance-groups managed create instance-group-2 --base-instance-name=instance-group-2 --template=instance-template-2 --size=1 --zone=us-central1-b
    gcloud beta compute --project "qwiklabs-gcp-00-022aa5706761" instance-groups managed set-autoscaling "instance-group-2" --zone "us-central1-b" --cool-down-period "45" --max-num-replicas "5" --min-num-replicas "1" --target-cpu-utilization "0.8" --mode "on"

Create a utility VM to access the backends' HTTP sites
    gcloud beta compute --project=qwiklabs-gcp-00-022aa5706761 instances create utility-vm --zone=us-central1-f --machine-type=f1-micro --subnet=subnet-a --private-network-ip=10.10.20.50 --no-address --maintenance-policy=MIGRATE --service-account=778999252080-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --image=debian-10-buster-v20200902 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=utility-vm --no-shielded-secure-boot --shielded-vtpm --shielded-integrity-monitoring --reservation-affinity=any

SSH to the create instance "utility-vm" :
    gcloud beta compute ssh --zone "us-central1-f" "utility-vm" --tunnel-through-iap

## Task 4. Configure the internal load balancer

## Task 5. Test the internal load balancer
    ssh to utility-vm :
    gcloud beta compute ssh --zone "us-central1-f" "utility-vm" --tunnel-through-iap

