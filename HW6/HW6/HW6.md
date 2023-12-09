# HW6

# Instructions

In this assigment, you will practice your skills in retrieving data from the internet, using REST APIs and web scraping.

Make sure of the following:

1. Your solutions should be in form of a report in .md format.
2. You have documented your procedure properly.
3. Your answers are clear and concise.

When you are finished push you results to Github and raise an issue, just as you have done in previous homeworks. To pass the homework you will have to complete the assigments below and also finish the peer-review.

## REST API


```python
import nltk
from nltk.corpus import stopwords
from wordcloud import WordCloud
import matplotlib.pyplot as plt
import numpy as np
from PIL import Image
```

### Step 1: Fetch Data from the Nobel Prize API
We are using the requests library to make a GET request to the Nobel Prize API.


```python
api_url = "http://api.nobelprize.org/2.0/nobelPrizes?yearTo=2023&nobelPrizeCategory=phy&format=json"
response = requests.get(api_url)
data = response.json()

# Check if data is retrieved
#print("Data Retrieved:", data)

```

### Step 2: Parse and Extract Data
We iterate through the JSON response to extract the 'motivation' field from each laureate.


```python
motivations = []
for prize in data['nobelPrizes']:
    if 'laureates' in prize:
        for laureate in prize['laureates']:
            if 'motivation' in laureate:
                motivations.append(laureate['motivation']['en'])

# Check if motivations are extracted
#print("Extracted Motivations:", motivations)
```

### Step 3: Process Text and Create a Word Cloud
First, we download and set up the list of stopwords (commonly used words that are not meaningful).

Then combine all motivations into a single text string, generate a word cloud image from the text.


```python
# Download stopwords
nltk.download('stopwords')

# Set up stop words
stop_words = set(stopwords.words('english'))

# Combine all motivations into a single text string
text = ' '.join(motivations)

# Customize the word cloud
wordcloud = WordCloud(
    width = 800,  # Width of the canvas
    height = 400,  # Height of the canvas
    background_color = 'white',  # Background color
    max_words = 200,  # Maximum number of words
    stopwords = stop_words,  # Stopwords to exclude
    contour_width = 1,  # Width of the contour
    contour_color = 'steelblue',  # Color of the contour
    colormap = 'viridis',  # Color scheme for the words
    # Uncomment the following line if you have a mask image
    # mask = np.array(Image.open('path/to/your/mask/image.png')),
    min_font_size = 10,  # Minimum font size
    max_font_size = 100,  # Maximum font size
    random_state = 42  # Ensures layout is the same each time
).generate(text)

```

    [nltk_data] Downloading package stopwords to /home/xyc/nltk_data...
    [nltk_data]   Package stopwords is already up-to-date!


### Step 4: Visualize
Display the generated word cloud using matplotlib.


```python
# Display the word cloud
plt.figure(figsize=(10, 5))  # Size of the figure
plt.imshow(wordcloud, interpolation='bilinear')
plt.axis('off')
plt.show()
```


    
![png](output_11_0.png)
    


## Web Scraping


### part 1: Imports and Function Definitions
This part is used to scrape data from the 'Books to Scrape' website. We will extract details about books across multiple pages, including each book's UPC, title, price, and rating.


```python
import requests
from bs4 import BeautifulSoup
import pandas as pd

# Function to get book details from its individual page
def get_book_details(book_url):
    response = requests.get(book_url)
    soup = BeautifulSoup(response.text, 'html.parser')
    product_info = soup.find('table', class_='table table-striped').find_all('tr')
    details = {row.find('th').text: row.find('td').text for row in product_info}
    return details
```

### part 2: Setup URL and Data Storage
We define the base URLs for the book listings and for individual book pages. We also initialize an empty list to store the data we will scrape.


```python
# Base URL of the site to scrape
base_url = "https://books.toscrape.com/catalogue/page-{}.html"
book_base_url = "https://books.toscrape.com/catalogue/"

# List to store book data
books_data = []
```

### part 3: Web Scraping Logic
We will iterate through the first three pages of the book listings. For each book on a page, we will scrape the title, price, and rating. We will also construct the URL for each book's individual page and call `get_book_details` to scrape the UPC.


```python
# Loop through the first three pages
for page in range(1, 4):
    # Fetch the content of the page
    response = requests.get(base_url.format(page))
    soup = BeautifulSoup(response.text, 'html.parser')
    
    # Find the list of books on the current page
    books = soup.find_all('article', class_='product_pod')
    
    # For each book, extract the required information
    for book in books:
        title = book.h3.a['title']
        relative_url = book.h3.a['href']
        book_url = f"https://books.toscrape.com/catalogue/{relative_url.split('/')[0]}"
        details = get_book_details(book_url)
        
        upc = details['UPC']
        price = details['Price (excl. tax)']
        price = '£' + price  # Add pound symbol
        rating = book.p['class'][-1]
        rating = rating.capitalize()  # Capitalize rating
        
        books_data.append({'upc': upc, 'title': title, 'price': price, 'rating': rating})
```

### part 4: Data Cleaning and Display
After scraping, we will put the data into a pandas DataFrame to organize and display it. We will also clean the data by correcting the encoding of the price symbol.


