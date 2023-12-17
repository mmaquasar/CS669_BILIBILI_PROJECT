# Index

### 1. Project Overview
### 2. Create Tables
### 3. Constraints
### 4. Specialization-Generalization Relationship 
### 5. Crawler making - Data processing
### 6. stored procedures
### 7. normalizing-eliminate data redundancy
### 8. queries & conclusions

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

# 3.Constraints

### The constraints in my project primarily ensure data integrity and efficiency in the database. They include unique identifiers for key fields to prevent duplicates, character limits for text fields to maintain uniformity and storage efficiency, non-negative constraints on numerical fields to reflect logical data values, datetime fields with default values for consistency

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

---

# 4. Specialization-Generalization Relationship in Content Creator Analysis

## Subsetting Definition for Content Creators:
To understand the diversity within YouTube's content creator community, we're applying a specialization-generalization relationship to categorize creators into distinct subsets, each with unique attributes.

## Characteristics of the Subsets:

### 1. Independent Creators:
- **Independent Channel Name**: The personal or brand name of the creator.
- **Personal Background**: Details like education, hobbies, signifying individual or small team operations.
Independent creators function without formal ties to larger entities, focusing on autonomous content production.

### 2. Official Brand Channels:
- **Brand Name**: The official channel name tied to a corporate entity.
- **Official Contact Information**: Direct communication channels like email, phone numbers, etc.
Brand channels represent structured organizations with dedicated content production and management teams.

---


