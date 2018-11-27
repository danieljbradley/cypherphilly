# Philly Graph DB Meetup -  Civic Data Journalism
### Importing and querying Philly Police Complaint Data in Neo4j
 
##### Background
The Philadelphia Police Department (PPD) is the nation's fourth largest police department, with over 6300 sworn members and 800 civilian personnel. The PPD is the primary law enforcement agency responsible for serving Philadelphia County, extending over 140 square-miles in which approximately 1.5 million reside.

While the PPD has a very noble mission and __very tough__ job to do, occasionally some officers fail to follow through with this mission.  Our goal is to look through open data related to Philadelphia Police Complaints, search for patterns and, perhaps, identify officers who repeatedly violate their duties, so that this information can be shared with the public.

##### About the data
There are 3 data sets that we will join together with neo4j
+ Complaints - contains information about the officer and the incident that sparked the complaint
+ Findings - contains information about the result of the complaint
+ Complainants - contains information about the person who filed the complaint

Unfortunately, we only have officer initials i.e. "JS" instead of full name "John Smith."  This means that we do not have a unique identifier for each officer.  Food for thought: a pair of initials within a police  district could still refer to more than one officer.  To further confuse things, officers are sometimes transferred to other districts.  Please keep this in mind while working with this data.

#### Resources

