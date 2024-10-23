# What is Amazon Route 53?<a name="Welcome"></a>

* Amazon Route 53
  * := Domain Name System \(DNS\) web service
    * /
      * highly available
      * highly scalable
    * allows
      * ⭐️perform 3 main functions ⭐️
        * domain registration
          * _Example:_ for your website or web application
        * DNS routing
        * health checking 
        * / 👀in ANY combination allowed 👀
          * 👀if you use Route 53 for all 3 -> follow the next order 👀
            * **Register domain names**
              * [How domain registration works](welcome-domain-registration.md)
              * [Registering a new domain](domain-register.md)
              * [Getting started with Amazon Route 53](getting-started.md)
            * **Route internet traffic -- to the -- resources for your domain**
              * if you enter a domain name | web browser -> Route 53 helps connect the browser -- with -- the website or web application
              * [How internet traffic is routed to your website or web application](welcome-dns-service.md)
              * [Configuring Amazon Route 53 as your DNS service](dns-configuring.md)
              * [Routing traffic email to Amazon WorkMail](routing-to-workmail.md)
            * **Check the health of your resources**
              * Route 53 -- sends automated requests over the internet to a -- resource / verify that it's
                * reachable
                * available
                * functional
              * if a resource becomes unavailable & you configure it -> you can
                * receive notifications
                * route internet traffic -- away from -- unhealthy resources
              * [How Amazon Route 53 checks the health of your resources](welcome-health-checks.md)
              * [Creating Amazon Route 53 health checks and configuring DNS failover](dns-failover.md) 

**Topics**
+ [How domain registration works](welcome-domain-registration.md)
+ [How internet traffic is routed to your website or web application](welcome-dns-service.md)
+ [How Amazon Route 53 checks the health of your resources](welcome-health-checks.md)
+ [Amazon Route 53 concepts](route-53-concepts.md)
+ [How to get started with Amazon Route 53](welcome-how-to-get-started.md)
+ [Related services](welcome-related-services.md)
+ [Accessing Amazon Route 53](welcome-accessing-route-53.md)
+ [AWS Identity and Access Management](IAMRoute53.md)
+ [Amazon Route 53 pricing and billing](Route53Pricing.md)