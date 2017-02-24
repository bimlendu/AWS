### AWS VPC

* The max number of hosts you can have in any VPC is a /16. 

* AWS reserves the first four IP addresses and the last 1 IP address for internal networking purposes.

* AWS services will require a few IP addresses. For example, elastic load balancers requires a number of IP addresses to work and the number will change over time to support scaling and failover.

* Multicast and broadcast are not supported.

* When you create a subnet, it will be associated with the main route table automatically. If you need different routing for that subnet, create a new routing table, associate it with the subnet and modify the new route table. Never modify the main route table otherwise other subnets may be given routes they should not have upon creation.

* NONE of your routes will show up in netstat -nr or any of the normal Linux tools.

* There is a default local route in every VPC routing table that enables intra subnet routing. The route cannot be deleted and you cannot create a more specific route than this. Therefore you only define ways out of the VPC (Internet Gateway, Virtual Private Gateway, NAT, VPC Peering, VPC endpoint) and not routes between your subnets.

* A subnet can only be associated with one route table at a time, but you can associate multiple subnets with the same route table.

* AWS uses the most specific route that matches the traffic to determine how to route the traffic (longest prefix match).

* Static route takes priority over a propagated route.

* CIDR blocks for IPv4 and IPv6 are treated separately. For example, a route with a destination CIDR of 0.0.0.0/0 (all IPv4 addresses) does not automatically include all IPv6 addresses. You must create a route with a destination CIDR of ::/0 for all IPv6 addresses.

* Routes to IPv4 and IPv6 addresses or CIDR blocks are independent of each other.

* AWS currently does not support IPv6 traffic over a VPN connection. However, IPv6 traffic routed through a virtual private gateway to an AWS Direct Connect connection is supported.

* VPC peering connection can also support IPv6 communication between instances in the VPCs, provided the VPCs and instances are enabled for IPv6 communication.

* When you add an Internet gateway, an egress-only Internet gateway, a virtual private gateway, a NAT device, a peering connection, or a VPC endpoint in your VPC, you must update the route table for any subnet that uses these gateways or connections. All public subnets share a single route table, but each private subnet has its own. So this has to be done for all private subnet routes.

* There is a limit on the number of route tables you can create per VPC, and the number of routes you can add per route table. http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Appendix_Limits.html#vpc-limits-route-tables

* You should leave the main route table in its original default state (with only the local route), and explicitly associate each new subnet you create with one of the custom route tables you've created. This ensures that you explicitly control how each subnet routes outbound traffic.

* Subnets typically won't have an explicit association to the main route table, although it might happen temporarily if you're replacing the main route table.

* Network ACLs operate on the subnet level and do not filter traffic between instances in the same subnet. They are stateless which means that you will have to implement two rules. This adds extra complexity and is difficult to maintain. Use network ACLs sparingly to enforce baseline security policy by blacklisting.

* Security groups operate on the instance level. They are stateful and easy to configure and if you keep them simple, they are easy to maintain. They can reference other security groups in the same VPC. They should be your main tool to secure your VPC.

* Traffic between instances in peered VPCs is private and isolated and there is no single point of failure or bandwidth bottleneck. VPC Peering can connect VPCs in multiple accounts but only within the same region.

* NAT Gateways are fully managed, redundant, highly available and can handle up to 10Gbps of bursty traffic. The downside is that they do not offer (unlike Internet Gateways) multi-zone capability (A NAT Gateway has to be created per availability zone) and require elastic IPs.

* Use VPC endpoints to access S3 resources from within your VPC. They allow you to access to Amazon S3 resources (AWS promises to add more services at some point) securely from your instances without having to use Internet Gateway, VPN, or NAT. Both a VPC endpoint and an S3 bucket have to be in the same region though. Use IAM policies to control VPC access to Amazon S3 resources.