# How internet traffic is routed to your website or web application<a name="welcome-dns-service"></a>

* ALL computers | internet -- communicate, using -- *IP addresses* /
  * common protocols
    * IPv4
      * _Example:_ 192\.0\.2\.44
    * IPv6
      * _Example:_ 2001:0db8:85a3:0000:0000:abcd:0001:2345
  * vs domain name
    * more difficult/ impossible to remember
* DNS service
  * allows
    * domain names <- can be translated to -> IP addresses
  * _Example:_ Amazon Route 53 \.

**Topics**
+ [Overview of how you configure Amazon Route 53 to route internet traffic for your domain](#welcome-dns-service-how-to-configure)
+ [How Amazon Route 53 routes traffic for your domain](#welcome-dns-service-how-route-53-routes-traffic)

## Overview of how you configure Amazon Route 53 to route internet traffic for your domain<a name="welcome-dns-service-how-to-configure"></a>

* goal
  * via Amazon Route 53 console
    * register a domain name
    * configure Route 53 / route internet traffic -- to your -- website or web application 

* steps
  * [register the domain name](welcome-domain-registration.md)
    * -> Route 53 -- automatically creates a -- public hosted zone / name == domain name
      * check [Working with public hosted zones](AboutHZWorkingWith.md)
  * create *records* == *resource record sets* | your hosted zone 
    * allows
      * -- routing traffic to -- your resources
        * check [Configuring Amazon Route 53 as your DNS service](dns-configuring.md)
    * information / record
      * **Name**  
        * == domain name \(example\.com\) OR subdomain name \(www\.example\.com, retail\.example\.com\)
        * requirements
          * recordName end == hosted zone name
            * _Example:_ if hosted zone name is "example.com" -> ALL record names | this hosted zone must end in example.com
            * 👀Route 53 console does this for you automatically 👀
      * **Type**
        * -- determines the -- type of resource / you want traffic to be routed to
          * _Example1:_ if you want to route traffic to an email server -> specify `MX`
          * _Example2:_ if you want to route traffic to a web server / has an IPv4 IP address -> specify `A`
      * **Value**  
        * -- related to -- Type
          * if you specify `MX` -> specify the names of >=1 email servers 
          * if you specify `A` -> specify an IP address / IPv4 format -- _Example:_ 192.0.2.136 --
    * check [Working with records](rrsets-working-with.md)
    * special Route 53 records
      * called 👀 alias records 👀
      * := records / -- route traffic to -- 
        * Amazon S3 buckets
        * Amazon CloudFront distributions
        * other AWS resources
      * check 
        * [Choosing between alias and non\-alias records](resource-record-sets-choosing-alias-non-alias.md) 
        * [Routing internet traffic to your AWS resources](routing-to-aws-resources.md)

## How Amazon Route 53 routes traffic for your domain<a name="welcome-dns-service-how-route-53-routes-traffic"></a>

* use case
  * someone requests content for -- domain name (_Example:_ www.example.com)
    * next diagram takes few milliseconds

![\[Conceptual graphic that shows how the Domain Name System and Route 53 route internet traffic to the resources for www.example.com.\]](http://docs.aws.amazon.com/Route53/latest/DeveloperGuide/images/how-route-53-routes-traffic.png)

1. user opens a web browser, enters www\.example\.com in the address bar, and presses Enter
1. request for www\.example\.com -- is routed to a -- DNS resolver / typically managed by the user's internet service provider (ISP)
   1. _Examples of user's ISP:_ cable internet provider, a DSL broadband provider, or a corporate network
1. DNS resolver -- forwards the request for www\.example\.com to a -- DNS root name server
   1. DNS root name server -- respond with the related -- TLD name servers / -- associated with the -- domain name 
1. DNS resolver -- forwards the request for www\.example\.com again, to -- 👀one of the TLD name servers 👀(_Example:_ for .com domains in this case)
   1. TLD name server -- responds with the -- names of the 4 Route 53 name servers /
      1. -- associated with -- the domain name
      2. DNS resolver caches the 4 Route 53 name servers
         1. -> if next time, someone -- browses to -- example\.com -> DNS resolver skips steps 3 and 4 
         2. typically cached for 2 days
1. DNS resolver chooses a Route 53 name server &  forwards the request for www\.example\.com to that name server
1. Route 53 name server
   1. looks for the www\.example\.com record | example\.com hosted zone
   2. gets the associated value -- _Example:_ IP address for a web server, 192\.0\.2\.44 --
   3. returns the IP address -- to the -- DNS resolver
      1. 👀DNS resolver caches the IP address / domain name 👀
        1. check [time to live (TTL)](route-53-concepts.md#route-53-concepts-time-to-live) 
1. DNS resolver returns -- record value (typically the Ipv4) to the -- web browser
1. web browser -- sends a request for the domain name (www\.example\.com) to the -- IP address / -- got from the -- DNS resolver
   1. here it's where content is
      1. _Example:_ web server / running |
         1. Amazon EC2 instance or
         2. Amazon S3 bucket / -- configured as a -- website endpoint
1. web server or other resource | 192\.0\.2\.44 -- returns the web page for www\.example\.com to the -- web browser
   1. -> web browser -- displays the -- page