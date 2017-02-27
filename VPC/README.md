### AWS VPC

1. The max number of hosts you can have in any VPC is a /16. 

2. AWS reserves the first four IP addresses and the last 1 IP address for internal networking purposes. 

From http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Subnets.html#VPC_Sizing

```
The first four IP addresses and the last IP address in each subnet CIDR block are not available for you to use, and cannot be assigned to an instance. For example, in a subnet with CIDR block 10.0.0.0/24, the following five IP addresses are reserved:
 
  * 10.0.0.0: Network address.
  * 10.0.0.1: Reserved by AWS for the VPC router.
  * 10.0.0.2: Reserved by AWS. The IP address of the DNS server is always the base of the VPC network range plus two; however, we  also reserve the base of each subnet range plus two. For more information, see Amazon DNS Server.
  * 10.0.0.3: Reserved by AWS for future use.
  * 10.0.0.255: Network broadcast address. We do not support broadcast in a VPC, therefore we reserve this address.
```

3. AWS services will require a few IP addresses. For example, elastic load balancers requires a number of IP addresses to work and the number will change over time to support scaling and failover.

4. Multicast and broadcast are not supported.

5. When you create a subnet, it will be associated with the main route table automatically. If you need different routing for that subnet, create a new routing table, associate it with the subnet and modify the new route table. Never modify the main route table otherwise other subnets may be given routes they should not have upon creation.

6. NONE of your routes will show up in netstat -nr or any of the normal Linux tools.

7. There is a default local route in every VPC routing table that enables intra subnet routing. The route cannot be deleted and you cannot create a more specific route than this. Therefore you only define ways out of the VPC (Internet Gateway, Virtual Private Gateway, NAT, VPC Peering, VPC endpoint) and not routes between your subnets.

8. A subnet can only be associated with one route table at a time, but you can associate multiple subnets with the same route table.

9. AWS uses the most specific route that matches the traffic to determine how to route the traffic (longest prefix match).

10. Static route takes priority over a propagated route.

11. CIDR blocks for IPv4 and IPv6 are treated separately. For example, a route with a destination CIDR of 0.0.0.0/0 (all IPv4 addresses) does not automatically include all IPv6 addresses. You must create a route with a destination CIDR of ::/0 for all IPv6 addresses.

12. Routes to IPv4 and IPv6 addresses or CIDR blocks are independent of each other.

13. AWS currently does not support IPv6 traffic over a VPN connection. However, IPv6 traffic routed through a virtual private gateway to an AWS Direct Connect connection is supported.

14. VPC peering connection can also support IPv6 communication between instances in the VPCs, provided the VPCs and instances are enabled for IPv6 communication.

15. When you add an Internet gateway, an egress-only Internet gateway, a virtual private gateway, a NAT device, a peering connection, or a VPC endpoint in your VPC, you must update the route table for any subnet that uses these gateways or connections. All public subnets share a single route table, but each private subnet has its own. So this has to be done for all private subnet routes.

16. There is a limit on the number of route tables you can create per VPC, and the number of routes you can add per route table. 

From http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Appendix_Limits.html#vpc-limits-route-tables

|Resource|Default limit|Comments|
|--------|-------------|--------|
|Route tables per VPC|200|Including the main route table. You can associate one route table to one or more subnets in a VPC. If you need to increase this limit, submit a request.|
Routes per route table (non-propagated routes)|50|This is the limit for the number of non-propagated entries per route table. You can submit a request for an increase of up to a maximum of 100; however, network performance may be impacted. This limit is enforced separately for IPv4 routes and IPv6 routes (you can have 50 each, and a maximum of 100 each).|

17. You should leave the main route table in its original default state (with only the local route), and explicitly associate each new subnet you create with one of the custom route tables you've created. This ensures that you explicitly control how each subnet routes outbound traffic.

18. Subnets typically won't have an explicit association to the main route table, although it might happen temporarily if you're replacing the main route table.

19. Network ACLs operate on the subnet level and do not filter traffic between instances in the same subnet. They are stateless which means that you will have to implement two rules. This adds extra complexity and is difficult to maintain. Use network ACLs sparingly to enforce baseline security policy by blacklisting.

20. Security groups operate on the instance level. They are stateful and easy to configure and if you keep them simple, they are easy to maintain. They can reference other security groups in the same VPC. They should be your main tool to secure your VPC.

21. Traffic between instances in peered VPCs is private and isolated and there is no single point of failure or bandwidth bottleneck. VPC Peering can connect VPCs in multiple accounts but only within the same region.

22. NAT Gateways are fully managed, redundant, highly available and can handle up to 10Gbps of bursty traffic. The downside is that they do not offer (unlike Internet Gateways) multi-zone capability (A NAT Gateway has to be created per availability zone) and require elastic IPs.

23. Use VPC endpoints to access S3 resources from within your VPC. They allow you to access to Amazon S3 resources (AWS promises to add more services at some point) securely from your instances without having to use Internet Gateway, VPN, or NAT. Both a VPC endpoint and an S3 bucket have to be in the same region though. Use IAM policies to control VPC access to Amazon S3 resources.