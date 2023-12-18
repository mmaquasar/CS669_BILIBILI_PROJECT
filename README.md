# Index

### 1. Project Overview
### 2. Create Tables
### 3. Constraints
### 4. Specialization-Generalization Relationship 
### 5. Crawler making - Data processing
### 6. Structure modify with data entry
### 7. stored procedures
### 8. normalizing-eliminate data redundancy
### 9. queries & conclusions

---


# 1. Project Overview

### The BiliTrendAnalyzer project is designed to extract and examine Bilibili's trending video data. Utilizing a web scraper

### the project captures key performance indicators like views, likes, and comments to identify emerging trends in keywords and video topics with precision and clarity.

---

# 2. CREATE TABLES

### 1. Videos Table - Primary and Foreign Key Constraints 
![Videos Table PK and FK Constraints](https://cdn.discordapp.com/attachments/1136758352274260142/1169388506976620644/image.png)

### 2. Listing All Tables 
![List of All Tables](https://cdn.discordapp.com/attachments/1136758352274260142/1169389691473236038/image.png)

### 3. Videos Table Indices 
![Videos Table Indices](https://cdn.discordapp.com/attachments/1136758352274260142/1169389803108839565/image.png)

### 4. ContentCreators Table Indices 
![ContentCreators Table Indices](https://cdn.discordapp.com/attachments/1136758352274260142/1169389907677024327/image.png)

### 5. Tables
![ContentCreators Table Indices](https://cdn.discordapp.com/attachments/1136758352274260142/1185776375077015562/image.png?ex=6590d74e&is=657e624e&hm=9d7a4163768c92becaffc90edc9c611a4aa07af4bfa5e70b27e4643bb2123031&)


---

# 3.Constraints

<h3>Relationships & Constraints Explanation</h3>

<ul>
  <li><strong>Video - Keyword Relationship:</strong>
    <ul>
      <li>Allows one video to be associated with multiple keywords and vice versa, enabling rich, multifaceted categorization and searchability of videos.</li>
      <li>Unique combination of VideoID and KeywordID prevents duplicate associations, ensuring data integrity.</li>
    </ul>
  </li>

  <li><strong>Video - Content Creators Relationship:</strong>
    <ul>
      <li>Defines a one-to-many relationship where a single creator can produce multiple videos, reflecting real-world content creation dynamics.</li>
      <li>Each video is linked to one specific creator, ensuring clear attribution and simplifying content management.</li>
    </ul>
  </li>

  <li><strong>Video - Video Categories Relationship:</strong>
    <ul>
      <li>One category can encompass numerous videos, facilitating organized content categorization.</li>
      <li>Every video is assigned to a single category for simplified classification and user navigation.</li>
    </ul>
  </li>

  <li><strong>Video - Comments Relationship:</strong>
    <ul>
      <li>Enables a video to have a varying number of comments, mirroring real user engagement.</li>
      <li>Links each comment to a specific video, maintaining clarity in discussions.</li>
    </ul>
  </li>
</ul>

<h4>Rules & Constraints:</h4>

<ul>
  <li>Unique identifiers (VideoID, KeywordID, etc.) prevent data duplication and enable precise querying.</li>
  <li>Character limits on titles and names ensure data uniformity and storage efficiency.</li>
  <li>Non-negative constraints on numerical fields like views and likes reflect logical data values.</li>
  <li>Datetime fields with default values provide temporal context while maintaining data consistency.</li>
  <li>Indices on frequently queried columns enhance query performance.</li>
  <li>Cascading options in foreign key relationships safeguard data integrity during updates/deletions.</li>
</ul>
## SQL Table Definitions:



```sql
-- Video Categories
CREATE TABLE VideoCategories (
    CategoryID INT PRIMARY KEY,
    CategoryName VARCHAR(255)
);

-- Keywords
CREATE TABLE Keywords (
    KeywordID INT PRIMARY KEY,
    KeywordName VARCHAR(255)
);

-- Content Creators
CREATE TABLE ContentCreators (
    CreatorID INT PRIMARY KEY,
    CreatorName VARCHAR(255),
    FollowersCount INT CHECK (FollowersCount >= 0),
    TotalVideosPublished INT CHECK (TotalVideosPublished >= 0),
    CreatorType VARCHAR(50) CHECK (CreatorType IN ('Independent', 'OfficialBrand'))
);

-- Independent Creators
CREATE TABLE IndependentCreators (
    CreatorID INT FOREIGN KEY REFERENCES ContentCreators(CreatorID),
    IndependentChannelName VARCHAR(255),
    PersonalBackground TEXT
);

-- Official Brand Channels
CREATE TABLE OfficialBrandChannels (
    CreatorID INT FOREIGN KEY REFERENCES ContentCreators(CreatorID),
    BrandName VARCHAR(255),
    OfficialContactInformation VARCHAR(255)
);

-- Videos
CREATE TABLE Videos (
    VideoID INT PRIMARY KEY,
    VideoTitle VARCHAR(255),
    Description TEXT,
    PublishTime DATETIME DEFAULT GETDATE(),
    Views INT CHECK (Views >= 0),
    Likes INT CHECK (Likes >= 0),
    CreatorID INT FOREIGN KEY REFERENCES ContentCreators(CreatorID),
    CategoryID INT FOREIGN KEY REFERENCES VideoCategories(CategoryID)
);

-- Video-Keyword Association
CREATE TABLE VideoKeywordAssociation (
    VideoID INT FOREIGN KEY REFERENCES Videos(VideoID),
    KeywordID INT FOREIGN KEY REFERENCES Keywords(KeywordID),
    PRIMARY KEY (VideoID, KeywordID)
);

-- Comments
CREATE TABLE Comments (
    CommentID INT PRIMARY KEY,
    VideoID INT FOREIGN KEY REFERENCES Videos(VideoID),
    CommentText TEXT,
    CommentTime DATETIME DEFAULT GETDATE()
);

```
---


# 4. Specialization-Generalization Relationship


### To understand the diversity within YouTube's content creator community, we're applying a specialization-generalization relationship to categorize creators into distinct subsets, each with unique attributes.


### 1. Independent Creators:
- **Independent Channel Name**: The personal or brand name of the creator.
- **Personal Background**: Details like education, hobbies, signifying individual or small team operations.
Independent creators function without formal ties to larger entities, focusing on autonomous content production.

### 2. Official Brand Channels:
- **Brand Name**: The official channel name tied to a corporate entity.
- **Official Contact Information**: Direct communication channels like email, phone numbers, etc.
Brand channels represent structured organizations with dedicated content production and management teams.

---

# 5. Crawler making - Data processing

## **Video basic data**    (Get top 100.py)
#### request the top 100 views videos of the day and according bvid ----------------- get the data and import to csv

#### codes

![Failed To Obtain Creater Information](https://cdn.discordapp.com/attachments/1136758352274260142/1185785676189401128/image.png?ex=6590dff8&is=657e6af8&hm=375ef7e332b7a74befaa2d8db0b3e171f2b0c3afa03ac7bcb10e22f5bf477003&)
#### excel

![Failed To Obtain Creater Information](https://cdn.discordapp.com/attachments/1136758352274260142/1185791870933151744/image.png?ex=6590e5bc&is=657e70bc&hm=972360572ee2141519dd0d03c411aac122e1610a836529fd1c4d54b32f2273d9&)

---
## bvid, aid, and cid
### Bilibili video data, danmuku and comments are controlled by three separate IDs, These IDs require specific algorithms for translation and combination. During data retrieval, there is no fixed video ID; instead, individual IDs are used, which necessitate translation and amalgamation to be usable.
---
## **danmuku & Comments crawler**: (DataCollection.py)
#### request the top 100 views videos of the day and according bvid ------------ transfer bvid into av & cid --------- crawl danmuku and comments using av and cid. 


![Failed To Obtain Creater Information](https://cdn.discordapp.com/attachments/1136758352274260142/1185793533211320380/image.png?ex=6590e749&is=657e7249&hm=3ad97bec3fcb03d38f945b7f972c455711d5c474246c2cc47017013e1fd6c22b&)


## **Key word table**:  (keywords.py)
#### The jieba module is used to analyze the chinese words of danmuku and select a valid word with the highest frequency of each video, export as keyword csv.

![Failed To Obtain Creater Information](https://cdn.discordapp.com/attachments/1136758352274260142/1185795417498206289/image.png?ex=6590e90a&is=657e740a&hm=fe6afe29c72f21baf1bf78746f72c9c94a98e9a57b8b3997bde55bf06e81b9ec&)

---

# 6. Structure modify with data entry
![Failed To Obtain Creater Information](https://cdn.discordapp.com/attachments/1136758352274260142/1185817757929967677/image.png?ex=6590fdd8&is=657e88d8&hm=de72e977b98ca5515b877e2ecec70959abd2bf92cdfc67eb709742e17e5af2e7&)

## Refined data Structure adapting processed data:
![Failed To Obtain Creater Information](https://cdn.discordapp.com/attachments/1136758352274260142/1186302382745858160/image.png?ex=6592c130&is=65804c30&hm=92afb8cd8c8f6c3a64e7ae61504d251b31aa8708f8f6cf0e5b2f754610195afd&)

## Filled in table overview
![Failed To Obtain Creater Information](https://cdn.discordapp.com/attachments/1136758352274260142/1186299263957864458/image.png?ex=6592be48&is=65804948&hm=365be042dd06e04142daef408cd2996da4f1ad4b021922a66ed3627064471b4e&)

# 7. creating Procedure

## Inserting Data by filename+date
### Automatically capture real time date:
![Failed To Obtain Creater Information](https://cdn.discordapp.com/attachments/1136758352274260142/1186408942092431461/image.png?ex=6593246e&is=6580af6e&hm=da10da286c8b23fd5af065397755e08447272564377aa21383686213e1e90432&)

## SQL Definitions:

```sql

CREATE PROCEDURE BulkInsertWithDate
    @BaseFilePath NVARCHAR(MAX), 
    @FileExtension NVARCHAR(10) 
AS
BEGIN
    SET NOCOUNT ON;

    DECLARE @FilePath NVARCHAR(MAX);
    DECLARE @Date NVARCHAR(10) = CONVERT(NVARCHAR(10), GETDATE(), 120); 
    SET @Date = REPLACE(@Date, '-', '/'); 
    SET @FilePath = @BaseFilePath + '_' + @Date + @FileExtension;

    -- Construct the dynamic SQL command for the BULK INSERT
    DECLARE @Sql NVARCHAR(MAX);
    SET @Sql = N'BULK INSERT dbo.Users FROM ''' + REPLACE(@FilePath, '''', '''''') + ''' WITH (FIELDTERMINATOR = '','', ROWTERMINATOR = ''\n'', FIRSTROW = 2, TABLOCK)';

    -- Execute the dynamic SQL command
    EXEC sp_executesql @Sql;
END;
GO


EXEC BulkInsertWithDate @BaseFilePath = 'C:\\Users\\ASUS\\Desktop\\UserTable', @FileExtension = '.csv';
-- Repeat this call with modified paths and table names for other CSV files and tables

```
---

# 8. normalizing-eliminate data redundancy

#### I created a dedicated Users table to centralize all user-related information. This approach helped me reduce data redundancy and ensured the consistency and integrity of user information. Through this, I was able to manage user data more effectively, while simplifying queries and updates related to users.

#### I changed the data types of certain fields from INT to BIGINT, such as FollowersCount in the ContentCreators table and Views and Likes in the Videos table. This change was made to accommodate the growth in data volume, ensuring that the database could handle a larger range of values and preventing potential data overflow issues.

#### I optimized the table structure within the database, removing tables and fields that were no longer needed. This pruning helped improve the efficiency and maintainability of the database, while also reducing the need for storage space. This streamlined approach made the database more focused on core data, enhancing overall performance.

#### To improve query performance, I created indexes for frequently queried fields, such as VideoTitle in the Videos table and KeywordName in the Keywords table. These indexes significantly increased the query speed for these fields, especially when dealing with large volumes of data.

---

# 9. queries & conclusions

## reflection（1）  Lessons from Comment Sections
#### When I was scraping comment data from Bilibili videos, some videos had their comment sections closed due to sensitive content. At the time, I didn't pay much attention to this and simply set up the crawler to skip these videos when it encountered an error. This decision, which seemed logical, actually laid hidden pitfalls for later when I was integrating the data. Videos without comment data were like missing puzzle pieces in my dataset, preventing me from perfectly matching bvids with comments.

#### This small oversight cost me dearly, making me realize that no matter how insignificant data may seem, it's all part of the bigger picture. So, I've decided to change my approach. If I encounter this situation again, I'll have the crawler create an empty file, rather than doing nothing. This way, even a blank space represents the real state of the video at that moment—an area without comments.

#### This change means that in the future, no matter what data I'm handling, I'll leave a clear record, even if it's zero comments. It's not just for the sake of data integrity but also a reminder to myself to maintain rigor in my work. After all, in the world of data, nothing should be overlooked.





## reflection (2）   Navigating Encoding Challenges in Data Aggregation
#### I aimed to merge multiple CSV files into a single one. Initially, I encountered encoding issues when reading the files and attempted various encodings, but to no avail.

#### I realized that some files might already be in UTF-8 encoding or contain characters that GBK couldn't decode. To address this, I used the chardet library to detect the encoding of the files and only converted those that weren't already in UTF-8. Despite this, the conversion still failed for some files, forcing me to resort to the cumbersome method of manually copying this data.

#### This experience has taught me the importance of planning ahead for data collection, designing the data specifications, size, and format from the outset, and striving to capture the data in its final form as much as possible, to avoid wasting excessive time during data processing.


