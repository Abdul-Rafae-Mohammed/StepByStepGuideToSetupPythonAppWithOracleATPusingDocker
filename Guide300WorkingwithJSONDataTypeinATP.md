<table class="tbl-heading"><tr><td class="td-logo">[![](images/obe_tag.png)](README.md)

Last Updated:<br>November 06, 2019
</td>
<td class="td-banner">
# Guide 3: Working with JSON datatype in Oracle Autonomous Database
</td></tr><table>

## Introduction

Oracle database 18c and above natively supports JSON datatype, which means that users can directly work with JSON data like any other primary datatypes in Oracle database.

Oracle provides native PL/SQL utilities to read and write data from or to a JSON object, so users dont have to worry about parsing and extracting the data from the JSON objects before they can actually use it. they can write simple select statements to extract data and generate results for their reporting purposes or any other workloads.

To **log issues**, click [here](https://github.com/Abdul-Rafae-Mohammed/StepByStepGuideToSetupPythonAppWithOracleATP/issues/new) to go to the github oracle repository issue submission form.

## Objectives

- Learn how to work with JSON data in Oracle Database.

- Learn PL/SQL utilities that Oracle provides to work with JSON data.

## Required Artifacts

- SQL Developer or any SQL Client client to execute scripts.

- Execute guide 1 and 2 before you execute this guide.

## Steps

### **Preview and Analyze the JSON Data**

- Connect to the database using any SQL Client. In this guide , we are using SQL developer.

---########################################################################################################
--For this exercise, we have collected tweets based on the keyword 'IPL'.  
--IPL is a cricket tournament which is conducted every year in India, 
--where players from various countries participate and play for their franchises.
--########################################################################################################

--########################################################################################################
--IPL conducted an auction of players recently. 
--We have collected tweets regarding the auction and will now derive some insights about the IPL and the auction conducted.
--########################################################################################################

--##############################################
--Lets have a peek at the Twitter Data we have!!
--##############################################

```
select * from user_tables;
```

--#########################################################
--Lets have a look at the tweets which we have collected!!
--#########################################################

```
select * from jsontweets order by ts;
```

--##############################################
--The tweets are currently in JSON Format.
--##############################################

```
select tweetjson from jsontweets order by ts desc;
```

--##############################################
--Now...we will
--##############################################
 
--##################################################################################
--Now, we are going to perform some basic data analysis on tweets ....

-- The first query gets the details about all the tweets collected and gives a deep dive view about the tweet data.

-- The second query retrieves details about the users who tweeted about IPL and gives us more details about tweeters.
--##################################################################################


```
 select jt.tweet_id, jt.tweet, jt.username, jt.source, jt.place, jt.retweet_count, jt.retweeted, jt.language_used, jt.tweet_time
 from JSONtweets,
   json_table( tweetJSON, '$' 
     columns( tweet_id NUMBER(38) path '$.id', tweet VARCHAR2(3000) path '$.text', username VARCHAR2(512) path '$.user.name', 
              source VARCHAR2(1000) path '$.source', place VARCHAR2(512) path '$.place', retweet_count NUMBER(38) path '$.retweet_count', 
              retweeted VARCHAR2(38) path '$.retweeted', language_used varchar2(256) path '$.lang', tweet_time TIMESTAMP path '$.timestamp_ms')
            ) jt;
/  
```

```
 select jt.user_id, jt.username, jt.screen_name, jt.user_location, jt.verified, jt.followers_count, jt.friends_count, 
 jt.favourites_count, jt.statuses_count, jt.geo_enabled
 from JSONtweets,
 json_table( tweetJSON, '$' 
 columns( user_id NUMBER(38) path '$.user.id', username VARCHAR2(512) path '$.user.name', 
 screen_name VARCHAR2(512) path '$.user.screen_name', user_location VARCHAR2(512) path '$.user.location', 
 verified VARCHAR2(512) path '$.user.verified', followers_count NUMBER(38) path '$.user.followers_count', 
 friends_count NUMBER(38) path '$.user.friends_count', favourites_count NUMBER(38) path '$.user.favourites_count',
 statuses_count NUMBER(38) path '$.user.statuses_count',geo_enabled VARCHAR2(5) path '$.user.geo_enabled')
        ) jt;
/
```


- You have now worked on processing and analyzing the JSON data in Oracle Database. As you can see it is very easy to work with JSON data as it is natively supported by Oracle Database.


You have now successfully completed this workshop.

<table>
<tr><td class="td-logo">[![]()](#)</td>
<td class="td-banner">
## Great Work - All Done!
</td>
</tr>
<table>