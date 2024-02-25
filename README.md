# Static Website hosting - AWS project.

### Aim :

* To host a Secured Static website in an AWS environment.

### What you will accomplish :
* Build a Static website.
* Make it work with a custom domain name.
* Incorporate Cloudfront and SSL/HTTPS.


### Services used :
1. AWS S3
2. AWS Amplify
3. AWS Route 53
4. AWS Certificate Manager
5. AWS Cloudfront


### Architecture 
![alt text](arch.png)


## Steps : 

### 1. Create your bucket for hosting your website.

* While creating the bucket, please note that your bucket name should match your custom domain name. In this example - aryank.click.
* For your bucket to be accessible publicly, disable Block public access.
![alt text](image.png)
* Go ahead and create your bucket.
* Enable Static Website Hosting :
    * Open your bucket and go into the Properties tab. Scroll down and click on edit for Static Website Hosting.
    * Enable Static web hosting and in Index document box enter index.html and save changes.
    ![alt text](image2.png)
* Apply bucket policy for public access : 
    * Go into the Permissions tab. Scroll down and edit the bucket policy as shown below
```json
    {
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::aryank.click/*"
        }
    ]
}
```
#### Remember to update your bucket name in the "Resource" key value.

* Upload your HTML, CSS, and Javascript code to the bucket.
* Once uploaded you can now test your website in the Static website hosting section. A bucket website endpoint will be available. Open it to view your website.
![alt text](image3.png)

Currently, this website is served on a fully qualified domain name. We need to make it available on our custom domain name.


### 2. Host your website on your custom domain.

* Go to AWS Route 53. In hosted zones, open your domain and create a record. This record will redirect incoming traffic to our website in the S3 bucket.
* Click on Create Record. Select simple routing. Select Define simple record and in endpoint section choose Alias to S3 website endpoint.
* Select your region and bucket accordingly and create the record.
![alt text](image4.png)
* Now you can use your custom domain to open your website.

### 3. Request an SSL certificate for your domain.
#### Note : Change your region to us-east-1(N.Virginia) to create your certificate. This is important as later in the steps, Cloudfront specifically requires certificates created in  US-east-1 (N.Virginia) region only.
* Open Amazon Certificate Manager. request a public certificate.
* Enter your fully qualified domain name.
* Leave everything on default and click on request.
* Now a request will be executed. Meanwhile, open your requested certificate and in the Domain section click on Create records in Route 53. This will create a CNAME record of our domain in Route 53.
 
 ### 4. Apply certificate to your domain.

 #### Note: You cannot apply SSL certificates directly to your S3 bucket. We will have to setup a Cloudfront distribution that points to our website in the S3 bucket. SSL certificate will then be assigned to this distribution

 * Open AWS Cloudfront and click on Create a Cloudfront distribution.
 * Enter origin domain. A dialogue box will appear asking whether to use the S3 website endpoint rather than the bucket endpoint. Click on Use website endpoint.  
 ![alt text](image5.png)

 * In the Viewer section, select Redirect HTTP to HTTPS.  
 ![alt text](image6.png)
 * In the Web Application Firewall section, select Do not enable security protections to keep it simple.
 * In the Settings section, add your custom domain name as your alternate domain and select your custom SSl certificate.  
 ![alt text](image7.png)
 * In the Default root object section, enter index.html
 * Leave everything else default and then select Create distribution.

 ### 5. Redirect Route 53 to CloudFront distribution.
 * Open Route53 and edit your A record.
 * Change route traffic: Alias to CloudFront distribution and select your distribution. Click save.

### Test your website
 Go to your domain. your website will be hosted securely.

## Congratulations!
### You have successfully hosted a Secured Static website.
