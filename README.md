# NoSQL Challenge: UK Food Standards Agency Data Analysis
## Project Overview
The UK Food Standards Agency evaluates various establishments across the United Kingdom and provides them with food hygiene ratings. The goal of this project is to analyze these ratings data to help the magazine Eat Safe, Love determine where to focus future articles.

### Parts 1-3: 
Part 1: Database and Jupyter Notebook Setup
Import Data:

Import the data from establishments.json into MongoDB.
Use the database name uk_food and the collection name establishments.
Record the command used for importing data and document it in the notebook.
Library Imports:

Import necessary libraries: PyMongo and Pretty Print (pprint).
MongoDB Setup:

### Create an instance of the Mongo Client.
Verify the creation of the database and collection:
List databases to ensure uk_food is present.
List collections in the uk_food database to verify establishments.
Retrieve and display one document from establishments using find_one and pprint.
Assign the establishments collection to a variable for further use.
Part 2: Update the Database
Add New Restaurant:

### Insert a new restaurant named "Penang Flavours" with the following details:
json
Copy code
{
  "BusinessName": "Penang Flavours",
  "BusinessType": "Restaurant/Cafe/Canteen",
  "BusinessTypeID": "",
  "AddressLine1": "Penang Flavours",
  "AddressLine2": "146A Plumstead Rd",
  "AddressLine3": "London",
  "AddressLine4": "",
  "PostCode": "SE18 7DY",
  "Phone": "",
  "LocalAuthorityCode": "511",
  "LocalAuthorityName": "Greenwich",
  "LocalAuthorityWebSite": "http://www.royalgreenwich.gov.uk",
  "LocalAuthorityEmailAddress": "health@royalgreenwich.gov.uk",
  "scores": {
      "Hygiene": "",
      "Structural": "",
      "ConfidenceInManagement": ""
  },
  "SchemeType": "FHRS",
  "geocode": {
      "longitude": "0.08384000",
      "latitude": "51.49014200"
  },
  "RightToReply": "",
  "Distance": 4623.9723280747176,
  "NewRatingPending": True
}
Update BusinessTypeID:

Find the BusinessTypeID for "Restaurant/Cafe/Canteen" and update the new restaurant with this BusinessTypeID.
Remove Dover Establishments:

Count the number of establishments in Dover.
Remove establishments within the Dover Local Authority.
Verify that these documents have been deleted.
Data Type Corrections:

Convert latitude and longitude from strings to decimal numbers.
Convert RatingValue from strings to integer numbers.
Part 3: Exploratory Analysis
Establishments with Hygiene Score of 20:

Find and display establishments with a hygiene score of 20.
Use count_documents to count the documents.
Convert results to a Pandas DataFrame, display the number of rows, and show the first 10 rows.
Establishments in London with High Ratings:

Query establishments in London with a RatingValue of 4 or greater.
Note: Use $regex for the London Local Authority name.
Convert results to a DataFrame, display the number of rows, and show the first 10 rows.
Top 5 Establishments with RatingValue of 5:

Find the top 5 establishments with a RatingValue of 5, sorted by the lowest hygiene score, and closest to "Penang Flavours."
Use geocode proximity and search within a 0.01 degree radius.
Establishments with Hygiene Score of 0 by Local Authority:

Aggregate and count the number of establishments with a hygiene score of 0 for each Local Authority.
Sort the results in descending order and print the top ten local authority areas.
Convert results to a DataFrame, display the number of rows, and show the first 10 rows.
Code
python
Copy code
### Database Setup and Data Export
import json
from pymongo import MongoClient
from pprint import pprint

### Connect to MongoDB and export data
mongo = MongoClient(port=27017)
db = mongo['uk_food']
cursor = db.establishments.find()
establishments_list = list(cursor)
file_path = '/Users/mauriciogil/Desktop/nosql-challenge/Resources/establishments.json'
with open(file_path, 'w') as file:
    json.dump(establishments_list, file, indent=4)

### Define latitude and longitude for "Penang Flavours"
latitude = <latitude_of_Penang_Flavours>
longitude = <longitude_of_Penang_Flavours>

### Query for top 5 establishments
degree_search = 0.01
latitude_min = latitude - degree_search
latitude_max = latitude + degree_search
longitude_min = longitude - degree_search
longitude_max = longitude + degree_search
query = {
    "RatingValue": 5,
    "Latitude": {"$gte": latitude_min, "$lte": latitude_max},
    "Longitude": {"$gte": longitude_min, "$lte": longitude_max}
}
sort = [("HygieneScore", 1)]
results = db.establishments.find(query).sort(sort).limit(5)
for result in results:
    pprint(result)

### Aggregation method used for pipeline to count establishments with a hygiene score of 0
pipeline = [
    {"$match": {"scores.Hygiene": 0}},
    {"$group": {"_id": "$LocalAuthorityName", "count": {"$sum": 1}}},
    {"$sort": {"count": -1}}
]
results = list(db.establishments.aggregate(pipeline))
df = pd.DataFrame(results)
df.columns = ['LocalAuthorityName', 'EstablishmentCount']
print(f"Number of rows in the DataFrame: {len(df)}")
print(df.head(10))

### Credits
Data Source: UK Food Establishments Database. XpertLearning Assitant, Stack Overflow, Classroom study material
Tools Used: MongoDB, Python, Pandas, JSON.
