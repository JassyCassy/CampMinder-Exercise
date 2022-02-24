# CampMinder-Exercise

### Following the requirements in the given exercise of architecting the n-tier application, I made a decision to use an approach shown on the diagram.


To make my application more available and fault-tolerant, I chose to create a separated and isolated Virtual Network with 2 subnets(1 public and 1 private) that spans across 3 AZ’s, route tables, NAT Gateway, and Virtual network gateway.
To secure my Virtual Network and Subnets, I have defined relevant ingress and egress rules in Network ACL’s on the VNet level and blocked all traffic to the data tier subnet which resides in a private subnet.


I have deployed an AKS cluster, spanning across 3 AZ’s for high availability, with managed nodes group, custom security group, and AKS autoscaler to support any excessive load and save cost when a number of requests or resources utilization is low. 
Also, inside of the AKS cluster, I have deployed an HPA, to support horizontal pod scaling based on custom metrics scraped by Prometheus. 
In order to evenly distribute traffic and make an application even more secure inside of my AKS cluster, I have deployed an Nginx ingress controller with a Gateway load balancer with SSL termination.


I have deployed Azure MySQL database(cheaper DB solution) in the private subnet(no public access), with the main DB instance in one AZ and standby instance in separate AZ for more availability and disaster recovery in the case if one AZ goes down. Also, I have deployed 2 Read Replicas in separate AZ to support read-only connections and reduce the load on the main DB instance. 
To connect my AKS cluster and DB, I used the VNet endpoint.


To expose static JS front end, I have deployed that to Azure blob with public access and defined this blob as an origin for CDN(edge location), to reduce latency for users accessing this web page as well as more it more secure, since CDN supports SSL certificates and provide caching. 


For DNS I used Azure DNS with public hosted zone and registered domain name, where I created DNS records for relevant resources. 


To minimize operational overhead, the entire infrastructure was deployed using terraform, in order to manage everything as IaaC, giving an option to be cloud-agnostic and fail tolerant, in case someone will make unnecessary changes within this architecture. 

As a monitoring solution, I chose to deploy Prometheus and Grafana with an Alert manager in AKS, which give me the flexibility to scrap custom metrics from all Cluster objects and send them to Grafana dashboards with an option of creating custom alerts, which will notify me and the team about any issues.
For the logging solution, I chose Splunk, which supports logs injection from any part of the infrastructure and gives visibility on internal processes, and helps with debugging my app and infrastructure.
