**Azure Cloud Fundamentals & ADF Data Pipeline — Week 4 Assignment**

**Objective:**

Build a complete  cloud-based data pipeline on Microsoft Azure by:

* Setting up Azure cloud infrastructure (Resource Group, Storage Account)  
* Implementing a data pipeline using Azure Data Factory (ADF)  
* Validating, copying and loading data from Blob Storage to destination  
* Configuring IAM roles for secure access management

**Azure Cloud Fundamentals:**

**What is AZURE?**

Microsoft **Azure** is a cloud computing platform and service created by Microsoft that provides on-demand access to computing resources such as servers, storage, databases, networking, analytics, artificial intelligence, and security services over the internet.  
Organizations use Azure to build, deploy, manage, and scale applications without having to purchase and maintain physical infrastructure.


![Cloud Services](https://raw.githubusercontent.com/heeral17/Celebal_Internship_Assignment/refs/heads/main/Assignment4/photos/Cloud-Service-Models.png)
**Benefits of Azure**

* Scalability and flexibility  
* High availability and reliability  
* Pay-as-you-go pricing  
* Global data centers  
* Strong security and compliance support

**In this assignment  PaaS(Platform as a Service)** — ADF and Blob Storage are both fully managed by Azure. We only configured and used them, no server management needed.


![Azure Services](https://raw.githubusercontent.com/heeral17/Celebal_Internship_Assignment/refs/heads/main/Assignment4/photos/Azure-services.png)


**Azure Data Factory (ADF)**

* **What it is:** Cloud-based ETL and data integration service.  
* **3 main sections:**  
  * **Author** — Build pipelines, datasets, linked services  
  * **Monitor** — Track pipeline runs, activity status, duration  
  * **Manage** — Configure linked services, integration runtimes, triggers

**Azure Resource Group**

* Logical container for grouping related Azure resources  
* Enables managing, monitoring and billing resources together  
* Deleting a Resource Group deletes all resources inside it  
* Naming convention best practice: rg-{project}-{environment} e.g. rg-superstore-dev

**Azure Blob Storage**

* **What it is:** Object storage service for storing massive amounts of unstructured data  
* **Blob types:** Block Blob (files like CSV, images), Append Blob (logs), Page Blob (VM disks)  
* **Access tiers:** Hot (frequent access), Cool (infrequent), Archive (rarely accessed)  
* **Used here:** Sample-S.csv uploaded as **Block Blob** with **Hot** access tier

**How to Reproduce ADF Data Pipeline**

1. Create Resource Group in Azure Portal  
2. Create Storage Account → Blob Container named data  
3. Upload CSV to data container  
4. Create Azure Data Factory instance  
5. In ADF → Manage → Create Linked Service (Azure Blob Storage)  
6. In ADF → Author → Create Source \+ Destination Datasets  
7. Create Pipeline → Add Get Metadata → Add Copy Data  
8. Connect Get Metadata output to Copy Data input  
9. Publish All → Debug or Add Trigger → Run  
10. Monitor → Pipeline runs → Verify Succeeded

**Architecture Overview**

Blob Storage (input/.csv)  
      |  
     ▼  
\[Get Metadata Activity\]  ←── Validates file existence & properties  
      |  
     ▼  
\[Copy Data Activity\]     ←── Reads CSV, copies to destination  
      |  
     ▼  
Blob Storage (output/New data)

**Data Flow**

| Stage | Detail |
| :---- | :---- |
| Source | data/Sample-S.csv — 2.18 MiB, Block Blob |
| Authentication | Access Key |
| Process | Metadata validation → Copy Data |
| Destination | data/New data — 2.18 MiB, Block Blob |
| Total Duration | 27 seconds |

**Azure Resources Created**

| Resource | Name | Type |
| :---- | :---- | :---- |
| Resource Group | heeral\_internship\_adf\_pipeline | Resource Group |
| Storage Account | internshipadfstorage | Microsoft.Storage |
| Blob Container | data | Container |
| Data Factory | Microsoft.DataFactory-20260614230850 | ADF |
| Linked Service | AzureBlobStorage1 | Azure Blob Storage |
| Source Dataset | input | DelimitedText (CSV) |
| Destination Dataset | output | DelimitedText (CSV) |

**Pipeline Details**  
**Activities**

| Activity | Type | Duration | Status |
| :---- | :---- | :---- | :---- |
| Get Metadata1 | Get Metadata | 13s | Succeeded |
| Copy data | Copy Data | 15s | Succeeded |

**ADF Components Explained**

**Linked Service:**

Defines the **connection** to Azure Blob Storage. Acts as the bridge between ADF and the storage account. Created AzureBlobStorage1 pointing to internshipadfstorage.  
**Datasets**

* **Input dataset** — Points to source CSV file in data container  
* **Output dataset** — Points to destination location in same container  
* Both configured as DelimitedText format with comma delimiter

**Get Metadata Activity**

Validates the source file before copying:

* Confirms file exists in blob container  
* Retrieves file properties (size, last modified)  
* Prevents pipeline from failing silently on missing files

**Copy Data Activity**

* Reads source CSV from input dataset  
* Writes to output dataset destination  
* Handles format conversion and encoding automatically

**IAM Roles**

| Role | Description | Scope |
| :---- | :---- | :---- |
| Reader | View all resources, no modifications | Storage Account |
| Contributor | Full access except role management | Storage Account |

Roles assigned via **Access Control (IAM)** on the storage account to provide ADF the necessary permissions to read and write blob data.

**Tech Stack**

| Tool | Purpose |
| :---- | :---- |
| Microsoft Azure | Cloud platform |
| Azure Blob Storage | Data source and destination |
| Azure Data Factory | Pipeline orchestration |
| Azure IAM | Access control |
| CSV (Superstore dataset) | Source data |

**Key Concepts Learned**

| Concept | Understanding |
| :---- | :---- |
| Resource Group | Logical container for related Azure resources |
| Blob Storage | Object storage for unstructured data like CSV, JSON |
| Linked Service | Connection definition between ADF and data store |
| Dataset | Schema representation of data within a linked service |
| Get Metadata | ADF activity to retrieve file/folder properties |
| Copy Data | ADF activity to move data between source and sink |
| IAM | Identity and Access Management for role-based access |
| Pipeline Trigger | Manual or scheduled execution of ADF pipeline |

**Author**

**Heeral Jain Data Engineering Intern**  

