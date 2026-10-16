# DOS-upload-image-optimization
DigitalOcean Spaces Upload & Image Optimization Guide (Windows + Linux)
Overview
This documentation explains how to:

Install and configure AWS CLI
Connect AWS CLI with DigitalOcean Spaces
Upload single or multiple images
Verify uploaded files
Remove files/folders
Convert and compress images to WebP format
Optimize storage and bandwidth usage
DigitalOcean Spaces is S3-compatible, so AWS CLI works with it using a custom endpoint URL.

1. Install AWS CLI on Windows
Download and install AWS CLI v2 using Command Prompt:

msiexec.exe /i https://awscli.amazonaws.com/AWSCLIV2.msi
After installation, verify:

aws --version
Expected output:

aws-cli/2.x.x Python/3.x Windows/AMD64
2. Install AWS CLI on Linux
Download AWS CLI v2:

curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
Install unzip:

sudo apt update
sudo apt install unzip -y
Extract the archive:

unzip awscliv2.zip
Install AWS CLI:

sudo ./aws/install
Verify installation:

aws --version
Example output:

aws-cli/2.x.x Python/3.x.x Linux/x86_64
2. Configure AWS CLI
Run:

aws configure
Provide the following values:

AWS Access Key ID: YOUR_ACCESS_KEY
AWS Secret Access Key: YOUR_SECRET_KEY
Default region name: sgp1
Default output format: json
Example
AWS Access Key ID: DO00XXXXXXXXXXXXXXXX
AWS Secret Access Key: abcdefghijklmnopqrstuvwxyz
Default region name: sgp1
Default output format: json
3. DigitalOcean Spaces Endpoint Format
DigitalOcean Spaces requires an endpoint URL.

Format
https://<region>.digitaloceanspaces.com
Example
https://sgp1.digitaloceanspaces.com
Common regions:

Region	Endpoint
Singapore	https://sgp1.digitaloceanspaces.com
Bangalore	https://blr1.digitaloceanspaces.com
New York	https://nyc3.digitaloceanspaces.com
Amsterdam	https://ams3.digitaloceanspaces.com
4. Upload a Single File
Command
aws s3 cp ./file_name.png s3://bucket-name/folder/ --acl public-read --endpoint-url https://sgp1.digitaloceanspaces.com
Example
aws s3 cp ./logo.webp s3://my-space/blog-images/ --acl public-read --endpoint-url https://sgp1.digitaloceanspaces.com
5. Upload Multiple Files / Sync Folder
Use sync to upload an entire folder.

Command
aws s3 sync ./folder_name s3://bucket-name/folder/ --acl public-read --endpoint-url https://sgp1.digitaloceanspaces.com
Example
aws s3 sync ./webp s3://my-space/blog-images/ --acl public-read --endpoint-url https://sgp1.digitaloceanspaces.com
6. Verify Uploaded Files
List Files Recursively
aws s3 ls s3://bucket-name/folder/ --recursive --endpoint-url https://sgp1.digitaloceanspaces.com
Example
aws s3 ls s3://my-space/blog-images/ --recursive --endpoint-url https://sgp1.digitaloceanspaces.com
7. Remove Files
Remove a Single File
aws s3 rm s3://bucket-name/folder/file_name.webp --endpoint-url https://sgp1.digitaloceanspaces.com
Example
aws s3 rm s3://my-space/blog-images/logo.webp --endpoint-url https://sgp1.digitaloceanspaces.com
8. Remove a Folder
To remove all files inside a folder recursively:

aws s3 rm s3://bucket-name/folder/ --recursive --endpoint-url https://sgp1.digitaloceanspaces.com
Example
aws s3 rm s3://my-space/blog-images/ --recursive --endpoint-url https://sgp1.digitaloceanspaces.com
9. Install WebP Tools (Linux/WSL)
For Ubuntu/Debian/WSL:

sudo apt update
sudo apt install webp -y
Verify installation:

cwebp -version
10. Convert PNG/JPG Images to WebP
Create Output Directory
mkdir -p webp
Convert PNG Files
for img in *.png; do
  cwebp -q 70 "$img" -o "webp/${img%.*}.webp"
done
Convert JPG/JPEG Files
for img in *.jpg; do
  cwebp -q 70 "$img" -o "webp/${img%.*}.webp"
done
For .jpeg:

for img in *.jpeg; do
  cwebp -q 70 "$img" -o "webp/${img%.*}.webp"
done
11. Compare File Sizes
Original Files
du -sh *.png
or

du -sh *.jpg
Compressed WebP Files
du -sh webp/*
12. Upload Optimized WebP Images
aws s3 sync ./webp s3://bucket-name/folder/ --acl public-read --endpoint-url https://sgp1.digitaloceanspaces.com
13. Recommended Compression Settings
Quality	Usage
-q 50	Very high compression
-q 70	Recommended balance
-q 80	Higher quality
-q 90	Near-original quality
Recommended production value:

-q 70
14. Best Practices
Use WebP for:
Blog images
Product thumbnails
Landing page assets
Static website images
Benefits
Reduced bandwidth cost
Faster website loading
Better Lighthouse/PageSpeed score
Lower CDN usage
Improved SEO performance
15. Common Issues
aws: command not found
Restart terminal after installation or reinstall AWS CLI.

Unable to locate credentials
Run:

aws configure
again and provide valid credentials.

AccessDenied
Check:

Space permissions
Access keys
Bucket name
ACL settings
Images Not Public
Ensure:

--acl public-read
is included during upload.

16. Example Full Workflow
Step 1 — Convert Images
mkdir -p webp

for img in *.png; do
  cwebp -q 70 "$img" -o "webp/${img%.*}.webp"
done
Step 2 — Upload Images
aws s3 sync ./webp s3://my-space/blog-images/ --acl public-read --endpoint-url https://sgp1.digitaloceanspaces.com
Step 3 — Verify Upload
aws s3 ls s3://my-space/blog-images/ --recursive --endpoint-url https://sgp1.digitaloceanspaces.com
