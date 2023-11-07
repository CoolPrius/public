A DMZ in the context of explicit internet access


In the ever-evolving landscape of cybersecurity, organizations must continually adapt to protect their digital assets. In this context, a Demilitarized Zone (DMZ) plays a critical role in safeguarding an enterprise virtual network from external threats. Azure Network Security Groups (NSGs) offer a no-cost solution to establish and manage a DMZ within an enterprise virtual network, enhancing security and providing a range of benefits. This article explores the advantages of using an Azure NSG as a DMZ in a virtual network.

This specific example dives into the way an NSG could be utilized as a DMZ within an Azure firewall/NVA environment but this could easily be modified to work a similar way with a NAT gateway or load balancer for external outbound internet traffic mechanisms.

What is a DMZ?

A DMZ is a network segment that acts as an intermediary between the internal network (trusted zone) and the external network (untrusted zone). It serves as a buffer, allowing controlled communication between these two zones while protecting sensitive resources. Traditionally, a physical DMZ would involve multiple layers of firewall systems and complex configurations.

The Benefits of Azure NSGs as a DMZ

-Improved Network Segmentation:
Azure NSGs enable organizations to create a logical DMZ, allowing for better network segmentation. By defining and applying rules to NSGs, you can control traffic flow between different network segments within your environment, ensuring that only authorized connections are established. This level of granularity enhances network security by reducing the attack surface.

-Scalability and Flexibility:
Azure NSGs provide flexibility and scalability, which is essential for modern enterprise networks. As your organization grows, NSGs can be easily adjusted to accommodate new resources and services. Whether you're deploying web applications, databases, or virtual machines, NSGs allow you to adapt the DMZ configuration to meet evolving security needs.

-Rule-Based Access Control:
Azure NSGs operate based on rule sets, allowing administrators to define which traffic is allowed and which is blocked. This rule-based access control is highly customizable, enabling fine-grained control over network traffic. You can specify the source and destination, ports, and protocols.

-Monitoring and Logging:
Azure NSGs offer extensive monitoring and logging capabilities. By leveraging these features, organizations can gain insight into network traffic, detect anomalies, and investigate security incidents. This could be extremely helpful for a DMZ NSG as alert rules could be created based off of certain behavior of traffic in the DMZ and could be put forward as higher priority alert in order to provide advanced warning if a threat actor is attempting to gain access to your network through a less secure resource in the DMZ.


What would a DMZ within an NSG look like from a high level view?

![Alt text](dmz-with-nsgs.png)

For the sake of this article, I peered all the virtual networks together to create a mesh network.

So now we know the architecture, here's what our baseline rule set would look like.

![Alt text](image.png)

The IP 172.16.0.4 is the Private IP of the Azure Firewall, 172.20.0.0/26 is the subnet of the DMZ. We have two rulesets, one for allowing the traffic in general to reach the firewall and another for allowing the "internet" tagged traffic through the firewall. The combination of these two rules are needed in order to allow internet connectivity from a resource in the DMZ subnet to the internet. 

Due to the "DenyAll" rule on both flow of this NSG, traffic can neither come in or leave this subnet (except internet-bound traffic).



![Alt text](image-1.png)



You can see, testvm01 is able to reach the internet with the Firewalls public IP.

![Alt text](image-2.png)



It is not able to ping the jumpbox01 in the prod subnet however.



Now that we have established that this VM is segmented completely out of the local virtual networks in the environmet, lets say we want a jumpbox in the prod virtual network to be able to ping and RDP into the testvm01 within the DMZ.


These are our VMs with their IPs

![Alt text](image-7.png)

I'll add two rules, one to allow RDP connectivity from the prod network and another to allow ICMP connectivity from the same prod network into the DMZ subnet. You may also notice the Bastion rule which is how I was able to connect to the testvm01 in the first place.



![Alt text](image-3.png)


I'm now able to ping testvm01 from jumpbox01 in the prod virtual network.

![Alt text](image-4.png)



Also can RDP into the testvm01 as well.



![Alt text](image-5.png)




If I try to ping back the other direction from the DMZ into our prod virtual network I am still not able to do so.




![Alt text](image-6.png)






Conclusion

We have an environment where an insecure resource can sit in a DMZ subnet and only be able to access the internet through an Azure Firewall, NVA, NAT Gateway or Load Balancer while still allowing resources in other virtual networks to access it.  

By leveraging Azure NSGs as a DMZ, enterprises can enhance their network security, mitigate risks, and ensure the confidentiality, integrity, and availability of their valuable assets. As cyber threats continue to evolve, adopting solutions like Azure NSGs is a proactive step toward safeguarding the digital infrastructure of the enterprise.
