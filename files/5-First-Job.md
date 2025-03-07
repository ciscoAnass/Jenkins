# Creating Your First Jenkins Freestyle Job

## Step 1: Create Your Jenkins Freestyle Job

- From the Jenkins dashboard, click "New Item"
- Enter a name ("Linux-User-Management")
- Select "Freestyle project" and click "OK"

## Step 2: Configure the Job

- Under the "Build" section, click "Add build step"
- Select "Execute shell"
- In the command text area, enter the following script:

```bash
#!/bin/bash

# System Health Monitor Script for Jenkins
# Creates a formatted report of key system metrics

# Set output file
REPORT_FILE="system_health_$(date +%Y%m%d_%H%M%S).txt"

echo "=================================================" > $REPORT_FILE
echo "  SYSTEM HEALTH REPORT - $(date '+%Y-%m-%d %H:%M:%S')" >> $REPORT_FILE
echo "=================================================" >> $REPORT_FILE

# System uptime
echo -e "\n📊 SYSTEM UPTIME" >> $REPORT_FILE
uptime >> $REPORT_FILE

# CPU information
echo -e "\n📊 CPU USAGE" >> $REPORT_FILE
top -bn1 | grep "Cpu(s)" >> $REPORT_FILE
echo -e "\nCore count:" >> $REPORT_FILE
nproc >> $REPORT_FILE

# Memory usage
echo -e "\n📊 MEMORY USAGE" >> $REPORT_FILE
free -h >> $REPORT_FILE

# Disk usage
echo -e "\n📊 DISK USAGE" >> $REPORT_FILE
df -h | grep -v "tmpfs" >> $REPORT_FILE

# Last 5 logins
echo -e "\n📊 RECENT LOGINS (LAST 5)" >> $REPORT_FILE
last -5 >> $REPORT_FILE

# Important running processes
echo -e "\n📊 KEY PROCESSES" >> $REPORT_FILE
ps aux | grep -E '(httpd|nginx|mysql|jenkins|java|docker)' | grep -v grep >> $REPORT_FILE

# Check for failed services
echo -e "\n📊 FAILED SERVICES" >> $REPORT_FILE
systemctl list-units --state=failed >> $REPORT_FILE

# Network connections
echo -e "\n📊 NETWORK CONNECTIONS" >> $REPORT_FILE
netstat -tuln | grep LISTEN >> $REPORT_FILE
```

***

## Step 3: Configure the Job

- Under "Post-build Actions", click "Add post-build action"
- You could select "E-mail Notification" to be notified when the build fails

## Step 4 : Save and Run the Job and View the Results

- Click "Save" at the bottom of the page
- You'll be taken to the job's main page
- Click "Build Now" in the left sidebar to run your job

- Once the build starts, a build number will appear under "Build History"
- Click on the build number to open the build details
- Click on "Console Output" to see the results of your shell script

![Shell JOb File](/img/6-FirstShell.png)

![Shell Console Output](/img/7-consoleoutput.png)

