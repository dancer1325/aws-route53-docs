# Choosing a routing policy<a name="routing-policy"></a>

* routing policy
  * -> 👀how Amazon Route 53 -- responds to -- queries 👀 
  * types
    + **Simple routing policy**
      + uses
        + 1! resource / -- performs a -- given function | your domain
          + _Example:_ web server / serves content -- for the -- example.com website
        + create records | private hosted zone
    + **Failover routing policy**
      + uses
        + configure active-passive failover
        + create records | private hosted zone
    + **Geolocation routing policy**
      + uses
        + route traffic / -- based on the -- location of your users
        + create records | private hosted zone
    + **Geoproximity routing policy**
      + uses
        + route traffic / -- based on the -- location of your resources
        + shift traffic from resources | one location -- to --> resources | another location
    + **Latency routing policy**
      + uses
        + resources | multiple AWS Regions & you want to route traffic to the region / provides the best latency
        + create records | private hosted zone
    + **IP\-based routing policy**
      + uses
        + route traffic / -- based on the -- location of your users & you have the IP addresses / traffic originates from
    + **Multivalue answer routing policy**
      + uses
        + Route 53 -- respond to -- DNS queries / <= 8 healthy records selected
        + multivalue answer / -- routing to -- create records | private hosted zone
    + **Weighted routing policy**
      + uses
        + route traffic -- to -- multiple resources / specified proportions
        + create records | private hosted zone
  * When you create a record -> you choose the routing policy

**Topics**
+ [Simple routing](routing-policy-simple.md)
+ [Failover routing](routing-policy-failover.md)
+ [Geolocation routing](routing-policy-geo.md)
+ [Geoproximity routing \(traffic flow only\)](routing-policy-geoproximity.md)
+ [Latency\-based routing](routing-policy-latency.md)
+ [IP\-based routing](routing-policy-ipbased.md)
+ [Multivalue answer routing](routing-policy-multivalue.md)
+ [Weighted routing](routing-policy-weighted.md)
+ [How Amazon Route 53 uses EDNS0 to estimate the location of a user](routing-policy-edns0.md)