# Symbol Servers

BugSplat can process crashes using symbol files stored in external symbol servers. BugSplat supports accessing HTTP/HTTPS servers and Amazon S3 Buckets. BugSplat caches files from external symbol servers, ensuring fast crash calculation. Please note that if BugSplat requests a file not present on your external symbol server, it will not try to access the file again for 24 hours.

### HTTPS

BugSplat can access symbol files over HTTPS. This method also supports [Windows SymStore/SymProxy](https://learn.microsoft.com/en-us/windows-hardware/drivers/debugger/symbol-stores-and-symbol-servers) symbol servers. To connect an HTTPS symbol server to BugSplat, navigate to the [Symbols](https://app.bugsplat.com/v2/database/symbols?) page. Click the **+ Add Server** button, add a **URL** value, and click **OK**.

<figure><img src="../../../.gitbook/assets/output (1).gif" alt=""><figcaption><p>Adding an External HTTP Symbol Server</p></figcaption></figure>

{% hint style="info" %}
Some organizations restrict access to their symbol servers by IP address. To allow access to BugSplat, add the IP addresses [23.22.79.2](https://www.whatismyip.com/23.22.79.2/?iref=home), [3.93.104.250](https://us-east-1.console.aws.amazon.com/ec2/home?region=us-east-1#ElasticIpDetails:AllocationId=eipalloc-0da0d84b88eed6812), and [34.194.164.107](https://us-east-1.console.aws.amazon.com/ec2/home?region=us-east-1#ElasticIpDetails:AllocationId=eipalloc-0cd966956c064a2e4) to your whitelist.
{% endhint %}

### AWS S3

For AWS S3 symbol servers, you'll want to create a minimally permissive [IAM User](https://docs.aws.amazon.com/IAM/latest/UserGuide/id\_users\_create.html#id\_users\_create\_console) so that BugSplat can access your S3 bucket. Modify this example policy and attach it to your newly created IAM User.

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::{your-s3-bucket}",
                "arn:aws:s3:::{your-s3-bucket}/*"
            ]
        }
    ]
}
```

Once you create your IAM user, generate an [access key/secret](https://docs.aws.amazon.com/IAM/latest/UserGuide/id\_credentials\_access-keys.html) to allow BugSplat to access your S3 bucket. Navigate to the [Symbols](https://app.bugsplat.com/v2/database/symbols) page and click **+ Add Server**. Select **AWS-S3** in the **Type** dropdown. Enter values for **URL**, **Region**, **Access Key**, **Secret Key**, and click **OK.**

<figure><img src="../../../.gitbook/assets/output (1) (1).gif" alt=""><figcaption><p>Adding an External S3 Symbol Server</p></figcaption></figure>
