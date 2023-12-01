# HW5

## 1. Introduction

#### In this assigment, you will practice your skills in both SQL and Regex. The scenario involves user activity logs and user comments. The goal is to perform data analysis tasks using SQL queries and extract relevant information from user comments using Regex.

## 2. SQL

The file user_actions.db in the data repo contains an SQLite database. In the database there is a table named user_actions. Analyse the table and solve the following tasks.

1. Retrieve the usernames of all users who have performed the "signup" action.
2. Find the total number of log entries for each user. Display the user_id, username, and the count of log entries.
3. Identify users who have both logged in (action = 'login') and signed up (action = 'signup') on the same day. Display the user_id and username.

Each task should be solved only using SQL.



### load and explore the database


```python
import sqlite3
import pandas as pd

# Connect to the SQLite database
db_path = '/home/xyc/Downloads/hw5/user_actions.db'
conn = sqlite3.connect(db_path)

# Query to list the tables in the database
query = "SELECT name FROM sqlite_master WHERE type='table';"

# Execute the query and display the tables
tables = pd.read_sql_query(query, conn)
tables

# Query to get the first few rows from the 'user_actions' table to understand its structure and content
query = "SELECT * FROM user_actions LIMIT 5;"

# Execute the query and display the results
user_actions_preview = pd.read_sql_query(query, conn)
user_actions_preview

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>user_id</th>
      <th>username</th>
      <th>email</th>
      <th>action</th>
      <th>timestamp</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>34</td>
      <td>user34</td>
      <td>user34@email.com</td>
      <td>signup</td>
      <td>2015-02-04 14:38:47</td>
    </tr>
    <tr>
      <th>1</th>
      <td>28</td>
      <td>user28</td>
      <td>user28@email.com</td>
      <td>signup</td>
      <td>2015-03-09 11:55:33</td>
    </tr>
    <tr>
      <th>2</th>
      <td>27</td>
      <td>user27</td>
      <td>user27@email.com</td>
      <td>login</td>
      <td>2015-04-17 14:48:31</td>
    </tr>
    <tr>
      <th>3</th>
      <td>27</td>
      <td>user27</td>
      <td>user27@email.com</td>
      <td>login</td>
      <td>2015-04-21 13:22:14</td>
    </tr>
    <tr>
      <th>4</th>
      <td>27</td>
      <td>user27</td>
      <td>user27@email.com</td>
      <td>reset_password</td>
      <td>2015-04-25 16:30:15</td>
    </tr>
  </tbody>
</table>
</div>



### Part 1 : Retrieve the usernames of all users who have performed the "signup" action.


```python
# SQL query to retrieve the usernames of all users who have performed the "signup" action
signup_users_query = """
SELECT DISTINCT username
FROM user_actions
WHERE action = 'signup';
"""

# Execute the query and display the results
signup_users = pd.read_sql_query(signup_users_query, conn)
signup_users

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>username</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>user34</td>
    </tr>
    <tr>
      <th>1</th>
      <td>user28</td>
    </tr>
    <tr>
      <th>2</th>
      <td>user1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>user24</td>
    </tr>
    <tr>
      <th>4</th>
      <td>user15</td>
    </tr>
    <tr>
      <th>5</th>
      <td>user20</td>
    </tr>
    <tr>
      <th>6</th>
      <td>user18</td>
    </tr>
    <tr>
      <th>7</th>
      <td>user25</td>
    </tr>
    <tr>
      <th>8</th>
      <td>user3</td>
    </tr>
    <tr>
      <th>9</th>
      <td>user9</td>
    </tr>
    <tr>
      <th>10</th>
      <td>user27</td>
    </tr>
    <tr>
      <th>11</th>
      <td>user16</td>
    </tr>
    <tr>
      <th>12</th>
      <td>user17</td>
    </tr>
    <tr>
      <th>13</th>
      <td>user4</td>
    </tr>
    <tr>
      <th>14</th>
      <td>user8</td>
    </tr>
    <tr>
      <th>15</th>
      <td>user13</td>
    </tr>
    <tr>
      <th>16</th>
      <td>user19</td>
    </tr>
    <tr>
      <th>17</th>
      <td>user31</td>
    </tr>
    <tr>
      <th>18</th>
      <td>user10</td>
    </tr>
    <tr>
      <th>19</th>
      <td>user23</td>
    </tr>
    <tr>
      <th>20</th>
      <td>user11</td>
    </tr>
    <tr>
      <th>21</th>
      <td>user33</td>
    </tr>
    <tr>
      <th>22</th>
      <td>user12</td>
    </tr>
    <tr>
      <th>23</th>
      <td>user29</td>
    </tr>
    <tr>
      <th>24</th>
      <td>user21</td>
    </tr>
    <tr>
      <th>25</th>
      <td>user6</td>
    </tr>
    <tr>
      <th>26</th>
      <td>user14</td>
    </tr>
    <tr>
      <th>27</th>
      <td>user30</td>
    </tr>
    <tr>
      <th>28</th>
      <td>user7</td>
    </tr>
    <tr>
      <th>29</th>
      <td>user26</td>
    </tr>
    <tr>
      <th>30</th>
      <td>user22</td>
    </tr>
    <tr>
      <th>31</th>
      <td>user5</td>
    </tr>
    <tr>
      <th>32</th>
      <td>user35</td>
    </tr>
    <tr>
      <th>33</th>
      <td>user2</td>
    </tr>
    <tr>
      <th>34</th>
      <td>user32</td>
    </tr>
  </tbody>
