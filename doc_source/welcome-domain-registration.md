# How domain registration works<a name="welcome-domain-registration"></a>

* use case
  * you want to create a 
    * website or
    * web application
* [domain name](route-53-concepts.md#route-53-concepts-domain-name)
  * == name of your website
  * _Example:_ example\.com
  * uses
    * if your enter it | browser -> display your website 
* steps to register a domain name -- via -- Amazon Route 53
  1. choose a domain name & confirm that it's available
     1. available == NO one else has registered the domain name YET
     2. if the domain name is ALREADY in use -> try
        1. other names or
        2. changing ONLY the *top\-level domain* -- _Example:_ .com, .ninja, .hockey
           1. [top-level domains / -- supported with -- Amazon Route 53](registrar-tld-list.md)
  2. if you register the domain name -- via -- Route 53 -> 
     1. you provide
        1. names
        2. contact information -- for the -- domain owner
        3. ...
     2. -- automatically makes itself the -- DNS service / domain -- via --
        1. creates a [hosted zone](route-53-concepts.md#route-53-concepts-hosted-zone) / name == domain name
        2. assigns 4 name servers / hosted zone
           1. name servers -- tell -- the browser where to find your resources  
              1. _Example1:_ web server
              2. _Example1:_ [Amazon S3 bucket](https://docs.aws.amazon.com/s3/)
        3. name servers -- are added to the -- domain
           1. check [How internet traffic is routed to your website or web application](welcome-dns-service.md)
  3. send your information -- to the -- registrar for the domain | end of the registration process
     1. [domain registrar](route-53-concepts.md#route-53-concepts-domain-registrar)
        1. is either
           1. Amazon Registrar, Inc\. or
           2. our registrar associate, Gandi
        2. [Finding your registrar](find-your-registrar.md)
  4. registrar -- sends your information to the -- *registry* for the domain
     1. registry == company / sells domain registrations / >= 1 top\-level domains
  5. registry -- stores the -- information about your domain | 
     1. their own database
     2. public WHOIS database 

* [more detailed information](domain-register.md)
* if you ALREADY registered a domain name -- via -- ANOTHER registrar -> you can transfer -- the domain registration to -- Route 53
  * check [Transferring registration for a domain to Amazon Route 53](domain-transfer-to-route-53.md)