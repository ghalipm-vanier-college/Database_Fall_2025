
# Module 01: Database Systems

## 1.1 Chapter Objectives 


By the end of this chapter, you should be able to:

  * Define the difference between data and information. 
  * Describe what a database is, its various types, and why they are valuable for decision-making. 
  * Explain the importance of database design. 
  * Understand how modern databases evolved from file systems. 
  * Understand the flaws in file system data management. 
  * Outline the main components of a database system. 
  * Describe the main functions of a database management system (DBMS).

## 1.2 Data vs Information

* **Data** consists of **raw facts** that have `not been processed` to reveal meaning to the end user. 

* **Information** is the `result of processing raw data` to reveal its meaning, and it requires context to be understood.
* **Data** is the foundation of information, which in turn is the bedrock of knowledge. 

* **Accurate, relevant, and timely information** is crucial for good decision-making.

## 1.3 Introducing the Database

* A **database** is a shared, integrated computer structure that stores a collection of end-user data and metadata. 

* **End-user data** consists of `the raw facts` of interest to the user. 

* **Metadata** is "data about data" that describes the data's characteristics and the relationships linking the data within the database. 

* A **database management system (DBMS)** is a collection of programs that `manages the database structure` and `controls access to the data`. It presents end-users with a single, integrated view of the data.

## 1.4 Advantages of using a DBMS include:
* Advantages:
  * Improved data sharing 
  * Improved data security 
  * Better data integration
  * Minimized data inconsistency 
  * Improved data access and decision-making 
  * Increased end-user productivity     
<img width="952" height="440" alt="image" src="https://github.com/user-attachments/assets/0b2a040b-c74f-47da-8efd-27e6759e5f22" />
Figure 1.4  The DBMS Manages the Interaction Between the End User and the Database

## 1.5 Types of Databases
Databases can be classified in several ways:    

* ### By Number of Users 
  * **Single-user database**: Supports only one user at a time (e.g., a desktop database).
  * **Multi-user database**: Supports multiple concurrent users (e.g., a workgroup database for a small group or an enterprise database for a large organization). 

* ### By Location 
  * **Centralized database**: Data is located at a single site. 
  * **Distributed database**: Data is spread across different sites. 
  * **Cloud database**: Created and maintained using cloud data services. 

* ### By Data Type and Purpose 
  * **General-purpose databases**: Contain a wide variety of data for multiple disciplines. 
  * **Discipline-specific databases**: Focused on a specific subject area. 
  * **Operational database**: Supports a company's day-to-day operations. 
  * **Analytical database**: Stores historical data for decision-making and business intelligence. This includes **data warehouses** and **online analytical processing (OLAP) tools**. 

* ### By Data Structure 
  * **Unstructured data**: Exists in its original, raw state. 
  * **Structured data**: Formatted to facilitate storage and use. 
  * **Semistructured data**: Has been partially processed. An 
  * **XML database** is an example that manages unstructured XML data.

## 1.6 Problems with File System Data Processing
* Traditional `file systems` present several challenges `compared to modern databases`: 
  * **Structural Dependence**: Access to a file depends on its structure, meaning changes to the file structure require modifications to all programs that access it. 
  * **Data Dependence**: Changes to data storage characteristics necessitate changes in all data access programs. 
  * **Data Redundancy**: The same data is stored unnecessarily in different places, which can lead to data inconsistency, data-entry errors, and poor data security. This is often referred to as "islands of information." 
  * **Data Anomalies**: When redundant data is not updated successfully, it can result in update, insertion, or deletion anomalies.
<img width="914" height="706" alt="image" src="https://github.com/user-attachments/assets/58e8f1a6-b77c-418c-8839-294ebf737302" />
Figure 1.9  Contrasting Database and File Systems

## 1.7 The Database System Environment
* A database system is composed of five main components: 
* **Hardware**
* **Software**
* **People**
* **Procedures**
* **Data**
<img width="945" height="611" alt="image" src="https://github.com/user-attachments/assets/42ed042d-9675-4a24-8190-073b95c90566" />
Figure 1.10  The Database System Environment

## 1.8 DBMS Functions
* A DBMS performs several critical functions to manage a database: 
* **Data Dictionary Management**: Stores definitions of data elements and their relationships. 
* **Data Storage Management**: Manages the structures for data storage. 
* **Data Transformation and Presentation**: Transforms data to match the user's logical view. 
* **Security Management**: Enforces user security and data privacy. 
* **Multiuser Access Control**: Ensures that multiple users can access the database concurrently without compromising integrity. 
* **Backup and Recovery Management**: Provides tools to ensure data safety and integrity. 
* **Data Integrity Management**: Enforces integrity rules to minimize redundancy. 
* **Database Access Languages**: Provides a query language, such as Structured Query Language (SQL), which is the de facto standard. 