</table>
</div>



### part 2 : Find the total number of log entries for each user. Display the user_id, username, and the count of log entries.


```python
# SQL query to find the total number of log entries for each user
query = """
SELECT user_id, username, COUNT(*) AS log_entries_count
FROM user_actions
GROUP BY user_id, username;
"""

# Execute the query and display the results
log_entries_per_user = pd.read_sql_query(query, conn)
log_entries_per_user.head()  # Displaying the first few rows of the result

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>user_id</th>
      <th>username</th>
      <th>log_entries_count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>user1</td>
      <td>104</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>user2</td>
      <td>149</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>user3</td>
      <td>108</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>user4</td>
      <td>436</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>user5</td>
      <td>192</td>
    </tr>
  </tbody>
</table>
</div>



### part 3 ： Identify users who have both logged in (action = 'login') and signed up (action = 'signup') on the same day. Display the user_id and username.


```python
# SQL query to find users who have both logged in and signed up on the same day

query = """
SELECT DISTINCT a.user_id, a.username
FROM user_actions a
JOIN user_actions b ON a.user_id = b.user_id
WHERE a.action = 'signup' 
      AND b.action = 'login' 
      AND DATE(a.timestamp) = DATE(b.timestamp);
"""

# Execute the query and display the results
users_logged_and_signed_same_day = pd.read_sql_query(query, conn)
users_logged_and_signed_same_day

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>user_id</th>
      <th>username</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>8</td>
      <td>user8</td>
    </tr>
    <tr>
      <th>1</th>
      <td>12</td>
      <td>user12</td>
    </tr>
    <tr>
      <th>2</th>
      <td>30</td>
      <td>user30</td>
    </tr>
    <tr>
      <th>3</th>
      <td>7</td>
      <td>user7</td>
    </tr>
    <tr>
      <th>4</th>
      <td>22</td>
      <td>user22</td>
    </tr>
    <tr>
      <th>5</th>
      <td>5</td>
      <td>user5</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2</td>
      <td>user2</td>
    </tr>
    <tr>
      <th>7</th>
      <td>32</td>
      <td>user32</td>
    </tr>
  </tbody>
</table>
</div>




```python

```

## 3. Regex

The file comments.txt in the data repo contains lines of text, each representing a user comment. Users sometimes include tags in their comments using the format "#tag". Analyse the file and solve the following tasks.

1. Write a regular expression to extract all hashtags from a given comment. For example, applying the regex to comment 1 should return ["#programming", "#tips"].
2. Create a regular expression to find comments that mention both "#programming" and "#python". Apply the regex to comment 2 and check if it matches.
3. Using your regular expression, extract all unique hashtags from the entire text file. (*)

The last task is optional (*)!

### load and explore the database


```python
# Reading the contents of the comments.txt file
comments_file_path = '/home/xyc/Downloads/hw5/comments.txt'

with open(comments_file_path, 'r') as file:
    comments = file.readlines()

# Displaying the first few comments for reference
comments[:5]

```




    ['1. "Great post! #programming #tips"\n',
     '2. "Enjoyed the article. #coding #python"\n',
     '3. "Interesting insights. #tech #data"\n',
     '4. "This is awesome! #programming #coding"\n',
     '5. "Thanks for sharing. #data #analysis"\n']



### Task 1: Extract Hashtags from a Comment
To extract hashtags, we can use a regular expression that finds patterns starting with "#" followed by alphanumeric characters and underscores (a common pattern for hashtags)


```python
import re

# Regular expression to extract hashtags
hashtag_regex = r"#\w+"

# Extracting hashtags from the first comment
first_comment = comments[0]
hashtags_in_first_comment = re.findall(hashtag_regex, first_comment)

hashtags_in_first_comment

```




    ['#programming', '#tips']



### Task 2: Identify Comments with Specific Tags
we'll create a regular expression to find comments that include both "#programming" and "#python". 

This regex needs to account for the presence of both tags in any order. 


```python
# Regular expression to find comments with both "#programming" and "#python"
programming_python_regex = r"(?=.*#programming)(?=.*#python)"

# Checking if the second comment matches this pattern
second_comment = comments[1]
match_in_second_comment = re.search(programming_python_regex, second_comment, re.IGNORECASE)

bool(match_in_second_comment)  # Converting the match object to a boolean to see if there is a match

```




    False



The regular expression did not find a match in the second comment for both "#programming" and "#python". 

This indicates that the second comment does not mention both of these hashtags.

### Task 3: Extract All Unique Hashtags

For the final task, we will use the previously defined regex for hashtags to extract all unique hashtags from the entire file.


```python
# Extracting all hashtags from the entire file
all_hashtags = re.findall(hashtag_regex, ' '.join(comments))

# Getting unique hashtags
unique_hashtags = set(all_hashtags)

unique_hashtags

```




    {'#analysis',
     '#analytics',
     '#coding',
     '#data',
     '#innovation',
     '#insights',
     '#programming',
     '#python',
     '#research',
     '#tech',
     '#tips'}



These are the distinct hashtags found across all user comments in the comments.txt file. 


```python

```