Organizations:
+ [Philly Graph DB Meetup Page](https://www.meetup.com/Philly-GraphDB/)
+ [OpenDataPhilly](https://www.opendataphilly.org/)

Data:
+ [PPD Complaints Datasets (Cleaned) from Cypher Philly](https://drive.google.com/drive/folders/1iJnBiUgt9J8TGbME4fzZz97zGCklWHBM)
+ [PPD Complaints Datasets (Uncleaned) from Open Data Philly](https://www.opendataphilly.org/dataset/police-complaints)

Graph DB/Cypher Resources:
+ [Intro to Cypher Query Language](https://neo4j.com/developer/cypher-query-language/)
+ [Modeling Your Data with Arrows Tool](http://www.apcjones.com/arrows/#)

Visualization Ideas:
+ [Scrolly Telly Example](https://philadelphia.maps.arcgis.com/apps/MapJournal/index.html?appid=d498be2dde18426193679f5e9ce0e6e5)

## Set up your Neo4j database
There are a few ways to get set up with your own Neo4j database:

+ install the [desktop software](https://neo4j.com/download/)
+ set up a full version on a [server](https://neo4j.com/download-center/#releases)
+ or use Neo4j’s publicly available [Sandboxes](https://neo4j.com/sandbox-v2/) (a place in the cloud where you can practice) - see below for instructions

#### Set up your sandbox:

1. Follow [this link.](https://neo4j.com/sandbox-v2/)
2. Click __Start Now__ then __Sign Up__ or __Log In__ if you already have an account.
3. Scroll to __Blank Sandbox__ (not Neo4j 3.3) near the bottom and click __Launch Sandbox__.
4. Wait a few seconds while the Sandbox is set up.
5. Under the  __Get Started with your Neo4j Sandbox__ #1,  follow the  __Visit the Neo4j Browser__ link.

## Mini-tour of Neo4j browser
The two images below highlight the main components of the Neo4j Browser
+ On the top is the __editor__
+ On the left hand side is the __sidebar__
+ The rest of the page is the __stream__

![alt text](https://github.com/danieljbradley/cypherphilly/blob/develop/img_tutorial2_neo4jbrowser.png?raw=true "Neo4j Browser screenshot with arrows and labels")

The image below shows the controls for the editor:

![alt text](https://github.com/danieljbradley/cypherphilly/blob/develop/img_tutorial3_editor.PNG?raw=true "Neo4j editor screenshot with arrows and labels")

In a minute, we will copy and paste some code into the editor.  But first, let's look at some shortcuts that will help you work more effectively in the editor:

Shortcut | Action
--- | ---
<kbd>up</kbd> | brings back last executed code
<kbd>down</kbd> | brings back last executed code
<kbd>shift</kbd> + <kbd>enter</kbd> | brings back last executed code


More shortcuts can be found [here](https://neo4j.com/developer/guide-neo4j-browser/#_useful_commands_and_keyboard_shortcuts).



Copy the load cypher queries Below in green per dataset section Complaints, Findings and Complainants
Run the Queries below, which download and process the CSV into the database

Notes: 



Guide and Template for Neo4j Create and load: 
Guide:
CREATE  ←(This creates the entity Labels from header names from your datasets) 
(`0`:->PPD_Complaints <--(Your Dataset file name goes here)
(CSV header titles go here)-->{cap_number:"string", dist_occurrence:"string", general_cap_classification:"string", summary:"string" })

LOAD CSV WITH HEADERS FROM 'file:///ppd_complaints.csv' AS line <--(This Line loads the contents of the CSV into the Neo4j app project database)
CREATE (:PPD_Complaints←(dataset name) (CSV headers)--> { cap_number: line.cap_number, dist_occurrence: line.dist_occurrence, general_cap_classification: line.general_cap_classification, summary: line.summary })<--(This Line created nodes for the CSV you loaded into the dataset)

Example Blank Template:
CREATE
(`0`:csv_name_here 
{first_header_title:"string", second_header_title:"string", thrid_header_title:"string", forth_header_title:"string" })

LOAD CSV WITH HEADERS FROM 'file:///csv_name_here.csv' AS line
CREATE (:csv_name_here { first_header_title: line.first_header_title, second_header_title: line.second_header_title, thrid_header_title: line.thrid_header_title, forth_header_title: line.forth_header_title })










Complaints:
LOAD CSV WITH HEADERS FROM 'http://cypherphil.ly/content/ppd_complaints.csv' AS line
CREATE (:PPD_Complaints { cap_number: line.cap_number, date_received: line.date_received, dist_occurrence: line.dist_occurrence, general_cap_classification: line.general_cap_classification, summary: line.summary })

Findings:
LOAD CSV WITH HEADERS FROM 'http://cypherphil.ly/content/ppd_complaint_disciplines.csv' AS line
CREATE (:PPD_Complaint_Disciplines  { cap_number: line.cap_number, po_initials: line.po_initials, po_race: line.po_race, po_sex: line.po_sex, allegations_investigated: line.allegations_investigated, investigative_findings: line.investigative_findings, disciplinary_findings: line.disciplinary_findings })

Complainants:
LOAD CSV WITH HEADERS FROM 'http://cypherphil.ly/content/ppd_complaint_complainants.csv' AS line
CREATE (:PPD_Complaint_Complainants   { cap_number: line.cap_number, complainant_sex: line.complainant_sex, complainant_race: line.complainant_race, complainant_age: line.complainant_age, complainant_initials: line.complainant_initials })


Getting Started with the Neo4j desktop app:

Neo4j Desktop User Interface Guide: https://neo4j.com/developer/guide-neo4j-desktop/#_installing_and_starting_neo4j_desktop
Desktop Walkthrough Steps:
Download Neo4j: https://neo4j.com/download/
Open the [Neo4j Desktop App]
Scroll down and click [New Graph]
Click on [Create a local graph]
Name Graph [PPD Complaints] and set password to [password]
In the new Graph Box your created Click [Manage] 
Next, click [Open Folder] and select the [Import] Folder for the selection of folders keep that folder open and go to your browser.
Past this link to the google drive with all of the datasets for the PPD complaints https://drive.google.com/drive/folders/1iJnBiUgt9J8TGbME4fzZz97zGCklWHBM?usp=sharing and [Download] all 3 complaints CSV’s for the google drive folder.
Go to your desktop and unzip the CSV’s you just downloaded and drag them into the import folder you should still have open.
 Once the CSV’s are in your import folder [Close] the folder and go back to your neo4j desktop app and [Click] the Play button(looks like an arrowhead), then [Click] the [Open Browser] button. And sew Neo4j Browser will pop up.
 From your new Neo4j browser Highlight and copy each section below in green and past them one section at a time into your Neo4j browser Command Line bar at the top and [Click] [Play] after each section pasted into the browser command line bar.  
 Once you have pasted all 6 sections in green you’ll be ready to start querying your datasets in the Neo4j Browser. You can also view your data as nodes by clicking on the Database symbol at the top left of your Neo4j Browser. In the new pop out you’ll see each data set as a tag, click on one to view the visualization of your data
Intro to Cypher: https://neo4j.com/developer/cypher-query-language/
OpenData Philly Website:  https://www.opendataphilly.org/



Complaints Against Police
As part of the Philadelphia Police Department's (PPD) accountability processes, PPD has released two datasets: The Complaints Against Police (CAP) dataset documents the civilian complaints alleging police misconduct and the CAP Findings dataset provides demographic details of the police officer involved, the allegations, and the status of the PPD's Internal Affairs Division's investigation of and findings (if available) about the allegation. Includes data from 2013 to the present year. Updated monthly.
Datasets: 
http://cypherphil.ly/content/ppd_complaints.csv
http://cypherphil.ly/content/ppd_complaint_disciplines.csv
http://cypherphil.ly/content/ppd_complaint_complainants.csv


DataSet 1 formatting: (Copy and Paste Cypher Code in green One section at a time into your local neo4j browser instance) 

ppd_complaints:
CREATE
(`0`:PPD_Complaints 
{cap_number:"string", date_received:"string",  dist_occurrence:"string", general_cap_classification:"string", summary:"string" })

LOAD CSV WITH HEADERS FROM 'file:///ppd_complaints.csv' AS line
CREATE (:PPD_Complaints { cap_number: line.cap_number, date_received: line.date_received, dist_occurrence: line.dist_occurrence, general_cap_classification: line.general_cap_classification, summary: line.summary })



DataSet 2 formatting: (Copy and Paste Code in green One section at a time into your local neo4j browser instance)
Note: this dataset has multiple rows for cap_number, which is what Neo4j uses to join the nodes.  So there will be some rows that cannot join to the complaints and complainants data. 

ppd_complaint_disciplines:
CREATE
(`0`:PPD_Complaint_Disciplines 
{cap_number:"string", po_initials:"string", po_race:"string", po_sex:"string", allegations_investigated:"string", investigative_findings:"string", disciplinary_findings:"string" })


LOAD CSV WITH HEADERS FROM 'file:///ppd_complaint_disciplines.csv' AS line
CREATE (:PPD_Complaint_Disciplines  { cap_number: line.cap_number, po_initials: line.po_initials, po_race: line.po_race, po_sex: line.po_sex, allegations_investigated: line.allegations_investigated, investigative_findings: line.investigative_findings, disciplinary_findings: line.disciplinary_findings })


DataSet 3 formatting: (Copy and Paste Code in green One section at a time into your local neo4j browser instance)



ppd_complaint_complainants:
CREATE
(`0`:PPD_Complaint_Complainants 
{cap_number:"string", complainant_sex:"string", complainant_race:"string", complainant_age:"string", complainant_initials:"string" })

LOAD CSV WITH HEADERS FROM 'file:///ppd_complaint_complainants.csv' AS line
CREATE (:PPD_Complaint_Complainants   { cap_number: line.cap_number, complainant_sex: line.complainant_sex, complainant_race: line.complainant_race, complainant_age: line.complainant_age, complainant_initials: line.complainant_initials })


Sample Queries for PPD Datasets 


1st  Example Query (Finds [District] with the Highest [Cap Number] occurrences in [Descending Order])
MATCH (n:PPD_Complaints)
WITH COUNT(DISTINCT n.cap_number) AS occurrences, toInteger(n.dist_occurrence) AS district
RETURN occurrences, district
ORDER BY occurrences DESC

2nd Example Query (Counts [Complaints] with the Highest occurrence by [Type] and lists out in [Descending Order])
MATCH (n)  
WITH COUNT(DISTINCT n.cap_number) as complaints, n.general_cap_classification as type
RETURN type, complaints
ORDER BY complaints DESC

3rd Keyword Search in [Complaints]  [summary] Label (assault)
MATCH (n:PPD_Complaints)
WHERE n.summary CONTAINS 'assault'
RETURN n

Keyword Search in [Complaints]  [summary] label (any regular expression of variation of Assault)
MATCH (n:PPD_Complaints)
WHERE n.summary =~ '(?i).*assault.*'
RETURN n



PPD Complaint Relationships
This section will help you start building relationships in your graph 
To Delete A Relationship Label and node relationship 
MATCH (a:PPD_Complaints)-[r:HaveA]->(b:PPD_Complaint_Complainants) ←- Example Relationship Types Label to be removed
DELETE r

1st Relationship(Complaints Has A Complainant/s):
MATCH (a:PPD_Complaints),(b:PPD_Complaint_Complainants)
WHERE a.cap_number = b.cap_number
CREATE (a)-[:HAS_A]->(b)
RETURN a,b

2nd Relationship(Complaints Has A Disciplines):
MATCH (a:PPD_Complaints),(b:PPD_Complaint_Disciplines)
WHERE a.cap_number = b.cap_number
CREATE (a)-[:HAS_A]->(b)
RETURN a,b


Data Annotations
This section will help you start annotating your data to quickly search your data for specific qualities. 
To Delete A node Label.
MATCH (n)
REMOVE n:PoliceOfficers ←- Example Label to be removed


1st Annotation (Complaints where summer has assault keyword in any variation):
MATCH (n:PPD_Complaints)
WHERE n.summary =~ '(?i).*assault.*'
SET n :PPD_ASSAULT
RETURN n









311 Service and Information Requests
This represents all service and information requests since December 8th, 2014 submitted to Philly311 via the 311 mobile application, calls, walk-ins, emails, the 311 website or social media.


CREATE
(`0`:public_cases_fc 
{the_geom:"string", lon:"string", objectid:"string", service_request_id:"string", status:"string", status_notes:"string", service_name:"string", service_code:"string", the_geom_webmercator:"string", requested_datetime:"string", updated_datetime:"string", expected_datetime:"string", address:"string", zipcode:"string", media_url:"string", lat:"string", service_notice:"string" })

USING PERIODIC COMMIT 500 LOAD CSV WITH HEADERS FROM 'file:///public_cases_fc.csv' AS line
CREATE (:public_cases_fc { the_geom: line.the_geom, lon: line.lon, objectid: line.objectid, service_request_id: line.service_request_id, service_request_id: line.service_request_id, status: line.status, status_notes: line.status_notes, service_name: line.service_name, service_code: line.service_code, the_geom_webmercator: line.the_geom_webmercator, requested_datetime: line.requested_datetime, updated_datetime: line.updated_datetime, expected_datetime: line.expected_datetime, address: line.address, zipcode: line.zipcode, media_url: line.media_url, lat: line.lat, service_notice: line.service_notice })
