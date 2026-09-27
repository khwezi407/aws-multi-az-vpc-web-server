# Troubleshooting Log

## Issue: Web server returned ERR_CONNECTION_REFUSED

**Symptom:**
After launching the EC2 instance, the browser could not load the web page even though the instance was running and passed status checks.

**Diagnosis:**
SSHed into the instance and ran `sudo systemctl status httpd`. Apache was not running. Inspecting the User Data log showed the script had failed at the `unzip` step because Amazon Linux 2 does not include `unzip` by default.

**Fix:**
Installed Apache and unzip manually, extracted the lab application to `/var/www/html/`, and started the service.

**Commands run:**

```bash
sudo systemctl status httpd
sudo yum install -y httpd unzip
cd /tmp
sudo wget https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/CUR-TF-100-RESTRT-1/267-lab-NF-build-vpc-web-server/s3/lab-app.zip
sudo unzip lab-app.zip -d /var/www/html/
sudo systemctl start httpd
sudo systemctl enable httpd
curl http://localhost
```

**Verification:**
`curl http://localhost` returned the Apache test page. The public IP then loaded successfully in the browser.

**Lesson learned:**
User Data scripts must install their own dependencies. Always verify the application is running after instance launch by checking service status and testing locally.
