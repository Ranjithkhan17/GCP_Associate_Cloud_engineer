# Getting Started with Google Cloud: Console & Cloud Shell[cite: 1]

Welcome to this lab! This guide is designed to help you become comfortable with the Google Cloud Platform (GCP) ecosystem. You will learn how to navigate the console, manage resources, and leverage the power of Cloud Shell for automation[cite: 1].

---

## 🛠️ Lab Overview
In this lab, you will learn how to perform the following tasks[cite: 1]:
* **Compute Engine:** Create and configure Virtual Machine (VM) instances[cite: 1].
* **IAM & Admin:** Create service accounts and configure permissions[cite: 1].
* **Cloud Shell:** Activate and use the pre-configured, authenticated command-line environment[cite: 1].
* **Cloud Storage:** Create storage buckets, copy files, and configure public access[cite: 1].
* **Cloud Shell Editor:** Expansion of project files, repository cloning, and code updates[cite: 1].

---

## 🚀 Step-by-Step Instructions

### Task 1: Explore the Google Cloud Console[cite: 1]

#### Part A: Create a Virtual Machine (VM) Instance[cite: 1]
1. In the **Navigation menu**, click **Compute Engine** > **VM instances**[cite: 1].
2. Click **Create instance**[cite: 1].
3. For **Name**, type `first-vm`[cite: 1].
4. Choose your preferred **Region** and **Zone**[cite: 1].
5. For **Machine type**, select **Shared-core** > **e2-micro** (this is an inexpensive option)[cite: 1].
6. Scroll down to **Firewall** and select **Allow HTTP traffic**[cite: 1].
7. Leave all other settings at their defaults and click **Create**[cite: 1].

#### Part B: Create an IAM Service Account[cite: 1]
1. In the **Navigation menu**, click **IAM & Admin** > **Service Accounts**[cite: 1].
2. Click **+ Create service account**[cite: 1].
3. For **Service account name**, type `test-service-account`[cite: 1].
4. Click **Create and continue**[cite: 1].
5. For the role selection, choose **Basic** > **Editor**[cite: 1].
6. Click **Continue**, then click **Done**[cite: 1].

---

### Task 2: Master Cloud Shell Commands[cite: 1]

Click the **Activate Cloud Shell** icon on the top right console title bar to open your terminal, then run the following commands[cite: 1]:

```bash
# 1. List the active accounts to verify your student credentials
gcloud auth list

# 2. Verify that Cloud Shell matches your current Project ID
gcloud config list project

# 3. Print the associated Google Cloud Project ID variable
echo $DEVSHELL_PROJECT_ID

# 4. Verify your current active working directory
pwd

# 5. Check out the helpful manual pages for creating storage buckets
gcloud storage buckets create --help
# (Press CTRL+C to exit the help documentation screen)
```[cite: 1]

---

### Task 3: Manage Cloud Storage Buckets[cite: 1]

You can perform data management actions seamlessly through both the Console interface and the Cloud Shell terminal[cite: 1].

#### Option A: Using the Cloud Console UI[cite: 1]
1. Navigate to **Cloud Storage** > **Buckets** and click **Create bucket**[cite: 1].
2. Name the bucket `[YOUR_PROJECT_ID]-bucket1`[cite: 1].
3. Set **Location Type** to **Region**, choose your region, and click **Continue**[cite: 1].
4. In the access control step, **deselect** "Enforce public access prevention on this bucket" and ensure **Uniform** is selected[cite: 1].
5. Click **Create**[cite: 1].

#### Option B: Using Cloud Shell CLI & Managing Files[cite: 1]
Run these commands in your active Cloud Shell session[cite: 1]:

