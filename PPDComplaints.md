# Philly Graph DB Meetup -  Civic Data Journalism
### Importing and querying Philly Police Complaint Data in Neo4j
Contents:
* [Background](#background)
* [Data](#data)
* [Resources](#resources)
* [Set up your Neo4j Database](#Set-up-your-Neo4j-Database)
  * [Set up a sandbox](#set-up-a-sandbox)
  * [Set up desktop app](#Set-up-desktop-app)
* [Mini-tour of Neo4j browser](#mini-tour-of-neo4j-browser)
* [Loading PPD Complaint Data](#loading-ppd-complaint-data)
  * [Sandbox load](#sandbox-load)
  * [Desktop App load](#desktop-app-load)
* [Creating Relationships](#creating-relationships)
* [Sample Queries](#sample-queries)
* [Data Annotations](#data-annotations)
* [311 Service and Information Requests](#311-service-and-information-requests)

### Background
As part of the Philadelphia Police Department's (PPD) accountability processes, PPD has released a few datasets surrounding police complaints.  This tutorial will teach you how to import and start analyzing the complaint data using a Neo4j graph database.

The Philadelphia Police Department (PPD) is the nation's fourth largest police department, with over 6,300 sworn members and 800 civilian personnel. The PPD is the primary law enforcement agency responsible for serving Philadelphia County, extending over 140 square-miles in which approximately 1.5 million reside.

We recognize that law enforcement is complex, messy work. There are a lot of people out there with bad intentions and police are putting their lives on the line to protect us from these people. We also recognize that, occasionally, one of those people with bad intentions makes it into the police force.  Our goal is to help in identifying those officers so that they can no longer abuse their power.  In the longterm, less officers violating their duties will result in a better police-community relationship and a safer Philadelphia.

### Data
The three datasets released by the PPD are:

+ **Complaints** - documents civilian complaints alleging police misconduct
+ **Findings** - provides demographic details of the police officer involved, a description of the allegations, and the status of the PPD's Internal Affairs Division's investigation, and findings (if available) about the allegation
+ **Complainants** - contains demographic information about the person who filed the complaint

Includes data from 2013 to present year and is updated monthly.

We will be using the files cleaned by Cypher Philly in this tutorial, but feel free to use the original, uncleaned versions:
+ [PPD Complaints Datasets (Cleaned) from Cypher Philly](https://drive.google.com/drive/folders/1iJnBiUgt9J8TGbME4fzZz97zGCklWHBM)
+ [PPD Complaints Datasets (Uncleaned) from Open Data Philly](https://www.opendataphilly.org/dataset/police-complaints)

### Resources

Graph DB/Cypher Resources:
+ [Intro to Cypher Query Language](https://neo4j.com/developer/cypher-query-language/)
+ [Neo4j Cypher Reference Card](https://neo4j.com/docs/cypher-refcard/current/)
+ [Modeling Your Data with Arrows Tool](http://www.apcjones.com/arrows/#)

Organizations:
+ [Philly Graph DB Meetup Page](https://www.meetup.com/Philly-GraphDB/)
+ [OpenDataPhilly](https://www.opendataphilly.org/)

Visualization Ideas:
+ [Scrolly Telly Example](https://philadelphia.maps.arcgis.com/apps/MapJournal/index.html?appid=d498be2dde18426193679f5e9ce0e6e5)

### Set up your Neo4j database
Please see instructions below for how to set up a sandbox or the desktop app.  You can also set up a full version on a [server](https://neo4j.com/download-center/#releases)

#### Set up a sandbox:

1. Follow [this link.](https://neo4j.com/sandbox-v2/)
2. Click __Start Now__ then __Sign Up__ or __Log In__ if you already have an account.
3. Scroll to __Blank Sandbox__ (not Neo4j 3.3) near the bottom and click __Launch Sandbox__.
4. Wait a few seconds while the Sandbox is set up.
5. Under the  __Get Started with your Neo4j Sandbox__ #1,  follow the  __Visit the Neo4j Browser__ link.


#### Set up desktop app:

1. Download Neo4j desktop app [here](https://neo4j.com/download/).
2. **Open** the **Neo4j Desktop App**.
3. Scroll down and click **New Graph**.
4. Click on **Create a local graph**
5. Name the Graph **"PPD Complaints"** and **set your password**.
6. In the new Graph Box your created Click **Manage**.
7. Next, click the **down arrow** next to **Open Folder**, then click **Import**.
8. Return to your web browser and download the 3 PPD Complaint CSV files [here](https://drive.google.com/drive/folders/1iJnBiUgt9J8TGbME4fzZz97zGCklWHBM?usp=sharing).
9. Unzip the CSV’s you just downloaded and drag them into the import folder from step 7.
10. **Close** the folder and go back to your neo4j desktop app and **click the Play button** (looks like an arrowhead), then **click the Open Browser** button and a new Neo4j Browser will pop up.

FOr more information about how to navigate the desktop app, please see [Neo4j Desktop User Interface Guide](https://neo4j.com/developer/guide-neo4j-desktop/#_installing_and_starting_neo4j_desktop).
 
 ### Mini-tour of Neo4j browser
The two images below highlight the main components of the Neo4j Browser
+ On the top is the __editor__
+ On the left hand side is the __sidebar__
+ The rest of the page is the __stream__

![alt text](https://github.com/danieljbradley/cypherphilly/blob/develop/img_tutorial2_neo4jbrowser.png?raw=true "Neo4j Browser screenshot with arrows and labels")

The image below shows the controls for the editor:

![alt text](https://github.com/danieljbradley/cypherphilly/blob/develop/img_tutorial3_editor.PNG?raw=true "Neo4j editor screenshot with arrows and labels")

#### Keyboard shortcuts
Shortcut | Action
:---: | ---
<kbd>up</kbd> | brings previous query from history into editor (single line query only) 
<kbd>Ctrl</kbd> + <kbd>up</kbd> or <kbd>Cmd</kbd> + <kbd>up</kbd>| brings previous query from history into editor (multi-line query)
<kbd>down</kbd> | brings "next" query from history into editor (single line query only)
<kbd>Ctrl</kbd> + <kbd>down</kbd> or <kbd>Cmd</kbd> + <kbd>down</kbd> | brings "next" query from history into editor (multi-line query)
<kbd>shift</kbd> + <kbd>enter</kbd> | new line for multi-line queries (only needed for 1st new line)
<kbd>Ctrl</kbd> + <kbd>enter</kbd> or <kbd>Cmd</kbd> + <kbd>enter</kbd> | runs multi-line query

More shortcuts can be found [here](https://neo4j.com/developer/guide-neo4j-browser/#_useful_commands_and_keyboard_shortcuts).

### Loading PPD Complaint Data
Instructions for loading data into the sandbox and desktop app appear below.  First, we will take a look at a template to see the general form for importing into a sandbox:
```
LOAD CSV WITH HEADERS FROM 'http://website.com/file_name.csv' AS line
CREATE (:File_Name { header_title1: line.header_title1, header_title2: line.header_title2 })
```
`File_Name` is a label.
`header_title1` and `header_title2` are properties.

... and for importing into the desktop app:

**Query 1:**
```
CREATE
(`0`:File_Name
{header_title1:"string", header_title2:"string" })
```
**Query 2:**
```
LOAD CSV WITH HEADERS FROM 'file:///file_name.csv' AS line
CREATE (:File_Name header_title1: line.header_title1, header_title2: line.header_title2 })
````

[Naming rules and recommendations](https://neo4j.com/docs/getting-started/3.5/graphdb-concepts/#graphdb-naming-rules-and-recommendations) will help you standardize names for labels, properties and relationships.

#### Load PPD Complaint data into Neo4j Sandbox
Copy a code block below, paste into Neo4j browser and **click Play** or press <kbd>Ctrl</kbd> + <kbd>enter</kbd> to run.  (Do this individually for each of the 3 queries below!)

**Complaints:**
```
LOAD CSV WITH HEADERS FROM 'http://cypherphil.ly/content/ppd_complaints.csv' AS line
CREATE (:PPD_Complaints { cap_number: line.cap_number, date_received: line.date_received, dist_occurrence: line.dist_occurrence, general_cap_classification: line.general_cap_classification, summary: line.summary })
```

**Findings:**
```
LOAD CSV WITH HEADERS FROM 'http://cypherphil.ly/content/ppd_complaint_disciplines.csv' AS line
CREATE (:PPD_Complaint_Disciplines  { cap_number: line.cap_number, po_initials: line.po_initials, po_race: line.po_race, po_sex: line.po_sex, allegations_investigated: line.allegations_investigated, investigative_findings: line.investigative_findings, disciplinary_findings: line.disciplinary_findings })
```
**Complainants:**
```
LOAD CSV WITH HEADERS FROM 'http://cypherphil.ly/content/ppd_complaint_complainants.csv' AS line
CREATE (:PPD_Complaint_Complainants   { cap_number: line.cap_number, complainant_sex: line.complainant_sex, complainant_race: line.complainant_race, complainant_age: line.complainant_age, complainant_initials: line.complainant_initials })
```
Once you run all the code blocks above, you’ll be ready to start querying your datasets in the Neo4j Browser.  You can also view your data as nodes by clicking on the Database symbol at the top left of your Neo4j Browser. In the new pop out you’ll see each data set as a tag, click on one to view the visualization of your data.

![alt text](https://raw.githubusercontent.com/danieljbradley/cypherphilly/develop/img_tutorial9_seenodes.png "screen shot of database symbol and number to click to see all nodes")

#### Load PPD Complaint data into Neo4j desktop app
Copy a code block below, paste into Neo4j browser and **click Play** or press <kbd>Ctrl</kbd> + <kbd>enter</kbd> to run.  (Do this individually for each of the 6 queries below!)

**Complaints (Query 1):**
```
CREATE
(`0`:PPD_Complaints 
{cap_number:"string", date_received:"string",  dist_occurrence:"string", general_cap_classification:"string", summary:"string" })
```
**Complaints (Query 2):**
```
LOAD CSV WITH HEADERS FROM 'file:///ppd_complaints.csv' AS line
CREATE (:PPD_Complaints { cap_number: line.cap_number, date_received: line.date_received, dist_occurrence: line.dist_occurrence, general_cap_classification: line.general_cap_classification, summary: line.summary })
```

**Findings (Query 1):**
_Note: this dataset has multiple rows for cap_number, which is what Neo4j uses to join the nodes.  So there will be some rows that cannot join to the complaints and complainants data._ 
```
CREATE
(`0`:PPD_Complaint_Disciplines 
{cap_number:"string", po_initials:"string", po_race:"string", po_sex:"string", allegations_investigated:"string", investigative_findings:"string", disciplinary_findings:"string" })
```
**Findings (Query 2):**
```
LOAD CSV WITH HEADERS FROM 'file:///ppd_complaint_disciplines.csv' AS line
CREATE (:PPD_Complaint_Disciplines  { cap_number: line.cap_number, po_initials: line.po_initials, po_race: line.po_race, po_sex: line.po_sex, allegations_investigated: line.allegations_investigated, investigative_findings: line.investigative_findings, disciplinary_findings: line.disciplinary_findings })
```
**Complainants (Query 1):**
```
CREATE
(`0`:PPD_Complaint_Complainants 
{cap_number:"string", complainant_sex:"string", complainant_race:"string", complainant_age:"string", complainant_initials:"string" })
```
**Complainants (Query 2):**
```
LOAD CSV WITH HEADERS FROM 'file:///ppd_complaint_complainants.csv' AS line
CREATE (:PPD_Complaint_Complainants   { cap_number: line.cap_number, complainant_sex: line.complainant_sex, complainant_race: line.complainant_race, complainant_age: line.complainant_age, complainant_initials: line.complainant_initials })
```
Once you run all the code blocks above, you’ll be ready to start querying your datasets in the Neo4j Browser.  You can also view your data as nodes by clicking on the Database symbol at the top left of your Neo4j Browser. In the new pop out you’ll see each data set as a tag, click on one to view the visualization of your data.

![alt text](https://raw.githubusercontent.com/danieljbradley/cypherphilly/develop/img_tutorial9_seenodes.png "screen shot of database symbol and number to click to see all nodes")

#### Sample Queries for PPD Datasets

1. Counts **complaints** by **police district**, sorted in descending order
```
MATCH (n:PPD_Complaints)
WITH COUNT(DISTINCT n.cap_number) AS occurrences, toInteger(n.dist_occurrence) AS district
RETURN occurrences, district
ORDER BY occurrences DESC
```

2. Counts **complaints** by **type**, sorted in descending order
```
MATCH (n)  
WITH COUNT(DISTINCT n.cap_number) as complaints, n.general_cap_classification as type
RETURN type, complaints
ORDER BY complaints DESC
```

3. Keyword Search for "assault" in [Complaints] [summary] label
```
MATCH (n:PPD_Complaints)
WHERE n.summary CONTAINS 'assault'
RETURN n
```

4. Keyword Search for (any regular expression of variation of) "assault" in [Complaints] [summary] label
```
MATCH (n:PPD_Complaints)
WHERE n.summary =~ '(?i).*assault.*'
RETURN n
```


### Creating Relationships
###### This section will help you start building relationships in your graph.
1st Relationship: "Complaints Has A Complainant(s)"
```
MATCH (a:PPD_Complaints),(b:PPD_Complaint_Complainants)
WHERE a.cap_number = b.cap_number
CREATE (a)-[:HAS_A]->(b)
RETURN a,b
```
2nd Relationship: "Complaints Has A Disciplines"
```
MATCH (a:PPD_Complaints),(b:PPD_Complaint_Disciplines)
WHERE a.cap_number = b.cap_number
CREATE (a)-[:HAS_A]->(b)
RETURN a,b
```
To Delete A Relationship Label and node relationship:
```
MATCH (a:PPD_Complaints)-[r:HaveA]->(b:PPD_Complaint_Complainants)
DELETE r
```

### Data Annotations

###### This section will help you start annotating your data to quickly search your data for specific qualities. 
Create a Label to identify Complaints where summary has "assault" keyword in any variation:
```
MATCH (n:PPD_Complaints)
WHERE n.summary =~ '(?i).*assault.*'
SET n :PPD_ASSAULT
RETURN n
```

To Delete a node Label:
```
MATCH (n)
REMOVE n:PoliceOfficers ←- Example Label to be removed
```

### 311 Service and Information Requests
This represents all service and information requests since December 8th, 2014 submitted to Philly311 via the 311 mobile application, calls, walk-ins, emails, the 311 website or social media.

Query 1:
```
CREATE
(`0`:public_cases_fc 
{the_geom:"string", lon:"string", objectid:"string", service_request_id:"string", status:"string", status_notes:"string", service_name:"string", service_code:"string", the_geom_webmercator:"string", requested_datetime:"string", updated_datetime:"string", expected_datetime:"string", address:"string", zipcode:"string", media_url:"string", lat:"string", service_notice:"string" })
```
Query 2:
```
USING PERIODIC COMMIT 500 LOAD CSV WITH HEADERS FROM 'file:///public_cases_fc.csv' AS line
CREATE (:public_cases_fc { the_geom: line.the_geom, lon: line.lon, objectid: line.objectid, service_request_id: line.service_request_id, service_request_id: line.service_request_id, status: line.status, status_notes: line.status_notes, service_name: line.service_name, service_code: line.service_code, the_geom_webmercator: line.the_geom_webmercator, requested_datetime: line.requested_datetime, updated_datetime: line.updated_datetime, expected_datetime: line.expected_datetime, address: line.address, zipcode: line.zipcode, media_url: line.media_url, lat: line.lat, service_notice: line.service_notice })
```