# Configuring Jenkins Step by Step

## Configure System Settings

**General Settings :**

- Login to your Jenkins dashboard
- Click on "Manage Jenkins" in the left sidebar
- Select `"System"`

---

**1- System Email Address :**

- Scroll to "System Admin e-mail address"
- --> Enter your email address (admin@ciscoanass.com)
- --> This will be used as the sender for email notifications

**2- Jenkins URL :**

- Enter the full URL where your Jenkins instance is accessible
- or example: `http://cisco-server-ip:8080/` or `https://jenkins.ciscoanass.com/`
- This ensures links in notifications work correctly

**3- of executors :**

- Set the number of jobs Jenkins can run simultaneously on the controller
- Recommended: 2-4 for smaller servers

***

## Configure Global Tool Settings

**JDK :**

- Go back to `"Manage Jenkins"`
- Select "Tool Configuration"
- Configure JDK (if needed)

- --> Click **"Add JDK"**
- --> Provide a name **("JDK 17")**
- --> Uncheck "Install automatically" if you've installed it manually
- --> Specify JAVA_HOME path (e.g., /usr/lib/jvm/java-17-openjdk-amd64)
- --> Or leave "Install automatically" checked to let Jenkins handle it

---

**Configure Git :**

- Click "Add Git"
- Name: "Default Git"
- Path to Git executable: Usually `/usr/bin/git` (run which git in terminal to confirm)

Add any other tools you need (Maven, Gradle, Docker, etc.)

Click **"Save"**

---

## Configure Email Notifications

- Go back to "Manage Jenkins"
- Select `"System"`
- Scroll down to `"E-mail Notification"` section SMTP Server
- Enter your SMTP server (smtp.gmail.com)

**Advanced Settings :**

- Check `"Use SMTP Authentication"`
- Enter Username and Password
- Check "Use TLS"
- SMTP Port: **465** (SSL) or **587** (TLS)
- Reply-To Address: "Jenkins Support"

- --> Test Email Configuration
- --> Check your inbox for the test email
