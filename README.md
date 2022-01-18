### Create Static webpage with S3 and Docker

Here's a different method on how we can host our static website using docker and AWS S3. The webpages are saved in AWS S3, which will be mounted to the host via s3fs. This mount point can be used to create a docker volume. This will eliminate the need to take backup.

See the detailed step followed by me do so

**Step 1:  Install and configure aws cli**

 In the host machine  the aws-cli must be configured inorder to access resources ia command line. As I have used the centos server for the purpose , the below commands are used. We can also use an EC2 instance.
 
#pip install --upgrade --user awscli
#aws configure    / give the creadentials

**Step 2: Install s3fs in the host machine and mount it into the server**

#sudo yum install s3fs-fuse

Now create a mount point for the s3 bucket on our server

#sudo mkdir /mnt/static-page/

#sudo s3fs <Bucket_name> /mnt/static-page -o allow_other -o default_acl=public-read -o use_cache=/tmp/s3fs

**Step 3: Copy the code to S3 or mount directory***

As my website code was on git hub , I can clone that from git .You can place the wesite code in our own way.Then copy that website code to the correponding mount point created
 
 #cp -r ~/web/  /mnt/static-page
 
 **Step 4 : Container creation***
 
 Use the S3 Bucket Files in a Docker Container.Here I have used an apache container image for hosting the site

#docker run -d --name web1 -p 80:80 --mount type=bind,source=/mnt/web,target=/usr/local/apache2/htdocs,readonly httpd:latest

**Step 5 : Verify ***

In web browser, verify connectivity to the container

<SERVER_PUBLIC_IP_ADDRESS>

**Step 5: Create a new page within the bucket:**

#cp index.html newPage.html

In a web browser, verify that the new page is accessible:
<SERVER_PUBLIC_IP_ADDRESS>/new.html

Verify that the page was added to the bucket:
#aws s3 ls <bucket_name>
