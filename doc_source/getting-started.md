# Getting started with Amazon Route 53<a name="getting-started"></a>

* goal
  * register a domain & create a static website | open Amazon S3 bucket / is served with SSL/TLS -- via -- Amazon CloudFront

**Estimated cost**
* annual fee to register a domain
  * [$9, x00 dollars]
    * -- depending on -- top\-level domain
    * [Route 53 Pricing for Domain Registration](https://d32ze2gidvkk54.cloudfront.net/Amazon_Route_53_Domain_Registration_Pricing_20140731.pdf)
* creation of Amazon S3 bucket
  * if you're a new AWS customer -> free
  * if you're an existing AWS customer -> -- [depend on]([Amazon S3 Pricing](https://aws.amazon.com/s3/pricing/)) -- 
    * how much data you store
    * number of requests / your data
    * amount of data transferred 
* CloudFront charges -- [depend on the]([CloudFront Pricing](https://aws.amazon.com/cloudfront/pricing/)) -- 
  * number of requests / your data
  * number of edge locations used
  * amount of data transferred 

**Topics**
+ [Use your domain for a static website](getting-started-s3.md)
+ [Use an Amazon CloudFront distribution to serve a static website](getting-started-cloudfront-overview.md)