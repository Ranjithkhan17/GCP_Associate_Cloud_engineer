# Getting Started with Google Cloud: Console & Cloud Shell

Welcome to this lab! In this hands-on guide, you'll learn the essentials of Google Cloud Platform (GCP). By the end, you'll feel confident navigating the GCP console, managing your cloud resources, and using command-line tools to automate tasks.

---

## 🛠️ What You'll Learn

By completing this lab, you'll gain practical experience with:

* **Compute Engine:** Learn how to create and manage Virtual Machine (VM) instances in the cloud.
* **IAM & Admin:** Understand how to create service accounts and manage permissions to control who can access what.
* **Cloud Shell:** Master the command-line environment that comes pre-configured and ready to use in GCP.
* **Cloud Storage:** Create storage buckets, upload files, and set up public access for sharing.
* **Cloud Shell Editor:** Learn how to edit files, clone code repositories, and update projects directly in your browser.

---

## 🚀 Step-by-Step Instructions

### Task 1: Get Familiar with the Google Cloud Console

#### Part A: Create Your First Virtual Machine

Let's start by creating a simple VM instance that you can use for testing. Follow these steps:

1. Click the **Navigation menu** (the three horizontal lines) in the top-left corner.
2. Select **Compute Engine** > **VM instances**.
3. Click the **Create instance** button.
4. For the **Name**, type `first-vm`.
5. Choose your preferred **Region** and **Zone** (pick whichever is closest to you).
6. Under **Machine type**, select **Shared-core** > **e2-micro** (this is an affordable option perfect for learning).
7. Scroll down to find **Firewall** settings and check the box for **Allow HTTP traffic** (this lets people access websites hosted on this VM).
8. Leave all other settings as they are and click **Create**.

Congratulations! Your first VM is now being created. This typically takes a minute or two.

#### Part B: Create a Service Account for Authentication

Service accounts are special accounts used by applications to authenticate with GCP services. Here's how to create one:

1. Click the **Navigation menu** again and go to **IAM & Admin** > **Service Accounts**.
2. Click **+ Create service account**.
3. Enter `test-service-account` as the **Service account name**.
4. Click **Create and continue**.
5. For **Select a role**, choose **Basic** > **Editor** (this gives the service account broad permissions for learning purposes).
6. Click **Continue**, then click **Done**.

Great! You've successfully created a service account. This account can now be used to access GCP services programmatically.

---

### Task 2: Get Comfortable with Cloud Shell Commands

Cloud Shell is like having a terminal built right into your browser. Let's explore some useful commands:

1. Look for the **Activate Cloud Shell** icon (it looks like a terminal symbol) in the top-right corner of the console title bar and click it.
2. A terminal window will open at the bottom of your screen. Copy and paste each of these commands one at a time to learn more about your GCP environment:

```bash
# 1. See which Google account you're logged in as
gcloud auth list

# 2. Check that you're working in the correct GCP project
gcloud config list project

# 3. Display your project's unique ID (you'll use this a lot!)
echo $DEVSHELL_PROJECT_ID

# 4. See what folder you're currently in
pwd

# 5. Learn more about creating storage buckets (press CTRL+C to close the help)
gcloud storage buckets create --help
```

These commands help you understand your current setup and are super useful when you start building real projects.

---

### Task 3: Create and Manage Cloud Storage Buckets

Cloud Storage is like a giant hard drive in the cloud where you can store files. Let's create some buckets (folders in cloud storage) and upload files to them.

#### Option A: Using the Web Console (Point and Click)

1. In the **Navigation menu**, go to **Cloud Storage** > **Buckets**.
2. Click **Create bucket**.
3. Name your bucket `[YOUR_PROJECT_ID]-bucket1` (replace `[YOUR_PROJECT_ID]` with your actual project ID from earlier).
4. Set **Location Type** to **Region** and select your preferred region.
5. Click **Continue**.
6. In the access control step, **uncheck** the box that says "Enforce public access prevention on this bucket" and make sure **Uniform** is selected.
7. Click **Create**.

Your first bucket is now ready to store files!

#### Option B: Using Cloud Shell Commands (Command Line)

For those who prefer typing commands, here's how to create buckets and upload files using the terminal:

Run these commands in your Cloud Shell terminal:

```bash
# 1. List all your buckets to see the one you just created
gcloud storage buckets list

# 2. Create a second bucket using the command line
# (Replace REGION with your region, like us-central1 or europe-west1)
gcloud storage buckets create gs://$DEVSHELL_PROJECT_ID-bucket2 --location=REGION

# 3. Verify both buckets are now visible
gcloud storage buckets list

# 4. Download a sample image file from Google's public storage
gcloud storage cp gs://cloud-training/ak8s/cat.jpg cat.jpg

# 5. Upload the image to your first bucket
gcloud storage cp cat.jpg gs://$DEVSHELL_PROJECT_ID-bucket1

# 6. Copy the image from bucket1 to bucket2 (no need to download it again!)
gcloud storage cp gs://$DEVSHELL_PROJECT_ID-bucket1/cat.jpg gs://$DEVSHELL_PROJECT_ID-bucket2/cat.jpg
```

