### AWS VPC

* AWS reserves the first four IP addresses and the last 1 IP address for internal networking purposes.

* AWS services will require a few IP addresses. For example, elastic load balancers requires a number of IP addresses to work and the number will change over time to support scaling and failover.

* Multicast and broadcast are not supported.

* When you create a subnet, it will be associated with the main route table automatically. If you need different routing for that subnet, create a new routing table, associate it with the subnet and modify the new route table. Never modify the main route table otherwise other subnets may be given routes they should not have upon creation.

* There is a default local route in every VPC routing table that enables intra subnet routing. The route cannot be deleted and you cannot create a more specific route than this. Therefore you only define ways out of the VPC (Internet Gateway, Virtual Private Gateway, NAT, VPC Peering, VPC endpoint) and not routes between your subnets.

* Network ACLs operate on the subnet level and do not filter traffic between instances in the same subnet. They are stateless which means that you will have to implement two rules. This adds extra complexity and is difficult to maintain. Use network ACLs sparingly to enforce baseline security policy by blacklisting.

* Security groups operate on the instance level. They are stateful and easy to configure and if you keep them simple, they are easy to maintain. They can reference other security groups in the same VPC. They should be your main tool to secure your VPC.

* Traffic between instances in peered VPCs is private and isolated and there is no single point of failure or bandwidth bottleneck. VPC Peering can connect VPCs in multiple accounts but only within the same region.

* NAT Gateways are fully managed, redundant, highly available and can handle up to 10Gbps of bursty traffic. The downside is that they do not offer (unlike Internet Gateways) multi-zone capability (A NAT Gateway has to be created per availability zone) and require elastic IPs.

* Use VPC endpoints to access S3 resources from within your VPC. They allow you to access to Amazon S3 resources (AWS promises to add more services at some point) securely from your instances without having to use Internet Gateway, VPN, or NAT. Both a VPC endpoint and an S3 bucket have to be in the same region though. Use IAM policies to control VPC access to Amazon S3 resources.