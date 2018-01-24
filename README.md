# GKE Cluster routing internet traffic through basic NAT

 - External services like hosted databases often have firewall rules which require a static IP address
 - This example routes all outbound Kubernetes traffic through a single NAT compute instance with a static IP 
 - Traffic heading for the kubernetes master is routed at a higher priority through the default internet gateway
 
## Quickstart

### Prerequisites 

1. Updated gcloud sdk 
2. A Project in Google Cloud in which you want to deploy this cluster 
3. Authenticated to google cloud

### Create

Firstly, in `gke-with-nat-route.jinja` and `gke-with-nat-route.yml` replace any references to `europe-west2` and `europe-west2-a` with the desired zone/region.

```
gcloud deployment-manager deployments create gke-with-nat --config gke-with-nat-route.yml
```

### Delete

```
gcloud compute routes delete master-route -q                 
gcloud compute routes delete gke-cluster-route-through-nat -q
gcloud container clusters delete nat-gke-cluster -q    
gcloud compute instances delete nat-vm -q                         
gcloud compute firewall-rules delete nat-vm-firewall  -q 
gcloud deployment-manager deployments delete gke-with-nat -q        
```    

## How it works in a nutshell

1. Creates a network
2. Creates a subnet for the cluster 
3. Creates a subnet for the nat instance 
4. Creates a NAT compute instance 
5. Creates Firewall rules for NAT instance 
6. Creates the GKE cluster in the cluster's subnet created from step 2 with tag route-through-nat
7. Creates the route from the cluster to the master for instances with tag, route-through-nat
8. Creates the NAT route from the cluster to the NAT for all destinations at a lower priority than the master route above for instances with tag route-through-nat