#### Part C: Make Your Files Public (and Viewable by Anyone)

Now let's make the image in your bucket publicly accessible so anyone on the internet can view it:

1. Go back to your **Cloud Storage** > **Buckets** page and click on your first bucket (`[YOUR_PROJECT_ID]-bucket1`).
2. Click on the **Permissions** tab.
3. Click **Grant access** (usually found under "View by principals").
4. In the **New principals** field, type `allUsers` (this means everyone).
5. In **Select a role**, choose **Storage Object Viewer** (this lets them see the files but not delete them).
6. Click **Save** and then confirm by clicking **Allow Public Access**.
7. Find your `cat.jpg` file in the bucket and click the three dots (**⋮**) menu next to it.
8. Copy the public URL and paste it into a new private/incognito browser tab to see your cat photo!

**Important:** Making files public means anyone on the internet can download them. Always be careful about what you make public!

---

### Task 4: Create a Website and Host It on Your VM

Now for the fun part—let's create a simple webpage and host it on the VM you created earlier!

#### Step 1: Edit Files in Cloud Shell Editor

1. In your Cloud Shell terminal, click **Open Editor** (or look for the editor icon).
2. Select **File** > **Open Folder** and click **Ok**.
3. Now you have a full code editor in your browser! Let's get some starter files:

```bash
# Clone a sample project repository
git clone https://github.com/googlecodelabs/orchestrate-with-kubernetes.git

# Create a new folder for practice
mkdir test
```

4. In the file explorer on the left, find the folder `orchestrate-with-kubernetes` and open the `cleanup.sh` file.
5. At the very bottom of the file, add this line: `echo Finished cleanup`
6. Save the file (Ctrl+S on Windows/Linux or Cmd+S on Mac).

#### Step 2: Create Your HTML Webpage

1. Right-click on the `orchestrate-with-kubernetes` folder in the left panel.
2. Select **New File** and name it `index.html`.
3. Copy this HTML code into the file (replace the URL with the public URL of your cat.jpg from earlier):

```html
<html>
  <head>
    <title>My First Cloud Website</title>
  </head>
  <body>
    <h1>Welcome to My Cloud Website!</h1>
    <p>Check out this cute cat photo:</p>
    <img src="PASTE_YOUR_PUBLIC_CAT_IMAGE_URL_HERE">
  </body>
</html>
```

4. Save the file.

#### Step 3: Upload Your Webpage to Your VM

Now let's get your HTML file onto your VM instance:

```bash
# Copy the file from Cloud Shell to your VM
# Replace ZONE with your VM's zone (you can find this in the VM instances list)
gcloud compute scp index.html first-vm:index.html --zone="ZONE"
```

#### Step 4: Set Up a Web Server on Your VM

1. Use the **SSH** button next to your `first-vm` in the Compute Engine console to open a terminal connection to your VM.
2. In that SSH terminal, run these commands to install and configure a web server:

```bash
# Remove an unnecessary package to speed things up
sudo apt-get remove -y --purge man-db
sudo touch /var/lib/man-db/auto-update

# Update the system
sudo apt-get update

# Install Nginx (a popular web server)
sudo apt-get install nginx -y

# Move your webpage into the web server's folder
sudo cp index.html /var/www/html
```

The web server is now running and serving your page!

#### Step 5: View Your Website

1. Go back to **Compute Engine** > **VM instances**.
2. Find your `first-vm` and click on the **External IP** address listed in the table.
3. Your website should load in a new browser tab, complete with the cat photo!

Congratulations! You've successfully created and hosted a website on the cloud! 🎉

---

## 💡 Helpful Tips for Your Learning Journey

* **Cloud Shell Files Are Temporary:** Files you save in your Cloud Shell home directory (`~/`) stay between sessions, but after a period of inactivity, the Cloud Shell machine will reset. So don't worry if things look different after a break—you can recreate them!

* **Use Environment Variables:** Commands like `$DEVSHELL_PROJECT_ID` automatically fill in your project ID. This saves you time and helps avoid mistakes. Try to use these shortcuts whenever possible.

* **Be Careful with Public Access:** When you grant `allUsers` access to files, literally anyone on the internet can access them. Only make things public if you're sure you want that. Use this feature wisely!

* **Keep Learning:** GCP has tons of other services to explore like Cloud SQL, Cloud Functions, and BigQuery. Once you're comfortable with the basics, there's so much more to discover!

---

**Happy Cloud Learning! 🚀**

Feel free to experiment, break things (that's how you learn!), and don't hesitate to refer back to these instructions when you need a refresher.
