# DigitalOcean Spaces Upload & Image Optimization Guide (Windows + Linux)

## Overview

This documentation explains how to:

* Install and configure AWS CLI
* Connect AWS CLI with DigitalOcean Spaces
* Upload single or multiple images
* Verify uploaded files
* Remove files/folders
* Convert and compress images to WebP format
* Optimize storage and bandwidth usage

DigitalOcean Spaces is S3-compatible, so AWS CLI works with it using a custom endpoint URL.

---

# 1. Install AWS CLI on Windows

Download and install AWS CLI v2 using Command Prompt:

```powershell
msiexec.exe /i https://awscli.amazonaws.com/AWSCLIV2.msi
```

After installation, verify:

```powershell
aws --version
```

Expected output:

```powershell
aws-cli/2.x.x Python/3.x Windows/AMD64
```
---
# 2. Install AWS CLI on Linux

Download AWS CLI v2:

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
```

Install unzip:

```bash
sudo apt update
sudo apt install unzip -y
```

Extract the archive:

```bash
unzip awscliv2.zip
```

Install AWS CLI:

```bash
sudo ./aws/install
```

Verify installation:

```bash
aws --version
```

Example output:

```bash
aws-cli/2.x.x Python/3.x.x Linux/x86_64
```

---

# 2. Configure AWS CLI

Run:

```powershell
aws configure
```

Provide the following values:

```text
AWS Access Key ID: YOUR_ACCESS_KEY
AWS Secret Access Key: YOUR_SECRET_KEY
Default region name: sgp1
Default output format: json
```

## Example

```text
AWS Access Key ID: DO00XXXXXXXXXXXXXXXX
AWS Secret Access Key: abcdefghijklmnopqrstuvwxyz
Default region name: sgp1
Default output format: json
```

---

# 3. DigitalOcean Spaces Endpoint Format

DigitalOcean Spaces requires an endpoint URL.

## Format

```text
https://<region>.digitaloceanspaces.com
```

## Example

```text
https://sgp1.digitaloceanspaces.com
```

Common regions:

| Region    | Endpoint                              |
| --------- | ------------------------------------- |
| Singapore | `https://sgp1.digitaloceanspaces.com` |
| Bangalore | `https://blr1.digitaloceanspaces.com` |
| New York  | `https://nyc3.digitaloceanspaces.com` |
| Amsterdam | `https://ams3.digitaloceanspaces.com` |

---

# 4. Upload a Single File

## Command

```powershell
aws s3 cp ./file_name.png s3://bucket-name/folder/ --acl public-read --endpoint-url https://sgp1.digitaloceanspaces.com
```

## Example

```powershell
aws s3 cp ./logo.webp s3://my-space/blog-images/ --acl public-read --endpoint-url https://sgp1.digitaloceanspaces.com
```

---

# 5. Upload Multiple Files / Sync Folder

Use `sync` to upload an entire folder.

## Command

```powershell
aws s3 sync ./folder_name s3://bucket-name/folder/ --acl public-read --endpoint-url https://sgp1.digitaloceanspaces.com
```

## Example

```powershell
aws s3 sync ./webp s3://my-space/blog-images/ --acl public-read --endpoint-url https://sgp1.digitaloceanspaces.com
```

---

# 6. Verify Uploaded Files

## List Files Recursively

```powershell
aws s3 ls s3://bucket-name/folder/ --recursive --endpoint-url https://sgp1.digitaloceanspaces.com
```

## Example

```powershell
aws s3 ls s3://my-space/blog-images/ --recursive --endpoint-url https://sgp1.digitaloceanspaces.com
```

---

# 7. Remove Files

## Remove a Single File

```powershell
aws s3 rm s3://bucket-name/folder/file_name.webp --endpoint-url https://sgp1.digitaloceanspaces.com
```

## Example

```powershell
aws s3 rm s3://my-space/blog-images/logo.webp --endpoint-url https://sgp1.digitaloceanspaces.com
```

---

# 8. Remove a Folder

To remove all files inside a folder recursively:

```powershell
aws s3 rm s3://bucket-name/folder/ --recursive --endpoint-url https://sgp1.digitaloceanspaces.com
```

## Example

```powershell
aws s3 rm s3://my-space/blog-images/ --recursive --endpoint-url https://sgp1.digitaloceanspaces.com
```

---

# 9. Install WebP Tools (Linux/WSL)

For Ubuntu/Debian/WSL:

```bash
sudo apt update
sudo apt install webp -y
```

Verify installation:

```bash
cwebp -version
```

---

# 10. Convert PNG/JPG Images to WebP

## Create Output Directory

```bash
mkdir -p webp
```

---

## Convert PNG Files

```bash
for img in *.png; do
  cwebp -q 70 "$img" -o "webp/${img%.*}.webp"
done
```

---

## Convert JPG/JPEG Files

```bash
for img in *.jpg; do
  cwebp -q 70 "$img" -o "webp/${img%.*}.webp"
done
```

For `.jpeg`:

```bash
for img in *.jpeg; do
  cwebp -q 70 "$img" -o "webp/${img%.*}.webp"
done
```

---

# 11. Compare File Sizes

## Original Files

```bash
du -sh *.png
```

or

```bash
du -sh *.jpg
```

## Compressed WebP Files

```bash
du -sh webp/*
```

---

# 12. Upload Optimized WebP Images

```bash
aws s3 sync ./webp s3://bucket-name/folder/ --acl public-read --endpoint-url https://sgp1.digitaloceanspaces.com
```

---

# 13. Recommended Compression Settings

| Quality | Usage                 |
| ------- | --------------------- |
| `-q 50` | Very high compression |
| `-q 70` | Recommended balance   |
| `-q 80` | Higher quality        |
| `-q 90` | Near-original quality |

Recommended production value:

```bash
-q 70
```

---

# 14. Best Practices

## Use WebP for:

* Blog images
* Product thumbnails
* Landing page assets
* Static website images

## Benefits

* Reduced bandwidth cost
* Faster website loading
* Better Lighthouse/PageSpeed score
* Lower CDN usage
* Improved SEO performance

---

# 15. Common Issues

## `aws: command not found`

Restart terminal after installation or reinstall AWS CLI.

---

## `Unable to locate credentials`

Run:

```bash
aws configure
```

again and provide valid credentials.

---

## `AccessDenied`

Check:

* Space permissions
* Access keys
* Bucket name
* ACL settings

---

## Images Not Public

Ensure:

```bash
--acl public-read
```

is included during upload.

---

# 16. Example Full Workflow

## Step 1 — Convert Images

```bash
mkdir -p webp

for img in *.png; do
  cwebp -q 70 "$img" -o "webp/${img%.*}.webp"
done
```

---

## Step 2 — Upload Images

```bash
aws s3 sync ./webp s3://my-space/blog-images/ --acl public-read --endpoint-url https://sgp1.digitaloceanspaces.com
```

---

## Step 3 — Verify Upload

```bash
aws s3 ls s3://my-space/blog-images/ --recursive --endpoint-url https://sgp1.digitaloceanspaces.com
```


