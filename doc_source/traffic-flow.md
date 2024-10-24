# Using traffic flow to route DNS traffic<a name="traffic-flow"></a>

* use cases / manage hosted zone's records -- can be -- challenging
  + lot of resources / perform SAME operation
    + _Example:_ web servers / serve traffic -- for the -- SAME domain
  + create a complex tree of records -- via -- [alias records](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resource-record-sets-choosing-alias-non-alias.html) + combination of [Route 53 routing policies](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy.html) 
* alternative
  * create records 1! time
    * cons
      * hard to keep track of the relationships -- among the -- records | you're reviewing the settings | Route 53 console 
* allows
  * simplifying the process of creating & maintaining records | large & complex configurations
---
**Visual editor**  
* == traffic flow visual editor
* lets
  * creating complex trees of records
  * seeing the relationships <- among the -> records
    * _Example:_ latency alias records -- reference -- weighted records & weighted records -- reference -- your resources | multiple AWS Regions
---
**Versioning**  
* <= 1000 versions / traffic policy can be created
* description / version can be given 
---
**Automatic record creation and updating**  
* TODO: A traffic policy can represent dozens or even hundreds of records\. Traffic flow lets you create all those records automatically by creating a *traffic policy record*\. You specify the hosted zone and the name of the record at the root of the tree, such as example\.com or www\.example\.com, and Route 53 automatically creates all the other records in the tree\. The root record—the traffic policy record—appears in the list of records for your hosted zone; all the other records are hidden\.   
When you create a new version of a traffic policy, you can selectively update traffic policy records that you created using the previous traffic policy version\. When you update a traffic policy record, Route 53 automatically updates all the other records in the tree\. You can also quickly roll back changes by updating a traffic policy record again to use a previous version of a traffic policy\.  
You can use traffic flow to create records only in public hosted zones\.
---
**Reuse for multiple records in different hosted zones**  
You can use a traffic policy to automatically create records in multiple public hosted zones\. For example, if you're using the same web servers for multiple domain names, you can use the same traffic policy to create traffic policy records in the hosted zones for example\.com, example\.org, and example\.net\.

**How Route 53 responds to DNS queries**  
When a client submits a query for the name of the root record, such as example\.com or www\.example\.com, Route 53 responds to the query based on the configuration in the traffic policy that you used to create the corresponding traffic policy record\.

**Geoproximity routing policy**  
The geoproximity routing policy is available only if you use traffic flow\. With geoproximity routing, you can route traffic based on the location of your resources and, optionally, shift traffic from resources in one location to resources in another\. For more information, see [Geoproximity routing \(traffic flow only\)](routing-policy-geoproximity.md)\.

**Charge for traffic flow**  
There's a monthly charge for each traffic policy record\. For more information, see the "Traffic Flow" section of [Amazon Route 53 pricing](https://aws.amazon.com/route53/pricing/)\.  
To minimize these charges, you can create one or more alias records in a hosted zone that reference a traffic policy record in that hosted zone\. For example, you can create a traffic policy record for example\.com and then create an alias record for www\.example\.com that references the traffic policy record\.

**Topics**
+ [Creating and managing traffic policies](traffic-policies.md)
+ [Creating and managing policy records](traffic-policy-records.md)