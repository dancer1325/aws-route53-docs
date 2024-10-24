# Finding your registrar and other information about your domain<a name="find-your-registrar"></a>

* [GetDomainDetail](https://docs.aws.amazon.com/Route53/latest/APIReference/API_domains_GetDomainDetail.html) API
  * allows
    * viewing domain information
      * registrar
      * registration date
      * privacy setting
      * ...
  * ways to invoke it
    * AWS SDKs
    * [AWS CLI]([get\-domain\-detail](https://docs.aws.amazon.com/cli/latest/reference/route53domains/get-domain-detail.html))

      ```
      aws route53domains get-domain-detail \
          --domain-name example.com
      ```
  
  * requirements
    * ⚠️ONLY | us-east-1 AWS Region ⚠️
