# NS and SOA records that Amazon Route 53 creates for a public hosted zone<a name="SOA-NSrecords"></a>

* Amazon Route 53 automatically creates / EACH public hosted zone
  * name server \(NS\) record
  * start of authority \(SOA\) record 

**Topics**
+ [The name server \(NS\) record](#NSrecords)
+ [The start of authority \(SOA\) record](#SOArecords)

## The name server \(NS\) record<a name="NSrecords"></a>

* 's name == hosted zone name
  * _Example of the format name:_
    * *ns\-2048\.awsdns\-64\.com*
    * *ns\-2049\.awsdns\-65\.net*
    * *ns\-2050\.awsdns\-66\.org*
    * *ns\-2051\.awsdns\-67\.co\.uk*
* lists the 4 name servers / 👀are the authoritative name servers | your hosted zone 👀
  * NOT recommended | this record, to
    * add NS
    * change NS
    * delete NS
  * way to get
    1. Sign in to the AWS Management Console & open the [Route 53 console](https://console.aws.amazon.com/route53/)
    1. click **Hosted zones** | navigation panel
    1. choose one **Hosted zones** & view details

## The start of authority \(SOA\) record<a name="SOArecords"></a>

* -- identifies the -- 👀base DNS information about the domain 👀
  * _Example:_

    ```
    1. ns-2048.awsdns-64.net. hostmaster.example.com. 1 7200 900 1209600 86400
    ```
  * Route 53 name server / created the SOA record
    * _Example:_ `ns-2048.awsdns-64.net`
  + email address of the administrator / `@` symbol -- is replaced by a -- period
    + _Example:_ `hostmaster.example.com`
    + `amazon.com` default email address / NOT monitored
  + serial number / if you update a record | hosted zone -> you can optionally (NOT done automatically by Route 53) increment
    + uses
      + by DNS services / support secondary DNS
    + _Example:_ value is `1`
  + refresh time | seconds / secondary DNS servers wait / before querying the primary DNS server's SOA record -- to check for -- changes
    + _Example:_ value is `7200`
  + retry interval | seconds / secondary server waits / before retrying a failed zone transfer
    + recommendations
      + retry time < refresh time
    + _Example:_ value is `900` \( == 15 minutes\) 
  + time | seconds / secondary server will keep trying to complete a zone transfer
    + if this time elapses / before a successful zone transfer -> secondary server -- will stop -- answering queries
      + Reason: 🧠 considers its data is too old / be reliable 🧠
    + _Example:_ value is `1209600` \(== 2 weeks\)
  + minimum time to live \(TTL\)
    + := length of time / recursive resolvers -- should cache the -- 👀 following responses from Route 53: 👀
      + **NXDOMAIN**
        + := responses from Route 53 / 
          + NO record of ANY type / name == specified -- via the -- DNS query (_Example:_ example\.com)
          + NO records / are children of the name == specified -- via the -- DNS query (_Example:_ zenith\.example\.com)
      + **NODATA**
        + := responses from Route 53 /
          + \>=1  record / 
            + name == specified -- via the -- DNS query
            + BUT type != specified -- via the -- DNS query \(_Example:_ A\)
    + if DNS resolver caches an NXDOMAIN OR NODATA response == *negative caching*
      + 👀duration of negative caching < the following values👀 
        + value—the minimum TTL | SOA record 
          + _Example:_ `86400` \(== 1 day\)
        + value of the TTL / SOA record
          + default value is 900 seconds
          + [How to change it](resource-record-sets-editing.md)
      + -> you're charged the rate / standard queries
        + [Amazon Route 53 Pricing](http://aws.amazon.com/route53/pricing/)
      + ways to NOT get negative caching?
        + change the
          + TTL / SOA record
          + minimum TTL | SOA record
          + both
        + 👀possible consequences of increasing these TTLs 👀
          + reduces the # of queries / -- are forwarded to -- Route 53 -> reduces the Route 53 charge / DNS queries 
            + Reason: 🧠DNS resolvers | internet / cache the non\-existence of records | longer periods 🧠
          + if you erroneously delete a valid record & later recreate it -> DNS resolvers -- will cache the -- negative response | longer period -> lengthens the amount of time / your customers or users can NOT reach the corresponding resource (_Example:_ web server for acme\.example\.com) 

* way to get
  1. Sign in to the AWS Management Console & open the [Route 53 console](https://console.aws.amazon.com/route53/)
  1. click **Hosted zones** | navigation panel
  1. Select the linked name of the domain & check | **Records** section