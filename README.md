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
## Find the video category with the most comments from male users：

![image](https://github.com/mmaquasar/CS669_BILIBILI_PROJECT/assets/141074130/e7c91648-c686-45c8-9d97-4e2cb25c4caf)

## Gender Distribution

![image](https://github.com/mmaquasar/CS669_BILIBILI_PROJECT/assets/141074130/6e57c700-ad97-4540-9f12-06c3e9933824)

## Which keywords are most often associated with videos.

![image](https://github.com/mmaquasar/CS669_BILIBILI_PROJECT/assets/141074130/7fe0d53b-dfc3-4e06-b6bc-fccb1d24245e)

## Active User 

![image](https://github.com/mmaquasar/CS669_BILIBILI_PROJECT/assets/141074130/f9b95633-bb1b-4662-9135-3d745604ba89)

---

## Reflection（1）  Lessons from Comment Sections
#### When I was scraping comment data from Bilibili videos, some videos had their comment sections closed due to sensitive content. At the time, I didn't pay much attention to this and simply set up the crawler to skip these videos when it encountered an error. This decision, which seemed logical, actually laid hidden pitfalls for later when I was integrating the data. Videos without comment data were like missing puzzle pieces in my dataset, preventing me from perfectly matching bvids with comments.

#### This small oversight cost me dearly, making me realize that no matter how insignificant data may seem, it's all part of the bigger picture. So, I've decided to change my approach. If I encounter this situation again, I'll have the crawler create an empty file, rather than doing nothing. This way, even a blank space represents the real state of the video at that moment—an area without comments.

#### This change means that in the future, no matter what data I'm handling, I'll leave a clear record, even if it's zero comments. It's not just for the sake of data integrity but also a reminder to myself to maintain rigor in my work. After all, in the world of data, nothing should be overlooked.





## Reflection (2）   Navigating Encoding Challenges in Data Aggregation
#### I aimed to merge multiple CSV files into a single one. Initially, I encountered encoding issues when reading the files and attempted various encodings, but to no avail.

#### I realized that some files might already be in UTF-8 encoding or contain characters that GBK couldn't decode. To address this, I used the chardet library to detect the encoding of the files and only converted those that weren't already in UTF-8. Despite this, the conversion still failed for some files, forcing me to resort to the cumbersome method of manually copying this data.

#### This experience has taught me the importance of planning ahead for data collection, designing the data specifications, size, and format from the outset, and striving to capture the data in its final form as much as possible, to avoid wasting excessive time during data processing.

---

# Future thoughs:

### I'm looking to improve the web scraping aspect of my project, particularly by automating the encoding conversion for Excel files with Python at the point of data saving. This would involve developing an automated system that is compatible with my existing database structure. Another area for improvement is the temporal dimension of the database; the current lack of detailed time data limits my analysis, and I aim to focus more on temporal analysis going forward.

### As for the data limits, I've been concentrating on data processing to circumvent these issues, but I recognize that this isn't a long-term solution. I should start incorporating the elements I've been managing through processing directly into the SQL database. This project has provided me with significant learning, and I've found CS669 to be a course of great value.

---

Full SQL code:

```sql
ALTER TABLE Videos DROP CONSTRAINT DF__Videos__ShareCou__3D5E1FD2;
ALTER TABLE Videos DROP CONSTRAINT CK__Videos__ShareCou__3F466844;
ALTER TABLE Videos DROP CONSTRAINT FK__Videos__CreatorI__33D4B598;


ALTER TABLE Videos
DROP COLUMN CreatorID,
            ShareCount,
            Dislike,
            VideoLength,
            CreatorID;

ALTER TABLE Videos
DROP COLUMN VideoLength	
-- Select all records from Comments table
SELECT * FROM dbo.Comments;

-- Select all records from VideoCategories table
SELECT * FROM dbo.VideoCategories;

-- Select all records from VideoKeywordAssociation table
SELECT * FROM dbo.VideoKeywordAssociation;

-- Select all records from Videos table
SELECT * FROM dbo.Videos;

-- Select all records from Keywords table
SELECT * FROM dbo.Keywords;

-- Select all records from VideoCategories table
SELECT * FROM dbo.Users;

-- Delete the first row from VideoCategories table where CategoryID is 1
DELETE FROM dbo.VideoCategories WHERE CategoryID = 1;

-- Delete the first row from Videos table where VideoID is 101
DELETE FROM dbo.Videos WHERE VideoID = 101;

ALTER TABLE dbo.Users
ADD UserName varchar(255) NOT NULL;

-- Add CategoryID column to VideoTable
ALTER TABLE dbo.Videos
ADD CategoryID int;

-- Add foreign key constraint to VideoTable for CategoryID
ALTER TABLE dbo.Videos
ADD CONSTRAINT FK_Videos_VideoCategories
FOREIGN KEY (CategoryID)
REFERENCES dbo.VideoCategories (CategoryID);


-- Videos
CREATE TABLE Videos (
    VideoID VARCHAR(255) PRIMARY KEY,
    VideoTitle VARCHAR(255),
    Description VARCHAR(MAX), -- Changed from TEXT to VARCHAR(MAX)
    PublishTime DATETIME DEFAULT GETDATE(),
    Views INT CHECK (Views >= 0),
    Likes INT CHECK (Likes >= 0)
);

-- Video-Keyword Association
CREATE TABLE VideoKeywordAssociation (
    VideoID VARCHAR(255) FOREIGN KEY REFERENCES Videos(VideoID),
    KeywordID INT FOREIGN KEY REFERENCES Keywords(KeywordID),
    PRIMARY KEY (VideoID, KeywordID)
);

-- Comments
CREATE TABLE Comments (
    CommentID INT PRIMARY KEY,
    VideoID VARCHAR(255) FOREIGN KEY REFERENCES Videos(VideoID), -- Assuming reference to Videos(VideoID)
    [User] VARCHAR(255), -- Changed User to [User], added missing comma
    CommentText VARCHAR(MAX) -- Changed from TEXT to VARCHAR(MAX)
);

-- Users
CREATE TABLE Users ( -- Changed USER to Users
    Name VARCHAR(255) PRIMARY KEY, -- Changed TEXT to VARCHAR(255)
    Gender VARCHAR(255),
    User_Level INT
);

ALTER TABLE dbo.Comments
ADD CONSTRAINT FK_Comments_Users
FOREIGN KEY (UserID) REFERENCES dbo.Users(UserID);


BULK INSERT dbo.Users
FROM 'C:\Users\ASUS\Desktop\UserTable.csv'
WITH
(
    FIELDTERMINATOR = ',',  -- CSV字段分隔符
    ROWTERMINATOR = '\n',   -- CSV行分隔符
    FIRSTROW = 2,           -- 如果第一行是标题行则从第二行开始
    TABLOCK
);

BULK INSERT dbo.Keywords
FROM 'C:\Users\ASUS\Desktop\Keywordstable.csv'
WITH
(
    FIELDTERMINATOR = ',',  -- CSV字段分隔符
    ROWTERMINATOR = '\n',   -- CSV行分隔符
    FIRSTROW = 2,           -- 如果第一行是标题行则从第二行开始
    TABLOCK
);

BULK INSERT dbo.VideoCategories
FROM 'C:\Users\ASUS\Desktop\Cattable.csv'
WITH
(
    FIELDTERMINATOR = ',',  -- CSV字段分隔符
    ROWTERMINATOR = '\n',   -- CSV行分隔符
    FIRSTROW = 2,           -- 如果第一行是标题行则从第二行开始
    TABLOCK
);

BULK INSERT dbo.Videos
FROM 'C:\Users\ASUS\Desktop\VideoTable.csv'
WITH
(
    FIELDTERMINATOR = ',',  -- CSV字段分隔符
    ROWTERMINATOR = '\n',   -- CSV行分隔符
    FIRSTROW = 2,           -- 如果第一行是标题行则从第二行开始
    TABLOCK
);

BULK INSERT dbo.VideoKeywordAssociation
FROM 'C:\Users\ASUS\Desktop\Video-key word Association table.csv'
WITH
(
    FIELDTERMINATOR = ',',  -- CSV字段分隔符
    ROWTERMINATOR = '\n',   -- CSV行分隔符
    FIRSTROW = 2,           -- 如果第一行是标题行则从第二行开始
    TABLOCK
);


BULK INSERT dbo.Comments
FROM 'C:\Users\ASUS\Desktop\TABLE.csv'
WITH
(
    FIELDTERMINATOR = ',',  -- CSV字段分隔符
    ROWTERMINATOR = '\n',   -- CSV行分隔符
    FIRSTROW = 2,           -- 如果第一行是标题行则从第二行开始
    TABLOCK
);

DELETE FROM dbo.Keywords;

DELETE FROM dbo.VideoCategories;

ALTER TABLE dbo.VideoKeywordAssociation
ADD CONSTRAINT FK_VideoKeywordAssociation_Keywords
FOREIGN KEY (KeywordID) REFERENCES dbo.Keywords(KeywordID);


ALTER TABLE dbo.Keywords
ADD CONSTRAINT PK_Keywords PRIMARY KEY (KeywordID);

ALTER TABLE dbo.Videos
ADD CONSTRAINT FK_Videos_VideoCategories
FOREIGN KEY (CategoryID) REFERENCES dbo.VideoCategories(CategoryID);

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


SELECT TOP 10 vc.CategoryName, COUNT(*) as CommentCount
FROM dbo.Users u
JOIN dbo.Comments c ON u.UserID = c.UserID
JOIN dbo.Videos v ON c.VideoID = v.VideoID
JOIN dbo.VideoCategories vc ON v.CategoryID = vc.CategoryID
WHERE u.Gender = '男'
GROUP BY vc.CategoryName
ORDER BY CommentCount DESC;

SELECT Gender, COUNT(*) as UserCount FROM dbo.Users GROUP BY Gender;

SELECT TOP 10 k.KeywordName, COUNT(*) as KeywordCount
FROM dbo.VideoKeywordAssociation vka
JOIN dbo.Keywords k ON vka.KeywordID = k.KeywordID
GROUP BY k.KeywordName
ORDER BY KeywordCount DESC;

SELECT TOP 10 u.UserName, COUNT(*) as CommentCount
FROM dbo.Comments c
JOIN dbo.Users u ON c.UserID = u.UserID
GROUP BY u.UserName
ORDER BY CommentCount DESC;

```