```python
# Convert the list of dictionaries to a DataFrame
books_df = pd.DataFrame(books_data)

# Correct any encoding issues for the price
books_df['price'] = books_df['price'].str.replace('Â£', '£')

# Display the DataFrame
books_df
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
      <th>upc</th>
      <th>title</th>
      <th>price</th>
      <th>rating</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>a897fe39b1053632</td>
      <td>A Light in the Attic</td>
      <td>££51.77</td>
      <td>Three</td>
    </tr>
    <tr>
      <th>1</th>
      <td>90fa61229261140a</td>
      <td>Tipping the Velvet</td>
      <td>££53.74</td>
      <td>One</td>
    </tr>
    <tr>
      <th>2</th>
      <td>6957f44c3847a760</td>
      <td>Soumission</td>
      <td>££50.10</td>
      <td>One</td>
    </tr>
    <tr>
      <th>3</th>
      <td>e00eb4fd7b871a48</td>
      <td>Sharp Objects</td>
      <td>££47.82</td>
      <td>Four</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4165285e1663650f</td>
      <td>Sapiens: A Brief History of Humankind</td>
      <td>££54.23</td>
      <td>Five</td>
    </tr>
    <tr>
      <th>5</th>
      <td>f77dbf2323deb740</td>
      <td>The Requiem Red</td>
      <td>££22.65</td>
      <td>One</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2597b5a345f45e1b</td>
      <td>The Dirty Little Secrets of Getting Your Dream...</td>
      <td>££33.34</td>
      <td>Four</td>
    </tr>
    <tr>
      <th>7</th>
      <td>e72a5dfc7e9267b2</td>
      <td>The Coming Woman: A Novel Based on the Life of...</td>
      <td>££17.93</td>
      <td>Three</td>
    </tr>
    <tr>
      <th>8</th>
      <td>e10e1e165dc8be4a</td>
      <td>The Boys in the Boat: Nine Americans and Their...</td>
      <td>££22.60</td>
      <td>Four</td>
    </tr>
    <tr>
      <th>9</th>
      <td>1dfe412b8ac00530</td>
      <td>The Black Maria</td>
      <td>££52.15</td>
      <td>One</td>
    </tr>
    <tr>
      <th>10</th>
      <td>0312262ecafa5a40</td>
      <td>Starving Hearts (Triangular Trade Trilogy, #1)</td>
      <td>££13.99</td>
      <td>Two</td>
    </tr>
    <tr>
      <th>11</th>
      <td>30a7f60cd76ca58c</td>
      <td>Shakespeare's Sonnets</td>
      <td>££20.66</td>
      <td>Four</td>
    </tr>
    <tr>
      <th>12</th>
      <td>ce6396b0f23f6ecc</td>
      <td>Set Me Free</td>
      <td>££17.46</td>
      <td>Five</td>
    </tr>
    <tr>
      <th>13</th>
      <td>3b1c02bac2a429e6</td>
      <td>Scott Pilgrim's Precious Little Life (Scott Pi...</td>
      <td>££52.29</td>
      <td>Five</td>
    </tr>
    <tr>
      <th>14</th>
      <td>a34ba96d4081e6a4</td>
      <td>Rip it Up and Start Again</td>
      <td>££35.02</td>
      <td>Five</td>
    </tr>
    <tr>
      <th>15</th>
      <td>deda3e61b9514b83</td>
      <td>Our Band Could Be Your Life: Scenes from the A...</td>
      <td>££57.25</td>
      <td>Three</td>
    </tr>
    <tr>
      <th>16</th>
      <td>feb7cc7701ecf901</td>
      <td>Olio</td>
      <td>££23.88</td>
      <td>One</td>
    </tr>
    <tr>
      <th>17</th>
      <td>e30f54cea9b38190</td>
      <td>Mesaerion: The Best Science Fiction Stories 18...</td>
      <td>££37.59</td>
      <td>One</td>
    </tr>
    <tr>
      <th>18</th>
      <td>a18a4f574854aced</td>
      <td>Libertarianism for Beginners</td>
      <td>££51.33</td>
      <td>Two</td>
    </tr>
    <tr>
      <th>19</th>
      <td>a22124811bfa8350</td>
      <td>It's Only the Himalayas</td>
      <td>££45.17</td>
      <td>Two</td>
    </tr>
    <tr>
      <th>20</th>
      <td>23356462d1320d61</td>
      <td>In Her Wake</td>
      <td>££12.84</td>
      <td>One</td>
    </tr>
    <tr>
      <th>21</th>
      <td>327f68a59745c102</td>
      <td>How Music Works</td>
      <td>££37.32</td>
      <td>Two</td>
    </tr>
    <tr>
      <th>22</th>
      <td>5674a18a29a43ced</td>
      <td>Foolproof Preserving: A Guide to Small Batch J...</td>
      <td>££30.52</td>
      <td>Three</td>
    </tr>
    <tr>
      <th>23</th>
      <td>c2e46a2ee3b4a322</td>
      <td>Chase Me (Paris Nights #2)</td>
      <td>££25.27</td>
      <td>Five</td>
    </tr>
    <tr>
      <th>24</th>
      <td>00bfed9e18bb36f3</td>
      <td>Black Dust</td>
      <td>££34.53</td>
      <td>Five</td>
    </tr>
    <tr>
      <th>25</th>
      <td>9528d0948525bf5f</td>
      <td>Birdsong: A Story in Pictures</td>
      <td>££54.64</td>
      <td>Three</td>
    </tr>
    <tr>
      <th>26</th>
      <td>c7d160c2c0de586f</td>
      <td>America's Cradle of Quarterbacks: Western Penn...</td>
      <td>££22.50</td>
      <td>Three</td>
    </tr>
    <tr>
      <th>27</th>
      <td>904208d6aa64b655</td>
      <td>Aladdin and His Wonderful Lamp</td>
      <td>££53.13</td>
      <td>Three</td>
    </tr>
    <tr>
      <th>28</th>
      <td>4c28def39d850cdf</td>
      <td>Worlds Elsewhere: Journeys Around Shakespeareâ...</td>
      <td>££40.30</td>
      <td>Five</td>
    </tr>
    <tr>
      <th>29</th>
      <td>ccd9ffa25efabdea</td>
      <td>Wall and Piece</td>
      <td>££44.18</td>
      <td>Four</td>
    </tr>
    <tr>
      <th>30</th>
      <td>6258a1f6a6dcfe50</td>
      <td>The Four Agreements: A Practical Guide to Pers...</td>
      <td>££17.66</td>
      <td>Five</td>
    </tr>
    <tr>
      <th>31</th>
      <td>3c039985229453bf</td>
      <td>The Five Love Languages: How to Express Heartf...</td>
      <td>££31.05</td>
      <td>Three</td>
    </tr>
    <tr>
      <th>32</th>
      <td>5dada2b7be26bd03</td>
      <td>The Elephant Tree</td>
      <td>££23.82</td>
      <td>Five</td>
    </tr>
    <tr>
      <th>33</th>
      <td>9f6568e9c95f60b0</td>
      <td>The Bear and the Piano</td>
      <td>££36.89</td>
      <td>One</td>
    </tr>
    <tr>
      <th>34</th>
      <td>6be3beb0793a53e7</td>
      <td>Sophie's World</td>
      <td>££15.94</td>
      <td>Five</td>
    </tr>
    <tr>
      <th>35</th>
      <td>668fe56b17cfcd4f</td>
      <td>Penny Maybe</td>
      <td>££33.29</td>
      <td>Three</td>
    </tr>
    <tr>
      <th>36</th>
      <td>094b269567e1c300</td>
      <td>Maude (1883-1993):She Grew Up with the country</td>
      <td>££18.02</td>
      <td>Two</td>
    </tr>
    <tr>
      <th>37</th>
      <td>19ed25f4641d5efd</td>
      <td>In a Dark, Dark Wood</td>
      <td>££19.63</td>
      <td>One</td>
    </tr>
    <tr>
      <th>38</th>
      <td>be5cc846f45496fb</td>
      <td>Behind Closed Doors</td>
      <td>££52.22</td>
      <td>Four</td>
    </tr>
    <tr>
      <th>39</th>
      <td>55f9da0c5eea2e10</td>
      <td>You can't bury them all: Poems</td>
      <td>££33.63</td>
      <td>Two</td>
    </tr>
    <tr>
      <th>40</th>
      <td>b4fd5943413e089a</td>
      <td>Slow States of Collapse: Poems</td>
      <td>££57.31</td>
      <td>Three</td>
    </tr>
    <tr>
      <th>41</th>
      <td>c8f7f0cb1abb9cac</td>
      <td>Reasons to Stay Alive</td>
      <td>££26.41</td>
      <td>Two</td>
    </tr>
    <tr>
      <th>42</th>
      <td>b12b89017878a60d</td>
      <td>Private Paris (Private #10)</td>
      <td>££47.61</td>
      <td>Five</td>
    </tr>
    <tr>
      <th>43</th>
      <td>c27f6e1f185b0383</td>
      <td>#HigherSelfie: Wake Up Your Life. Free Your So...</td>
      <td>££23.11</td>
      <td>Five</td>
    </tr>
    <tr>
      <th>44</th>
      <td>acddfeab2a407640</td>
      <td>Without Borders (Wanderlove #1)</td>
      <td>££45.07</td>
      <td>Two</td>
    </tr>
    <tr>
      <th>45</th>
      <td>3ecb690d1542c568</td>
      <td>When We Collided</td>
      <td>££31.77</td>
      <td>One</td>
    </tr>
    <tr>
      <th>46</th>
      <td>8d455c7539795d2a</td>
      <td>We Love You, Charlie Freeman</td>
      <td>££50.27</td>
      <td>Five</td>
    </tr>
    <tr>
      <th>47</th>
      <td>657fe5ead67a7767</td>
      <td>Untitled Collection: Sabbath Poems 2014</td>
      <td>££14.27</td>
      <td>Four</td>
    </tr>
    <tr>
      <th>48</th>
      <td>d170b5a0da46380e</td>
      <td>Unseen City: The Majesty of Pigeons, the Discr...</td>
      <td>££44.18</td>
      <td>Four</td>
    </tr>
    <tr>
      <th>49</th>
      <td>7ae099f3898e0209</td>
      <td>Unicorn Tracks</td>
      <td>££18.78</td>
      <td>Three</td>
    </tr>
    <tr>
      <th>50</th>
      <td>9a9d6e9bc555731e</td>
      <td>Unbound: How Eight Technologies Made Us Human,...</td>
      <td>££25.52</td>
      <td>One</td>
    </tr>
    <tr>
      <th>51</th>
      <td>09555be379ad9253</td>
      <td>Tsubasa: WoRLD CHRoNiCLE 2 (Tsubasa WoRLD CHRo...</td>
      <td>££16.28</td>
      <td>One</td>
    </tr>
    <tr>
      <th>52</th>
      <td>7858914fad68493c</td>
      <td>Throwing Rocks at the Google Bus: How Growth B...</td>
      <td>££31.12</td>
      <td>Three</td>
    </tr>
    <tr>
      <th>53</th>
      <td>51653ef291ab7ddc</td>
      <td>This One Summer</td>
      <td>££19.49</td>
      <td>Four</td>
    </tr>
    <tr>
      <th>54</th>
      <td>709822d0b5bcb7f4</td>
      <td>Thirst</td>
      <td>££17.27</td>
      <td>Five</td>
    </tr>
    <tr>
      <th>55</th>
      <td>4eed62cf5f8d8edf</td>
      <td>The Torch Is Passed: A Harding Family Story</td>
      <td>££19.09</td>
      <td>One</td>
    </tr>
    <tr>
      <th>56</th>
      <td>b5ea0b5dabed25a8</td>
      <td>The Secret of Dreadwillow Carse</td>
      <td>££56.13</td>
      <td>One</td>
    </tr>
    <tr>
      <th>57</th>
      <td>142d767a56a9254d</td>
      <td>The Pioneer Woman Cooks: Dinnertime: Comfort C...</td>
      <td>££56.41</td>
      <td>One</td>
    </tr>
    <tr>
      <th>58</th>
      <td>5ee94540d0749ea0</td>
      <td>The Past Never Ends</td>
      <td>££56.50</td>
      <td>Four</td>
    </tr>
    <tr>
      <th>59</th>
      <td>cedf82b5086e4691</td>
      <td>The Natural History of Us (The Fine Art of Pre...</td>
      <td>££45.22</td>
      <td>Three</td>
    </tr>
  </tbody>
</table>
</div>



### part 5: Styling (Optional)


```python
# Uncomment below lines if running in Jupyter Notebook to apply styles
# This cell should only be run if you're using Jupyter Notebook to view the DataFrame
books_df.style.set_properties(**{
    'background-color': 'black',
    'color': 'lawngreen',
    'border-color': 'white'
})
```




<style type="text/css">
#T_10e24_row0_col0, #T_10e24_row0_col1, #T_10e24_row0_col2, #T_10e24_row0_col3, #T_10e24_row1_col0, #T_10e24_row1_col1, #T_10e24_row1_col2, #T_10e24_row1_col3, #T_10e24_row2_col0, #T_10e24_row2_col1, #T_10e24_row2_col2, #T_10e24_row2_col3, #T_10e24_row3_col0, #T_10e24_row3_col1, #T_10e24_row3_col2, #T_10e24_row3_col3, #T_10e24_row4_col0, #T_10e24_row4_col1, #T_10e24_row4_col2, #T_10e24_row4_col3, #T_10e24_row5_col0, #T_10e24_row5_col1, #T_10e24_row5_col2, #T_10e24_row5_col3, #T_10e24_row6_col0, #T_10e24_row6_col1, #T_10e24_row6_col2, #T_10e24_row6_col3, #T_10e24_row7_col0, #T_10e24_row7_col1, #T_10e24_row7_col2, #T_10e24_row7_col3, #T_10e24_row8_col0, #T_10e24_row8_col1, #T_10e24_row8_col2, #T_10e24_row8_col3, #T_10e24_row9_col0, #T_10e24_row9_col1, #T_10e24_row9_col2, #T_10e24_row9_col3, #T_10e24_row10_col0, #T_10e24_row10_col1, #T_10e24_row10_col2, #T_10e24_row10_col3, #T_10e24_row11_col0, #T_10e24_row11_col1, #T_10e24_row11_col2, #T_10e24_row11_col3, #T_10e24_row12_col0, #T_10e24_row12_col1, #T_10e24_row12_col2, #T_10e24_row12_col3, #T_10e24_row13_col0, #T_10e24_row13_col1, #T_10e24_row13_col2, #T_10e24_row13_col3, #T_10e24_row14_col0, #T_10e24_row14_col1, #T_10e24_row14_col2, #T_10e24_row14_col3, #T_10e24_row15_col0, #T_10e24_row15_col1, #T_10e24_row15_col2, #T_10e24_row15_col3, #T_10e24_row16_col0, #T_10e24_row16_col1, #T_10e24_row16_col2, #T_10e24_row16_col3, #T_10e24_row17_col0, #T_10e24_row17_col1, #T_10e24_row17_col2, #T_10e24_row17_col3, #T_10e24_row18_col0, #T_10e24_row18_col1, #T_10e24_row18_col2, #T_10e24_row18_col3, #T_10e24_row19_col0, #T_10e24_row19_col1, #T_10e24_row19_col2, #T_10e24_row19_col3, #T_10e24_row20_col0, #T_10e24_row20_col1, #T_10e24_row20_col2, #T_10e24_row20_col3, #T_10e24_row21_col0, #T_10e24_row21_col1, #T_10e24_row21_col2, #T_10e24_row21_col3, #T_10e24_row22_col0, #T_10e24_row22_col1, #T_10e24_row22_col2, #T_10e24_row22_col3, #T_10e24_row23_col0, #T_10e24_row23_col1, #T_10e24_row23_col2, #T_10e24_row23_col3, #T_10e24_row24_col0, #T_10e24_row24_col1, #T_10e24_row24_col2, #T_10e24_row24_col3, #T_10e24_row25_col0, #T_10e24_row25_col1, #T_10e24_row25_col2, #T_10e24_row25_col3, #T_10e24_row26_col0, #T_10e24_row26_col1, #T_10e24_row26_col2, #T_10e24_row26_col3, #T_10e24_row27_col0, #T_10e24_row27_col1, #T_10e24_row27_col2, #T_10e24_row27_col3, #T_10e24_row28_col0, #T_10e24_row28_col1, #T_10e24_row28_col2, #T_10e24_row28_col3, #T_10e24_row29_col0, #T_10e24_row29_col1, #T_10e24_row29_col2, #T_10e24_row29_col3, #T_10e24_row30_col0, #T_10e24_row30_col1, #T_10e24_row30_col2, #T_10e24_row30_col3, #T_10e24_row31_col0, #T_10e24_row31_col1, #T_10e24_row31_col2, #T_10e24_row31_col3, #T_10e24_row32_col0, #T_10e24_row32_col1, #T_10e24_row32_col2, #T_10e24_row32_col3, #T_10e24_row33_col0, #T_10e24_row33_col1, #T_10e24_row33_col2, #T_10e24_row33_col3, #T_10e24_row34_col0, #T_10e24_row34_col1, #T_10e24_row34_col2, #T_10e24_row34_col3, #T_10e24_row35_col0, #T_10e24_row35_col1, #T_10e24_row35_col2, #T_10e24_row35_col3, #T_10e24_row36_col0, #T_10e24_row36_col1, #T_10e24_row36_col2, #T_10e24_row36_col3, #T_10e24_row37_col0, #T_10e24_row37_col1, #T_10e24_row37_col2, #T_10e24_row37_col3, #T_10e24_row38_col0, #T_10e24_row38_col1, #T_10e24_row38_col2, #T_10e24_row38_col3, #T_10e24_row39_col0, #T_10e24_row39_col1, #T_10e24_row39_col2, #T_10e24_row39_col3, #T_10e24_row40_col0, #T_10e24_row40_col1, #T_10e24_row40_col2, #T_10e24_row40_col3, #T_10e24_row41_col0, #T_10e24_row41_col1, #T_10e24_row41_col2, #T_10e24_row41_col3, #T_10e24_row42_col0, #T_10e24_row42_col1, #T_10e24_row42_col2, #T_10e24_row42_col3, #T_10e24_row43_col0, #T_10e24_row43_col1, #T_10e24_row43_col2, #T_10e24_row43_col3, #T_10e24_row44_col0, #T_10e24_row44_col1, #T_10e24_row44_col2, #T_10e24_row44_col3, #T_10e24_row45_col0, #T_10e24_row45_col1, #T_10e24_row45_col2, #T_10e24_row45_col3, #T_10e24_row46_col0, #T_10e24_row46_col1, #T_10e24_row46_col2, #T_10e24_row46_col3, #T_10e24_row47_col0, #T_10e24_row47_col1, #T_10e24_row47_col2, #T_10e24_row47_col3, #T_10e24_row48_col0, #T_10e24_row48_col1, #T_10e24_row48_col2, #T_10e24_row48_col3, #T_10e24_row49_col0, #T_10e24_row49_col1, #T_10e24_row49_col2, #T_10e24_row49_col3, #T_10e24_row50_col0, #T_10e24_row50_col1, #T_10e24_row50_col2, #T_10e24_row50_col3, #T_10e24_row51_col0, #T_10e24_row51_col1, #T_10e24_row51_col2, #T_10e24_row51_col3, #T_10e24_row52_col0, #T_10e24_row52_col1, #T_10e24_row52_col2, #T_10e24_row52_col3, #T_10e24_row53_col0, #T_10e24_row53_col1, #T_10e24_row53_col2, #T_10e24_row53_col3, #T_10e24_row54_col0, #T_10e24_row54_col1, #T_10e24_row54_col2, #T_10e24_row54_col3, #T_10e24_row55_col0, #T_10e24_row55_col1, #T_10e24_row55_col2, #T_10e24_row55_col3, #T_10e24_row56_col0, #T_10e24_row56_col1, #T_10e24_row56_col2, #T_10e24_row56_col3, #T_10e24_row57_col0, #T_10e24_row57_col1, #T_10e24_row57_col2, #T_10e24_row57_col3, #T_10e24_row58_col0, #T_10e24_row58_col1, #T_10e24_row58_col2, #T_10e24_row58_col3, #T_10e24_row59_col0, #T_10e24_row59_col1, #T_10e24_row59_col2, #T_10e24_row59_col3 {
  background-color: black;
  color: lawngreen;
  border-color: white;
}
</style>
<table id="T_10e24">
  <thead>
    <tr>
      <th class="blank level0" >&nbsp;</th>
      <th id="T_10e24_level0_col0" class="col_heading level0 col0" >upc</th>
      <th id="T_10e24_level0_col1" class="col_heading level0 col1" >title</th>
      <th id="T_10e24_level0_col2" class="col_heading level0 col2" >price</th>
      <th id="T_10e24_level0_col3" class="col_heading level0 col3" >rating</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th id="T_10e24_level0_row0" class="row_heading level0 row0" >0</th>
      <td id="T_10e24_row0_col0" class="data row0 col0" >a897fe39b1053632</td>
      <td id="T_10e24_row0_col1" class="data row0 col1" >A Light in the Attic</td>
      <td id="T_10e24_row0_col2" class="data row0 col2" >££51.77</td>
      <td id="T_10e24_row0_col3" class="data row0 col3" >Three</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row1" class="row_heading level0 row1" >1</th>
      <td id="T_10e24_row1_col0" class="data row1 col0" >90fa61229261140a</td>
      <td id="T_10e24_row1_col1" class="data row1 col1" >Tipping the Velvet</td>
      <td id="T_10e24_row1_col2" class="data row1 col2" >££53.74</td>
      <td id="T_10e24_row1_col3" class="data row1 col3" >One</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row2" class="row_heading level0 row2" >2</th>
      <td id="T_10e24_row2_col0" class="data row2 col0" >6957f44c3847a760</td>
      <td id="T_10e24_row2_col1" class="data row2 col1" >Soumission</td>
      <td id="T_10e24_row2_col2" class="data row2 col2" >££50.10</td>
      <td id="T_10e24_row2_col3" class="data row2 col3" >One</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row3" class="row_heading level0 row3" >3</th>
      <td id="T_10e24_row3_col0" class="data row3 col0" >e00eb4fd7b871a48</td>
      <td id="T_10e24_row3_col1" class="data row3 col1" >Sharp Objects</td>
      <td id="T_10e24_row3_col2" class="data row3 col2" >££47.82</td>
      <td id="T_10e24_row3_col3" class="data row3 col3" >Four</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row4" class="row_heading level0 row4" >4</th>
      <td id="T_10e24_row4_col0" class="data row4 col0" >4165285e1663650f</td>
      <td id="T_10e24_row4_col1" class="data row4 col1" >Sapiens: A Brief History of Humankind</td>
      <td id="T_10e24_row4_col2" class="data row4 col2" >££54.23</td>
      <td id="T_10e24_row4_col3" class="data row4 col3" >Five</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row5" class="row_heading level0 row5" >5</th>
      <td id="T_10e24_row5_col0" class="data row5 col0" >f77dbf2323deb740</td>
      <td id="T_10e24_row5_col1" class="data row5 col1" >The Requiem Red</td>
      <td id="T_10e24_row5_col2" class="data row5 col2" >££22.65</td>
      <td id="T_10e24_row5_col3" class="data row5 col3" >One</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row6" class="row_heading level0 row6" >6</th>
      <td id="T_10e24_row6_col0" class="data row6 col0" >2597b5a345f45e1b</td>
      <td id="T_10e24_row6_col1" class="data row6 col1" >The Dirty Little Secrets of Getting Your Dream Job</td>
      <td id="T_10e24_row6_col2" class="data row6 col2" >££33.34</td>
      <td id="T_10e24_row6_col3" class="data row6 col3" >Four</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row7" class="row_heading level0 row7" >7</th>
      <td id="T_10e24_row7_col0" class="data row7 col0" >e72a5dfc7e9267b2</td>
      <td id="T_10e24_row7_col1" class="data row7 col1" >The Coming Woman: A Novel Based on the Life of the Infamous Feminist, Victoria Woodhull</td>
      <td id="T_10e24_row7_col2" class="data row7 col2" >££17.93</td>
      <td id="T_10e24_row7_col3" class="data row7 col3" >Three</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row8" class="row_heading level0 row8" >8</th>
      <td id="T_10e24_row8_col0" class="data row8 col0" >e10e1e165dc8be4a</td>
      <td id="T_10e24_row8_col1" class="data row8 col1" >The Boys in the Boat: Nine Americans and Their Epic Quest for Gold at the 1936 Berlin Olympics</td>
      <td id="T_10e24_row8_col2" class="data row8 col2" >££22.60</td>
      <td id="T_10e24_row8_col3" class="data row8 col3" >Four</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row9" class="row_heading level0 row9" >9</th>
      <td id="T_10e24_row9_col0" class="data row9 col0" >1dfe412b8ac00530</td>
      <td id="T_10e24_row9_col1" class="data row9 col1" >The Black Maria</td>
      <td id="T_10e24_row9_col2" class="data row9 col2" >££52.15</td>
      <td id="T_10e24_row9_col3" class="data row9 col3" >One</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row10" class="row_heading level0 row10" >10</th>
      <td id="T_10e24_row10_col0" class="data row10 col0" >0312262ecafa5a40</td>
      <td id="T_10e24_row10_col1" class="data row10 col1" >Starving Hearts (Triangular Trade Trilogy, #1)</td>
      <td id="T_10e24_row10_col2" class="data row10 col2" >££13.99</td>
      <td id="T_10e24_row10_col3" class="data row10 col3" >Two</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row11" class="row_heading level0 row11" >11</th>
      <td id="T_10e24_row11_col0" class="data row11 col0" >30a7f60cd76ca58c</td>
      <td id="T_10e24_row11_col1" class="data row11 col1" >Shakespeare's Sonnets</td>
      <td id="T_10e24_row11_col2" class="data row11 col2" >££20.66</td>
      <td id="T_10e24_row11_col3" class="data row11 col3" >Four</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row12" class="row_heading level0 row12" >12</th>
      <td id="T_10e24_row12_col0" class="data row12 col0" >ce6396b0f23f6ecc</td>
      <td id="T_10e24_row12_col1" class="data row12 col1" >Set Me Free</td>
      <td id="T_10e24_row12_col2" class="data row12 col2" >££17.46</td>
      <td id="T_10e24_row12_col3" class="data row12 col3" >Five</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row13" class="row_heading level0 row13" >13</th>
      <td id="T_10e24_row13_col0" class="data row13 col0" >3b1c02bac2a429e6</td>
      <td id="T_10e24_row13_col1" class="data row13 col1" >Scott Pilgrim's Precious Little Life (Scott Pilgrim #1)</td>
      <td id="T_10e24_row13_col2" class="data row13 col2" >££52.29</td>
      <td id="T_10e24_row13_col3" class="data row13 col3" >Five</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row14" class="row_heading level0 row14" >14</th>
      <td id="T_10e24_row14_col0" class="data row14 col0" >a34ba96d4081e6a4</td>
      <td id="T_10e24_row14_col1" class="data row14 col1" >Rip it Up and Start Again</td>
      <td id="T_10e24_row14_col2" class="data row14 col2" >££35.02</td>
      <td id="T_10e24_row14_col3" class="data row14 col3" >Five</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row15" class="row_heading level0 row15" >15</th>
      <td id="T_10e24_row15_col0" class="data row15 col0" >deda3e61b9514b83</td>
      <td id="T_10e24_row15_col1" class="data row15 col1" >Our Band Could Be Your Life: Scenes from the American Indie Underground, 1981-1991</td>
      <td id="T_10e24_row15_col2" class="data row15 col2" >££57.25</td>
      <td id="T_10e24_row15_col3" class="data row15 col3" >Three</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row16" class="row_heading level0 row16" >16</th>
      <td id="T_10e24_row16_col0" class="data row16 col0" >feb7cc7701ecf901</td>
      <td id="T_10e24_row16_col1" class="data row16 col1" >Olio</td>
      <td id="T_10e24_row16_col2" class="data row16 col2" >££23.88</td>
      <td id="T_10e24_row16_col3" class="data row16 col3" >One</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row17" class="row_heading level0 row17" >17</th>
      <td id="T_10e24_row17_col0" class="data row17 col0" >e30f54cea9b38190</td>
      <td id="T_10e24_row17_col1" class="data row17 col1" >Mesaerion: The Best Science Fiction Stories 1800-1849</td>
      <td id="T_10e24_row17_col2" class="data row17 col2" >££37.59</td>
      <td id="T_10e24_row17_col3" class="data row17 col3" >One</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row18" class="row_heading level0 row18" >18</th>
      <td id="T_10e24_row18_col0" class="data row18 col0" >a18a4f574854aced</td>
      <td id="T_10e24_row18_col1" class="data row18 col1" >Libertarianism for Beginners</td>
      <td id="T_10e24_row18_col2" class="data row18 col2" >££51.33</td>
      <td id="T_10e24_row18_col3" class="data row18 col3" >Two</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row19" class="row_heading level0 row19" >19</th>
      <td id="T_10e24_row19_col0" class="data row19 col0" >a22124811bfa8350</td>
      <td id="T_10e24_row19_col1" class="data row19 col1" >It's Only the Himalayas</td>
      <td id="T_10e24_row19_col2" class="data row19 col2" >££45.17</td>
      <td id="T_10e24_row19_col3" class="data row19 col3" >Two</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row20" class="row_heading level0 row20" >20</th>
      <td id="T_10e24_row20_col0" class="data row20 col0" >23356462d1320d61</td>
      <td id="T_10e24_row20_col1" class="data row20 col1" >In Her Wake</td>
      <td id="T_10e24_row20_col2" class="data row20 col2" >££12.84</td>
      <td id="T_10e24_row20_col3" class="data row20 col3" >One</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row21" class="row_heading level0 row21" >21</th>
      <td id="T_10e24_row21_col0" class="data row21 col0" >327f68a59745c102</td>
      <td id="T_10e24_row21_col1" class="data row21 col1" >How Music Works</td>
      <td id="T_10e24_row21_col2" class="data row21 col2" >££37.32</td>
      <td id="T_10e24_row21_col3" class="data row21 col3" >Two</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row22" class="row_heading level0 row22" >22</th>
      <td id="T_10e24_row22_col0" class="data row22 col0" >5674a18a29a43ced</td>
      <td id="T_10e24_row22_col1" class="data row22 col1" >Foolproof Preserving: A Guide to Small Batch Jams, Jellies, Pickles, Condiments, and More: A Foolproof Guide to Making Small Batch Jams, Jellies, Pickles, Condiments, and More</td>
      <td id="T_10e24_row22_col2" class="data row22 col2" >££30.52</td>
      <td id="T_10e24_row22_col3" class="data row22 col3" >Three</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row23" class="row_heading level0 row23" >23</th>
      <td id="T_10e24_row23_col0" class="data row23 col0" >c2e46a2ee3b4a322</td>
      <td id="T_10e24_row23_col1" class="data row23 col1" >Chase Me (Paris Nights #2)</td>
      <td id="T_10e24_row23_col2" class="data row23 col2" >££25.27</td>
      <td id="T_10e24_row23_col3" class="data row23 col3" >Five</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row24" class="row_heading level0 row24" >24</th>
      <td id="T_10e24_row24_col0" class="data row24 col0" >00bfed9e18bb36f3</td>
      <td id="T_10e24_row24_col1" class="data row24 col1" >Black Dust</td>
      <td id="T_10e24_row24_col2" class="data row24 col2" >££34.53</td>
      <td id="T_10e24_row24_col3" class="data row24 col3" >Five</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row25" class="row_heading level0 row25" >25</th>
      <td id="T_10e24_row25_col0" class="data row25 col0" >9528d0948525bf5f</td>
      <td id="T_10e24_row25_col1" class="data row25 col1" >Birdsong: A Story in Pictures</td>
      <td id="T_10e24_row25_col2" class="data row25 col2" >££54.64</td>
      <td id="T_10e24_row25_col3" class="data row25 col3" >Three</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row26" class="row_heading level0 row26" >26</th>
      <td id="T_10e24_row26_col0" class="data row26 col0" >c7d160c2c0de586f</td>
      <td id="T_10e24_row26_col1" class="data row26 col1" >America's Cradle of Quarterbacks: Western Pennsylvania's Football Factory from Johnny Unitas to Joe Montana</td>
      <td id="T_10e24_row26_col2" class="data row26 col2" >££22.50</td>
      <td id="T_10e24_row26_col3" class="data row26 col3" >Three</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row27" class="row_heading level0 row27" >27</th>
      <td id="T_10e24_row27_col0" class="data row27 col0" >904208d6aa64b655</td>
      <td id="T_10e24_row27_col1" class="data row27 col1" >Aladdin and His Wonderful Lamp</td>
      <td id="T_10e24_row27_col2" class="data row27 col2" >££53.13</td>
      <td id="T_10e24_row27_col3" class="data row27 col3" >Three</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row28" class="row_heading level0 row28" >28</th>
      <td id="T_10e24_row28_col0" class="data row28 col0" >4c28def39d850cdf</td>
      <td id="T_10e24_row28_col1" class="data row28 col1" >Worlds Elsewhere: Journeys Around Shakespeareâs Globe</td>
      <td id="T_10e24_row28_col2" class="data row28 col2" >££40.30</td>
      <td id="T_10e24_row28_col3" class="data row28 col3" >Five</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row29" class="row_heading level0 row29" >29</th>
      <td id="T_10e24_row29_col0" class="data row29 col0" >ccd9ffa25efabdea</td>
      <td id="T_10e24_row29_col1" class="data row29 col1" >Wall and Piece</td>
      <td id="T_10e24_row29_col2" class="data row29 col2" >££44.18</td>
      <td id="T_10e24_row29_col3" class="data row29 col3" >Four</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row30" class="row_heading level0 row30" >30</th>
      <td id="T_10e24_row30_col0" class="data row30 col0" >6258a1f6a6dcfe50</td>
      <td id="T_10e24_row30_col1" class="data row30 col1" >The Four Agreements: A Practical Guide to Personal Freedom</td>
      <td id="T_10e24_row30_col2" class="data row30 col2" >££17.66</td>
      <td id="T_10e24_row30_col3" class="data row30 col3" >Five</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row31" class="row_heading level0 row31" >31</th>
      <td id="T_10e24_row31_col0" class="data row31 col0" >3c039985229453bf</td>
      <td id="T_10e24_row31_col1" class="data row31 col1" >The Five Love Languages: How to Express Heartfelt Commitment to Your Mate</td>
      <td id="T_10e24_row31_col2" class="data row31 col2" >££31.05</td>
      <td id="T_10e24_row31_col3" class="data row31 col3" >Three</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row32" class="row_heading level0 row32" >32</th>
      <td id="T_10e24_row32_col0" class="data row32 col0" >5dada2b7be26bd03</td>
      <td id="T_10e24_row32_col1" class="data row32 col1" >The Elephant Tree</td>
      <td id="T_10e24_row32_col2" class="data row32 col2" >££23.82</td>
      <td id="T_10e24_row32_col3" class="data row32 col3" >Five</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row33" class="row_heading level0 row33" >33</th>
      <td id="T_10e24_row33_col0" class="data row33 col0" >9f6568e9c95f60b0</td>
      <td id="T_10e24_row33_col1" class="data row33 col1" >The Bear and the Piano</td>
      <td id="T_10e24_row33_col2" class="data row33 col2" >££36.89</td>
      <td id="T_10e24_row33_col3" class="data row33 col3" >One</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row34" class="row_heading level0 row34" >34</th>
      <td id="T_10e24_row34_col0" class="data row34 col0" >6be3beb0793a53e7</td>
      <td id="T_10e24_row34_col1" class="data row34 col1" >Sophie's World</td>
      <td id="T_10e24_row34_col2" class="data row34 col2" >££15.94</td>
      <td id="T_10e24_row34_col3" class="data row34 col3" >Five</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row35" class="row_heading level0 row35" >35</th>
      <td id="T_10e24_row35_col0" class="data row35 col0" >668fe56b17cfcd4f</td>
      <td id="T_10e24_row35_col1" class="data row35 col1" >Penny Maybe</td>
      <td id="T_10e24_row35_col2" class="data row35 col2" >££33.29</td>
      <td id="T_10e24_row35_col3" class="data row35 col3" >Three</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row36" class="row_heading level0 row36" >36</th>
      <td id="T_10e24_row36_col0" class="data row36 col0" >094b269567e1c300</td>
      <td id="T_10e24_row36_col1" class="data row36 col1" >Maude (1883-1993):She Grew Up with the country</td>
      <td id="T_10e24_row36_col2" class="data row36 col2" >££18.02</td>
      <td id="T_10e24_row36_col3" class="data row36 col3" >Two</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row37" class="row_heading level0 row37" >37</th>
      <td id="T_10e24_row37_col0" class="data row37 col0" >19ed25f4641d5efd</td>
      <td id="T_10e24_row37_col1" class="data row37 col1" >In a Dark, Dark Wood</td>
      <td id="T_10e24_row37_col2" class="data row37 col2" >££19.63</td>
      <td id="T_10e24_row37_col3" class="data row37 col3" >One</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row38" class="row_heading level0 row38" >38</th>
      <td id="T_10e24_row38_col0" class="data row38 col0" >be5cc846f45496fb</td>
      <td id="T_10e24_row38_col1" class="data row38 col1" >Behind Closed Doors</td>
      <td id="T_10e24_row38_col2" class="data row38 col2" >££52.22</td>
      <td id="T_10e24_row38_col3" class="data row38 col3" >Four</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row39" class="row_heading level0 row39" >39</th>
      <td id="T_10e24_row39_col0" class="data row39 col0" >55f9da0c5eea2e10</td>
      <td id="T_10e24_row39_col1" class="data row39 col1" >You can't bury them all: Poems</td>
      <td id="T_10e24_row39_col2" class="data row39 col2" >££33.63</td>
      <td id="T_10e24_row39_col3" class="data row39 col3" >Two</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row40" class="row_heading level0 row40" >40</th>
      <td id="T_10e24_row40_col0" class="data row40 col0" >b4fd5943413e089a</td>
      <td id="T_10e24_row40_col1" class="data row40 col1" >Slow States of Collapse: Poems</td>
      <td id="T_10e24_row40_col2" class="data row40 col2" >££57.31</td>
      <td id="T_10e24_row40_col3" class="data row40 col3" >Three</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row41" class="row_heading level0 row41" >41</th>
      <td id="T_10e24_row41_col0" class="data row41 col0" >c8f7f0cb1abb9cac</td>
      <td id="T_10e24_row41_col1" class="data row41 col1" >Reasons to Stay Alive</td>
      <td id="T_10e24_row41_col2" class="data row41 col2" >££26.41</td>
      <td id="T_10e24_row41_col3" class="data row41 col3" >Two</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row42" class="row_heading level0 row42" >42</th>
      <td id="T_10e24_row42_col0" class="data row42 col0" >b12b89017878a60d</td>
      <td id="T_10e24_row42_col1" class="data row42 col1" >Private Paris (Private #10)</td>
      <td id="T_10e24_row42_col2" class="data row42 col2" >££47.61</td>
      <td id="T_10e24_row42_col3" class="data row42 col3" >Five</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row43" class="row_heading level0 row43" >43</th>
      <td id="T_10e24_row43_col0" class="data row43 col0" >c27f6e1f185b0383</td>
      <td id="T_10e24_row43_col1" class="data row43 col1" >#HigherSelfie: Wake Up Your Life. Free Your Soul. Find Your Tribe.</td>
      <td id="T_10e24_row43_col2" class="data row43 col2" >££23.11</td>
      <td id="T_10e24_row43_col3" class="data row43 col3" >Five</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row44" class="row_heading level0 row44" >44</th>
      <td id="T_10e24_row44_col0" class="data row44 col0" >acddfeab2a407640</td>
      <td id="T_10e24_row44_col1" class="data row44 col1" >Without Borders (Wanderlove #1)</td>
      <td id="T_10e24_row44_col2" class="data row44 col2" >££45.07</td>
      <td id="T_10e24_row44_col3" class="data row44 col3" >Two</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row45" class="row_heading level0 row45" >45</th>
      <td id="T_10e24_row45_col0" class="data row45 col0" >3ecb690d1542c568</td>
      <td id="T_10e24_row45_col1" class="data row45 col1" >When We Collided</td>
      <td id="T_10e24_row45_col2" class="data row45 col2" >££31.77</td>
      <td id="T_10e24_row45_col3" class="data row45 col3" >One</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row46" class="row_heading level0 row46" >46</th>
      <td id="T_10e24_row46_col0" class="data row46 col0" >8d455c7539795d2a</td>
      <td id="T_10e24_row46_col1" class="data row46 col1" >We Love You, Charlie Freeman</td>
      <td id="T_10e24_row46_col2" class="data row46 col2" >££50.27</td>
      <td id="T_10e24_row46_col3" class="data row46 col3" >Five</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row47" class="row_heading level0 row47" >47</th>
      <td id="T_10e24_row47_col0" class="data row47 col0" >657fe5ead67a7767</td>
      <td id="T_10e24_row47_col1" class="data row47 col1" >Untitled Collection: Sabbath Poems 2014</td>
      <td id="T_10e24_row47_col2" class="data row47 col2" >££14.27</td>
      <td id="T_10e24_row47_col3" class="data row47 col3" >Four</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row48" class="row_heading level0 row48" >48</th>
      <td id="T_10e24_row48_col0" class="data row48 col0" >d170b5a0da46380e</td>
      <td id="T_10e24_row48_col1" class="data row48 col1" >Unseen City: The Majesty of Pigeons, the Discreet Charm of Snails & Other Wonders of the Urban Wilderness</td>
      <td id="T_10e24_row48_col2" class="data row48 col2" >££44.18</td>
      <td id="T_10e24_row48_col3" class="data row48 col3" >Four</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row49" class="row_heading level0 row49" >49</th>
      <td id="T_10e24_row49_col0" class="data row49 col0" >7ae099f3898e0209</td>
      <td id="T_10e24_row49_col1" class="data row49 col1" >Unicorn Tracks</td>
      <td id="T_10e24_row49_col2" class="data row49 col2" >££18.78</td>
      <td id="T_10e24_row49_col3" class="data row49 col3" >Three</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row50" class="row_heading level0 row50" >50</th>
      <td id="T_10e24_row50_col0" class="data row50 col0" >9a9d6e9bc555731e</td>
      <td id="T_10e24_row50_col1" class="data row50 col1" >Unbound: How Eight Technologies Made Us Human, Transformed Society, and Brought Our World to the Brink</td>
      <td id="T_10e24_row50_col2" class="data row50 col2" >££25.52</td>
      <td id="T_10e24_row50_col3" class="data row50 col3" >One</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row51" class="row_heading level0 row51" >51</th>
      <td id="T_10e24_row51_col0" class="data row51 col0" >09555be379ad9253</td>
      <td id="T_10e24_row51_col1" class="data row51 col1" >Tsubasa: WoRLD CHRoNiCLE 2 (Tsubasa WoRLD CHRoNiCLE #2)</td>
      <td id="T_10e24_row51_col2" class="data row51 col2" >££16.28</td>
      <td id="T_10e24_row51_col3" class="data row51 col3" >One</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row52" class="row_heading level0 row52" >52</th>
      <td id="T_10e24_row52_col0" class="data row52 col0" >7858914fad68493c</td>
      <td id="T_10e24_row52_col1" class="data row52 col1" >Throwing Rocks at the Google Bus: How Growth Became the Enemy of Prosperity</td>
      <td id="T_10e24_row52_col2" class="data row52 col2" >££31.12</td>
      <td id="T_10e24_row52_col3" class="data row52 col3" >Three</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row53" class="row_heading level0 row53" >53</th>
      <td id="T_10e24_row53_col0" class="data row53 col0" >51653ef291ab7ddc</td>
      <td id="T_10e24_row53_col1" class="data row53 col1" >This One Summer</td>
      <td id="T_10e24_row53_col2" class="data row53 col2" >££19.49</td>
      <td id="T_10e24_row53_col3" class="data row53 col3" >Four</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row54" class="row_heading level0 row54" >54</th>
      <td id="T_10e24_row54_col0" class="data row54 col0" >709822d0b5bcb7f4</td>
      <td id="T_10e24_row54_col1" class="data row54 col1" >Thirst</td>
      <td id="T_10e24_row54_col2" class="data row54 col2" >££17.27</td>
      <td id="T_10e24_row54_col3" class="data row54 col3" >Five</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row55" class="row_heading level0 row55" >55</th>
      <td id="T_10e24_row55_col0" class="data row55 col0" >4eed62cf5f8d8edf</td>
      <td id="T_10e24_row55_col1" class="data row55 col1" >The Torch Is Passed: A Harding Family Story</td>
      <td id="T_10e24_row55_col2" class="data row55 col2" >££19.09</td>
      <td id="T_10e24_row55_col3" class="data row55 col3" >One</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row56" class="row_heading level0 row56" >56</th>
      <td id="T_10e24_row56_col0" class="data row56 col0" >b5ea0b5dabed25a8</td>
      <td id="T_10e24_row56_col1" class="data row56 col1" >The Secret of Dreadwillow Carse</td>
      <td id="T_10e24_row56_col2" class="data row56 col2" >££56.13</td>
      <td id="T_10e24_row56_col3" class="data row56 col3" >One</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row57" class="row_heading level0 row57" >57</th>
      <td id="T_10e24_row57_col0" class="data row57 col0" >142d767a56a9254d</td>
      <td id="T_10e24_row57_col1" class="data row57 col1" >The Pioneer Woman Cooks: Dinnertime: Comfort Classics, Freezer Food, 16-Minute Meals, and Other Delicious Ways to Solve Supper!</td>
      <td id="T_10e24_row57_col2" class="data row57 col2" >££56.41</td>
      <td id="T_10e24_row57_col3" class="data row57 col3" >One</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row58" class="row_heading level0 row58" >58</th>
      <td id="T_10e24_row58_col0" class="data row58 col0" >5ee94540d0749ea0</td>
      <td id="T_10e24_row58_col1" class="data row58 col1" >The Past Never Ends</td>
      <td id="T_10e24_row58_col2" class="data row58 col2" >££56.50</td>
      <td id="T_10e24_row58_col3" class="data row58 col3" >Four</td>
    </tr>
    <tr>
      <th id="T_10e24_level0_row59" class="row_heading level0 row59" >59</th>
      <td id="T_10e24_row59_col0" class="data row59 col0" >cedf82b5086e4691</td>
      <td id="T_10e24_row59_col1" class="data row59 col1" >The Natural History of Us (The Fine Art of Pretending #2)</td>
      <td id="T_10e24_row59_col2" class="data row59 col2" >££45.22</td>
      <td id="T_10e24_row59_col3" class="data row59 col3" >Three</td>
    </tr>
  </tbody>
</table>



