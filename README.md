Recreate the infrastructure from the previous activity in terraform. Once your infrastructure is created, you can use the s3 sync command to upload your files to your s3 bucket.

The code for the Route53 record is already given. Also all the required “Resource” blocks are also given below. Refer to the respective resources’ documentation pages to complete the code.

You can make use of https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/iam_policy_document to form your bucket policy.


#Go to Services -> S3 -> Create Bucket (Part 1)
resource "aws_s3_bucket" "static_bucket" {
 bucket = "<ADD YOUR NAME HERE>.sctp-sandbox.com" #Naming convention <name>s3.sctp-sandbox.com
 force_destroy = true
}


resource "aws_s3_bucket_public_access_block" "enable_public_access" {
}
#Click on your bucket & Go to the Permissions tab, scroll down to Bucket Policy and click “Edit”.
resource "aws_s3_bucket_policy" "allow_public_access" {
}
#Under Static website hosting, choose Enable. (Part 2)
resource "aws_s3_bucket_website_configuration" "website" {
}


data "aws_route53_zone" "sctp_zone" {
 name = "sctp-sandbox.com"
}


resource "aws_route53_record" "www" {
 zone_id = data.aws_route53_zone.sctp_zone.zone_id
 name = "" # Bucket prefix before sctp-sandbox.com
 type = "A"


 alias {
   name = aws_s3_bucket_website_configuration.website.website_domain
   zone_id = aws_s3_bucket.static_bucket.hosted_zone_id
   evaluate_target_health = true
 }
}