```bash
# 1. List existing buckets to verify the first one exists
gcloud storage buckets list

# 2. Create a second storage bucket instantly using the CLI
# (Replace REGION with your lab's assigned region)
gcloud storage buckets create gs://$DEVSHELL_PROJECT_ID-bucket2 --location=REGION

# 3. Confirm both storage buckets are visible in your project
gcloud storage buckets list

# 4. Download a test sample image file into your Cloud Shell
gcloud storage cp gs://cloud-training/ak8s/cat.jpg cat.jpg

# 5. Copy the sample image file into your first bucket
gcloud storage cp cat.jpg gs://$DEVSHELL_PROJECT_ID-bucket1

# 6. Copy the file directly from the first bucket into the second bucket
gcloud storage cp gs://$DEVSHELL_PROJECT_ID-bucket1/cat.jpg gs://$DEVSHELL_PROJECT_ID-bucket2/cat.jpg
```[cite: 1]

#### Part C: Granting Public Access via IAM[cite: 1]
1. In the console bucket list, select your first bucket `[YOUR_PROJECT_ID]-bucket1`[cite: 1].
2. Switch to the **Permissions** tab and click **Grant access** under *View by principals*[cite: 1].
3. In **New principals**, type `allUsers`[cite: 1].
4. In **Select a role**, choose **Storage Object Viewer**[cite: 1].
5. Click **Save**, then confirm by clicking **Allow Public Access**[cite: 1].
6. Click **Copy URL** next to your `cat.jpg` object and paste it into a fresh incognito tab to view it publicly[cite: 1].

---

### Task 4: Utilize the Cloud Shell Editor & Host a Webpage[cite: 1]

1. Click the **Open Editor** icon in Cloud Shell, select **File** > **Open Folder**, and click **Ok**[cite: 1].
2. Open the built-in Editor terminal and pull down standard code materials[cite: 1]:

```bash
# Clone the development lab repository
git clone [https://github.com/googlecodelabs/orchestrate-with-kubernetes.git](https://github.com/googlecodelabs/orchestrate-with-kubernetes.git)

# Create a clean practice directory
mkdir test
```[cite: 1]

3. Use the left folder navigation panel to locate `orchestrate-with-kubernetes/cleanup.sh`[cite: 1]. Click it and append `echo Finished cleanup` to the very bottom of the file[cite: 1].
4. Verify your file edits in the terminal[cite: 1]:

```bash
cd orchestrate-with-kubernetes
cat cleanup.sh
```[cite: 1]

5. Right-click the `orchestrate-with-kubernetes` folder workspace, select **New File**, and save it as `index.html`[cite: 1]. Paste this layout framework inside, swapping in the public URL you generated earlier[cite: 1]:

```html
<html><head><title>Cat</title></head>
<body>
<h1>Cat</h1>
<img src="PASTE_YOUR_PUBLIC_CAT_IMAGE_URL_HERE">
</body></html>
```[cite: 1]

6. Deploy the custom webpage structure to your virtual web server instance[cite: 1]:

```bash
# 1. Securely copy the index.html file from Cloud Shell to the VM instance
# (Replace ZONE with your instance's active zone)
gcloud compute scp index.html first-vm:index.html --zone="ZONE"

# 2. Access the VM directly using the SSH button in the Compute Engine console window.
# Inside that newly opened SSH window, configure the Nginx web application:
sudo apt-get remove -y --purge man-db
sudo touch /var/lib/man-db/auto-update
sudo apt-get update
sudo apt-get install nginx -y

# 3. Move your uploaded webpage file straight into the Nginx production root
sudo cp index.html /var/www/html
```[cite: 1]

7. Return to the **VM instances** console dashboard page and select the live link in the **External IP** column for your `first-vm` to view your hosted page[cite: 1]!

---

## 💡 Quick Tips for Success
* **Persistence:** Files saved in your Cloud Shell home directory (`~/`) stay intact across sessions, but the underlying machine framework resets if idle[cite: 1].
* **Environment Variables:** Utilizing system structures like `$DEVSHELL_PROJECT_ID` inside your scripts speeds up configurations so you don't have to look up details repeatedly[cite: 1].
* **Public Warning:** Keep in mind that applying `allUsers` access settings means absolutely anyone on the internet can download or open that file[cite: 1]. Use with caution!

---
*Happy Cloud Learning!*
