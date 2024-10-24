# Migrating a hosted zone to a different AWS account<a name="hosted-zones-migrating"></a>

* goal
  * migrate a hosted zone from one AWS account -- to a -> different account
* steps
  * list the records | old hosted zone
  * edit the output
  * create records | new hosted zone
    * ways to create
      * manually | [Route 53 console](resource-record-sets-creating.md)
        * use cases
          * few records
      * AWS CLI
        * use cases
          * few records
        * 👀done | this .md 👀
      * AWS SDKs
      * Amazon Route 53 API
      * AWS Tools for Windows PowerShell
    * ⚠️alias records / route traffic to traffic policy instances, NOT possible to migrate ⚠️

**Topics**
+ [Step 1: Install or upgrade the AWS CLI](#hosted-zones-migrating-install-cli)
+ [Step 2: Create the new hosted zone](#hosted-zones-migrating-create-hosted-zone)
+ [Step 3: Create a file that contains the records that you want to migrate](#hosted-zones-migrating-create-file)
+ [Step 4: Edit the records that you want to migrate](#hosted-zones-migrating-edit-records)
+ [Step 5: Split large files into smaller files](#hosted-zones-migrating-split-file)
+ [Step 6: Create records in the new hosted zone](#hosted-zones-migrating-create-records)
+ [Step 7: Compare records in the old and new hosted zones](#hosted-zones-migrating-compare)
+ [Step 8: Update the domain registration to use name servers for the new hosted zone](#hosted-zones-migrating-update-domain)
+ [Step 9: Wait for DNS resolvers to start using the new hosted zone](#hosted-zones-migrating-wait-for-ttl)
+ [Step 10: \(Optional\) delete the old hosted zone](#hosted-zones-migrating-delete-old-hosted-zone)

## Step 1: Install or upgrade the AWS CLI<a name="hosted-zones-migrating-install-cli"></a>

* [AWS Command Line Interface User Guide](https://docs.aws.amazon.com/cli/latest/userguide/)
  * recommendations
    * upgrade to the latest version -> support latest Route 53 features 
* [Configure AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html)

## Step 2: Create the new hosted zone -- via -- Route 53 console<a name="hosted-zones-migrating-create-hosted-zone"></a>

* once you create the NEW hosted zone -> Route 53 -- assigns a -- NEW set of 4 name servers / NEW hosted zone

* steps
  1. Sign in to the AWS Management Console & open the [Route 53 console](https://console.aws.amazon.com/route53/)
  2. Sign in with the account credentials for the account / you want to migrate
  3. [Create a public hosted zone](CreatingHostedZone.md) & make note of the hosted zone ID

## Step 3: Create a file / contains the records / you want to migrate<a name="hosted-zones-migrating-create-file"></a>

* missing steps
  * create a file / contains the records to migrate
  * edit the file
  * the edited file -- to create -- records | NEW hosted zone

* step to create a file  / contains the records to migrate
  1. Sign in AWS Management Console & open [Route 53 console](https://console.aws.amazon.com/route53/)
     1. Sign in with the account credentials for the account / you want to migrate
  2. Get the hosted zone ID
     1. choose **Hosted zones** | navigation pannel
     2. Identify the hosted zone to migrate & check the **Hosted zone ID** column
  3. Run the  

     ```
     aws route53 list-resource-record-sets --hosted-zone-id hostedZoneIdGot > pathToOutputFile
     ```
     
     + if hosted zones / contain > 100 records 
       + & you are using AWS CLI -> automatically pagination
         + check [AWS CLInterface's pagination options](https://docs.aws.amazon.com/cli/latest/userguide/pagination.html)
       + & you use another programmatic method (_Example:_ AWS SDKs) -> you MUST submit multiple requests
     + if you are using Windows PowerShell v6.0- -> run

       ```
       aws route53 list-resource-record-sets --hosted-zone-id hosted-zone-id | Out-File path-to-output-file -Encoding utf8
       ```

       _Example:_ | Windows computer, you might run

       ```
       aws route53 list-resource-record-sets --hosted-zone-id ZOLDZONE12345 > c:\temp\list-records-ZOLDZONE12345.txt
       ```

     + if you are running AWS CLI | Windows computer's Windows PowerShell v6.0- -> you might run

       ```
       $output = aws route53 list-resource-record-sets --hosted-zone-id <hosted-zone-id>; $mypath = <output-path ;
       [System.IO.File]::WriteAllLines($mypath,$output)
       ```

## Step 4: Edit the records that you want to migrate<a name="hosted-zones-migrating-edit-records"></a>

* TODO:
The format of the file that you created in the previous procedure is close to the format that is required by the AWS CLI `change-resource-record-sets` command that you use to create records in the new hosted zone\. However, the file requires some edits\. You must apply some of the changes to every record\. You can make these changes using the search and replace function in a good text editor\.

Open a copy of the file that you created in [Step 3: Create a file that contains the records that you want to migrate](#hosted-zones-migrating-create-file), and make the following changes:
+ Delete the first two lines at the top of the output:

  ```
  {
      "ResourceRecordSets": [
  ```
+ Delete the lines related to the NS and SOA records\. The new hosted zone already has those records\.
+ *Optional* – Add a `Comment` element\.
+ Add a `Changes` element\.
+ For each record, add an `Action` and a `ResourceRecordSet` element\.
+ Add opening and closing braces \( `{ }` \) as required to make the JSON code valid\.
**Note**  
You can use a JSON validator to verify that you have all the braces and brackets in the correct places\. To find an online JSON validator, do an internet search on "`json validator`"\.
+ If the hosted zone contains any aliases that refer to other records in the same hosted zone, make the following changes:
  + Change the hosted zone ID to the ID of the new hosted zone\.
**Important**  
If the alias record is pointing to another resource, for example, a load balancer, update the hosted zone ID to the hosted zone id of the resource itself, not the hosted zone ID of the domain\. That hosted zone ID can be found from the AWS console where the resource was created\.
  + Move the alias records to the bottom of the file\. Route 53 must create the record that an alias record refers to before it can create the alias record\.
**Important**  
If one or more alias records refer to other alias records, the records that are the alias target must appear in the file before the referencing alias records\. For example, if `alias.example.com` is the alias target for `alias.alias.example.com`, `alias.example.com` must appear first in the file\.
  + Delete any alias records that route traffic to a traffic policy instance\. Make note of the records so you can recreate them later\.
+ You can use this process to create records in a hosted zone that has a different name\. For every record in the output, change the domain name part of the `Name` element to the name of the new hosted zone\. For example, if you list records in the example\.com hosted zone and you want to create records in an example\.net hosted zone, change the example\.com part of every record name to example\.net:

  From:
  + `"Name": "example.com."`
  + `"Name": "www.example.com."`

  To:
  + `"Name": "example.net."`
  + `"Name": "www.example.net."`

The following example shows the edited version of records for a hosted zone for example\.com\. The red, italicized text is new:

```
{
    "Comment": "string",
    "Changes": [
        {
            "Action": "CREATE",
            "ResourceRecordSet":{
                "ResourceRecords": [
                    {
                        "Value": "192.0.2.4"
                    }, 
                    {
                        "Value": "192.0.2.5"
                    }, 
                    {
                        "Value": "192.0.2.6"
                    }
                ], 
                "Type": "A", 
                "Name": "route53documentation.com.", 
                "TTL": 300
            }
        },
        {
            "Action": "CREATE",
            "ResourceRecordSet":{
                "AliasTarget": {
                    "HostedZoneId": "Z3BJ6K6RIION7M",
                    "EvaluateTargetHealth": false,
                    "DNSName": "s3-website-us-west-2.amazonaws.com."
            },
                "Type": "A",
                "Name": "www.route53documentation.com."
            }
        }
    ]
}
```

## Step 5: Split large files into smaller files<a name="hosted-zones-migrating-split-file"></a>

If you have a lot of records or if you have records that have a lot of values \(for example, a lot of IP addresses\), you might need to split the file into smaller files\. Here are the maximums:
+ Each file can contain a maximum of 1,000 records\.
+ The maximum combined length of the values in all `Value` elements is 32,000 bytes\.

## Step 6: Create records in the new hosted zone<a name="hosted-zones-migrating-create-records"></a>

To create records in the new hosted zone, use the following AWS CLI command: 

```
aws route53 change-resource-record-sets --hosted-zone-id id-of-new-hosted-zone --change-batch file://path-to-file-that-contains-records
```

For example:

```
aws route53 change-resource-record-sets --hosted-zone-id ZNEWZONE1245 --change-batch file://c:/temp/change-records-ZNEWZONE1245.txt
```

If you deleted any alias records that route traffic to a traffic policy instance, recreate them using the Route 53 console\. For more information, see [Creating records by using the Amazon Route 53 console](resource-record-sets-creating.md)\.

## Step 7: Compare records in the old and new hosted zones<a name="hosted-zones-migrating-compare"></a>

To confirm that you successfully created all of your records in the new hosted zone, we recommend that you list the records in the new hosted zone and compare the output with the list of records from the old hosted zone\. To do that, perform the following procedure\.<a name="hosted-zones-migrating-compare-procedure"></a>

**To compare records in the old and new hosted zones**

1. Run the following command:

   ```
   aws route53 list-resource-record-sets --hosted-zone-id hosted-zone-id --output json > path-to-output-file
   ```

   Specify the following values:
   + For *hosted\-zone\-id*, specify the ID of the new hosted zone\. 
   + For *path\-to\-output\-file*, specify the directory path and file name that you want to save the output in\. Use a file name that is different from the file name that you used in [Step 3: Create a file that contains the records that you want to migrate](#hosted-zones-migrating-create-file)\. Using a different file name ensures that the new file doesn't overwrite the old file\. 
   + The `>` character sends output to the specified file\.

   For example, if you're using a Windows computer, you might run the following command:

   ```
   aws route53 list-resource-record-sets --hosted-zone-id ZNEWZONE67890 --output json > c:\temp\list-records-ZNEWZONE67890.txt
   ```

1. Compare the output with the output from [Step 3: Create a file that contains the records that you want to migrate](#hosted-zones-migrating-create-file)\.

   Other than the values of the NS and SOA records and any changes that you made in [Step 4: Edit the records that you want to migrate](#hosted-zones-migrating-edit-records) \(such as different hosted zone IDs or domain names\), the two outputs should be identical\.

1. If the records in the new hosted zone don't match the records in the old hosted zone, you can do one of the following:
   + Make minor corrections using the Route 53 console\. For more information, see [Editing records](resource-record-sets-editing.md)\.
   + If a large number of records are missing, create a new text file that contains the missing records and then repeat [Step 6: Create records in the new hosted zone](#hosted-zones-migrating-create-records)\.
   + Delete all the records except the NS and SOA records in the new hosted zone, and repeat the following steps:
     + [Step 4: Edit the records that you want to migrate](#hosted-zones-migrating-edit-records)
     + [Step 5: Split large files into smaller files](#hosted-zones-migrating-split-file)
     + [Step 6: Create records in the new hosted zone](#hosted-zones-migrating-create-records)
     + [Step 7: Compare records in the old and new hosted zones](#hosted-zones-migrating-compare)

## Step 8: Update the domain registration to use name servers for the new hosted zone<a name="hosted-zones-migrating-update-domain"></a>

When you finish creating records in the new hosted zone, change the name servers for the domain registration to use the name servers for the new hosted zone\.

**Important**  
If you don't update the domain registration to use the name servers for the new hosted zone, Route 53 will continue to use the old hosted zone to route traffic for the domain\. If you delete the old hosted zone without updating name servers for the domain registration, the domain will become unavailable on the internet\. If you add, update, or delete records in the new hosted zone without updating name servers for the domain registration, then traffic won't be routed based on those changes\.

For more information, see [Making Amazon Route 53 the DNS service for an existing domainMaking Route 53 the DNS service for an existing domain](MigratingDNS.md)\.

**Note**  
Whether you use the process to migrate DNS service for a domain that's in use or the process for an inactive domain, you can skip the following steps because you've already created a new hosted zone and the records in that hosted zone:  
Step 1: Get Your Current DNS Configuration from the Current DNS Service Provider
Step 2: Create a Hosted Zone
Step 3: Create Records

## Step 9: Wait for DNS resolvers to start using the new hosted zone<a name="hosted-zones-migrating-wait-for-ttl"></a>

If your domain is in use—for example, if your users are using the domain name to browse to a website or access a web application—then DNS resolvers have cached the names of the name servers that were provided by your current DNS service provider\. A DNS resolver that cached that information a few minutes ago will save it for up to two days\.

**Note**  
If you created any records in the new hosted zone that don't appear in the old hosted zone, your users can't use the new records to access your resources until resolvers start using the name servers for the new hosted zone\. For example, suppose you create a record, test\.example\.com, in the new hosted zone that should route internet traffic to your website\. If the record doesn't appear in the old hosted zone, you can't enter test\.example\.com in a web browser until resolvers start using the new hosted zone\.

To ensure that migrating a hosted zone to another AWS account has completed before you delete the old hosted zone, wait for two days after you update the domain registration to use name servers for the new hosted zone\. After the two\-day TTL expires and resolvers request the name servers for your domain, the resolvers will get the current name servers\. You can also enable [Resolver query logging](resolver-query-logs.md) to monitor the queries in the new hosted zones\. For information about the pricing for Resolver query logging, see [CloudWatch pricing](http://aws.amazon.com/cloudwatch/pricing/)\.

## Step 10: \(Optional\) delete the old hosted zone<a name="hosted-zones-migrating-delete-old-hosted-zone"></a>

When you're confident that you don't need the old hosted zone any longer, you can optionally delete it\.

**Important**  
Don't delete the old hosted zone or any records in that hosted zone for at least 48 hours after you update the domain registration to use name servers for the new hosted zone\. If you delete the old hosted zone before DNS resolvers stop using the records in that hosted zone, your domain could be unavailable on the internet until resolvers start using the new hosted zone\.

The hosted zone must be empty except for the default NS and SOA records\. If the old hosted zone contains a lot of records, deleting them using the console can take a long time\. One option is to perform the following steps:

1. Make another copy of the edited file from [Step 4: Edit the records that you want to migrate](#hosted-zones-migrating-edit-records)\.

1. In the copy of the file, change `"Action": "CREATE"` to `"Action": "DELETE"` for every record\.

1. Use the following AWS CLI command to delete the records:

   ```
   aws route53 change-resource-record-sets --hosted-zone-id id-of-old-hosted-zone --change-batch file:///path-to-file-that-contains-records
   ```
**Important**  
Make sure that the value that you specify for the hosted zone ID is the ID of the old hosted zone, not the ID of the new hosted zone\.

1. Delete any remaining records and the hosted zone:

   1. Sign in to the AWS Management Console and open the Route 53 console at [https://console\.aws\.amazon\.com/route53/](https://console.aws.amazon.com/route53/)\.

      Sign in with the account credentials for the account that created the old hosted zone\.

   1. In the navigation pane, choose **Hosted zones**\.

   1. Choose the name of the old hosted zone\. If you have a lot of hosted zones, you can choose **Exact domain name** and enter the name of the hosted zone, and press **Enter** to filter the list\.

   1. If the hosted zone contains any records other than the default NS and SOA records \(such as alias records that route traffic to a traffic policy instance\), choose the corresponding check boxes and choose **Delete**\.

   1. In the navigation pane, choose **Hosted zones**\.

   1. In the list of hosted zones, choose the radio button for the hosted zone that you want to delete\.

   1. Choose **Delete**\.