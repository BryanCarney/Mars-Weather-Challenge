# Mars-Weather-Challenge

# Module 11 Challenge Files

[Part 1 - Mars News](https://github.com/BryanCarney/Mars-Weather-Challenge/blob/main/part_1_mars_news.ipynb)

[Part 2 - Mars Weather](https://github.com/BryanCarney/Mars-Weather-Challenge/blob/main/part_2_mars_weather.ipynb)

[Mars Data Extract](https://github.com/BryanCarney/Mars-Weather-Challenge/blob/main/mars_data.csv)

# Background

This new assignment consists of two technical products with the following deliverables:

Deliverable 1: Scrape titles and preview text from Mars news articles.

Deliverable 2: Scrape and analyze Mars weather data, which exists in a table.

## Deliverable 1: Scrape Titles and Preview Text from Mars News

Import Splinter and BeautifulSoup

    from splinter import Browser
    from bs4 import BeautifulSoup
    
    browser = Browser('chrome')

**Step 1: Visit the Website**

1. Use automated browsing to visit the [Mars news site](https://static.bc-edx.com/data/web/mars_news/index.html). Inspect the page to identify which elements to scrape.

> **Hint** To identify which elements to scrape, you might want to inspect the page by using Chrome DevTools.

Visit the Mars news site

    url = 'https://static.bc-edx.com/data/web/mars_news/index.html'
    browser.visit(url)

**Step 2: Scrape the Website**

Create a Beautiful Soup object and use it to extract text elements from the website.

Create a Beautiful Soup object

    html = browser.html
    soup = BeautifulSoup(html, 'html.parser')

Extract all the text elements

    news_title = soup.find('div', id = "news")
    articles = news_title.find_all('div', class_="col-md-12")
    
    for article in articles:
        title = article.find('div', class_="content_title").get_text()
        summary = article.find('div', class_="article_teaser_body").get_text()
        print(f"News Article Title: {title}\nNews Article Summary: {summary}\n")

![image](https://github.com/user-attachments/assets/000e3f0c-c049-427f-b239-61e7ceb6ddcb)

**Step 3: Store the Results**

Extract the titles and preview text of the news articles that you scraped. Store the scraping results in Python data structures as follows:

* Store each title-and-preview pair in a Python dictionary. And, give each dictionary two keys: `title` and `preview`. An example is the following:

{'title': "NASA's MAVEN Observes Martian Light Show Caused by Major Solar Storm", 
'preview': "For the first time in its eight years orbiting Mars, NASA’s MAVEN mission witnessed two different types of ultraviolet aurorae simultaneously, the result of solar storms that began on Aug. 27."


* Store all the dictionaries in a Python list.

* Print the list in your notebook.

Create an empty list to store the dictionaries

    news_articles = []

Loop through the text elements

    for article in articles:
    
Extract the title and preview text from the elements

        title = article.find('div', class_="content_title").get_text()
        summary = article.find('div', class_="article_teaser_body").get_text()

Store each title and preview pair in a dictionary

        news_dict = {"News Article Title": title, "News Article Summary": summary}

Add the dictionary to the list

        news_articles.append(news_dict)

Print the list to confirm success
    
    news_articles

![image](https://github.com/user-attachments/assets/48e58a57-ecd3-45ef-8f99-b2a5938e895b)

    browser.quit()

## Deliverable 2: Scrape and Analyze Mars Weather Data

Import relevant libraries

    from splinter import Browser
    from bs4 import BeautifulSoup
    import matplotlib.pyplot as plt
    import pandas as pd
    import calendar
    import requests

    browser = Browser('chrome')

**Step 1: Visit the Website**

Use automated browsing to visit the [Mars Temperature Data Site](https://static.bc-edx.com/data/web/mars_facts/temperature.html). Inspect the page to identify which elements to scrape.

   > **Hint** To identify which elements to scrape, you might want to inspect the page by using Chrome DevTools to discover whether the table contains usable classes.

Visit the website

https://static.bc-edx.com/data/web/mars_facts/temperature.html

    url = "https://static.bc-edx.com/data/web/mars_facts/temperature.html"
    browser.visit(url)

**Step 2: Scrape the Table**

Create a Beautiful Soup object and use it to scrape the data in the HTML table.

Note that this can also be achieved by using the Pandas `read_html` function. However, use Beautiful Soup here to continue sharpening your web scraping skills.

Create a Beautiful Soup Object

    html = browser.html
    soup = BeautifulSoup(html, 'html.parser')

Extract all rows of data

    all_rows = soup.find_all('tr')
    all_rows

![image](https://github.com/user-attachments/assets/07ef310f-d5a6-48da-ac0b-bd738ab4154d)

**Step 3: Store the Data**

Assemble the scraped data into a Pandas DataFrame. The columns should have the same headings as the table on the website. Here’s an explanation of the column headings:

* `id`: the identification number of a single transmission from the Curiosity rover
* `terrestrial_date`: the date on Earth
* `sol`: the number of elapsed sols (Martian days) since Curiosity landed on Mars
* `ls`: the solar longitude
* `month`: the Martian month
* `min_temp`: the minimum temperature, in Celsius, of a single Martian day (sol)
* `pressure`: The atmospheric pressure at Curiosity's location

Create an empty list

    mars_data = []

Loop through the scraped data to create a list of rows

    header_row = soup.find('tr')
    headers = [header.get_text() for header in header_row.find_all('th')]

Print the headers to check the content

    print(headers)

Extract all rows of data

    rows = soup.find_all('tr')[1:]  # Skip the header row

Loop through the scraped data to create a list of rows

    for row in rows:
        row_data = [cell.get_text() for cell in row.find_all('td')]
        mars_data.append(row_data)

Print the rows_data to check the content

    for row in mars_data[:5]:
        print(row)

![image](https://github.com/user-attachments/assets/02c43435-a9ec-4afc-8932-450f7de629c9)

Create a Pandas DataFrame by using the list of rows and a list of the column names

    mars_df = pd.DataFrame(mars_data, columns=headers)

Confirm DataFrame was created successfully

    mars_df.head()

![image](https://github.com/user-attachments/assets/dcc7b167-58bc-4a39-9e27-2eec9c8c58bc)

**Step 4: Prepare Data for Analysis**

Examine the data types that are currently associated with each column. If necessary, cast (or convert) the data to the appropriate `datetime`, `int`, or `float` data types.

  > **Hint** You can use the Pandas `astype` and `to_datetime` methods to accomplish this task.

Examine data type of each column

    mars_df.dtypes

![image](https://github.com/user-attachments/assets/f9729b84-57a5-46b4-acf9-64ae958f6d4f)

Change data types for data analysis

    mars_df['id'] = mars_df['id'].astype(int)
    mars_df['terrestrial_date'] = pd.to_datetime(mars_df['terrestrial_date'])
    mars_df['sol'] = mars_df['sol'].astype(int)
    mars_df['ls'] = mars_df['ls'].astype(float)
    mars_df['month'] = mars_df['month'].astype(int)
    mars_df['min_temp'] = mars_df['min_temp'].astype(float)
    mars_df['pressure'] = mars_df['pressure'].astype(float)

Confirm type changes were successful by examining data types again
    
    mars_df.dtypes

![image](https://github.com/user-attachments/assets/01947ae4-d6cb-4251-99b3-db39a34bb568)

**Step 5: Analyze the Data**

Analyze your dataset by using Pandas functions to answer the following questions:

1. How many months exist on Mars?
2. How many Martian (and not Earth) days worth of data exist in the scraped dataset?
3. What are the coldest and the warmest months on Mars (at the location of Curiosity)? To answer this question:
    * Find the average the minimum daily temperature for all of the months.
    * Plot the results as a bar chart.
4. Which months have the lowest and the highest atmospheric pressure on Mars? To answer this question:
    * Find the average the daily atmospheric pressure of all the months.
    * Plot the results as a bar chart.
5. About how many terrestrial (Earth) days exist in a Martian year? To answer this question:
    * Consider how many days elapse on Earth in the time that Mars circles the Sun once.
    * Visually estimate the result by plotting the daily minimum temperature.

Step 5 Analysis

1. How many months are there on Mars?

        mars_months = mars_df['month'].nunique()
        mars_months

![image](https://github.com/user-attachments/assets/62f3727e-83be-4c5f-8898-57ab97f20130)

2. How many sols (Martian days) worth of data are there?

        mars_days = mars_df['sol'].nunique()
        mars_days

![image](https://github.com/user-attachments/assets/4ada73b1-3229-42c2-96af-cfd1e1913a2b)

3. What is the average minimum temperature by month?

        avg_min_temp = mars_df.groupby('month')['min_temp'].mean()
        avg_min_temp

![image](https://github.com/user-attachments/assets/5b213b45-1521-4da0-a72a-94ab39da7352)

Plot the average minimum temperature by month

    avg_min_temp.plot(kind='bar', color='blue', figsize=(10, 6))
    plt.title('Average Minimum Temperature by Month on Mars')
    plt.xlabel('Month')
    plt.ylabel('Average Minimum Temperature (Celsius)')
    plt.xticks(rotation=0)
    plt.legend(['Avg Min Temp'])
    plt.show()

![image](https://github.com/user-attachments/assets/a4649d10-b32d-46ff-b4b3-57cf2d7a658b)

Identify the coldest and hottest months in Curiosity's location by sorting the previous graph

    coldest_temp = avg_min_temp.min()
    coldest_temp_month = avg_min_temp.idxmin() 
    hottest_temp = avg_min_temp.max()
    hottest_temp_month = avg_min_temp.idxmax()

    print(f'The coldest month on Mars is month number {coldest_temp_month} at {coldest_temp}')
    print(f'The hottest month on Mars is month number {hottest_temp_month} at {hottest_temp}')

    sorted_avg_min_temp = avg_min_temp.sort_values()

Plot the results of the resorted values

    sorted_avg_min_temp.plot(kind='bar', color='blue', figsize=(10, 6))
    plt.title('Sorted Average Minimum Temperature by Month on Mars')
    plt.xlabel('Month')
    plt.ylabel('Average Minimum Temperature (Celsius)')
    plt.xticks(rotation=0)
    plt.legend(['Avg Min Temp'])
    plt.show()

![image](https://github.com/user-attachments/assets/14b57ca6-bc38-41f6-b903-79e47b7f42c5)

4. What is the average pressure by month?

        avg_pressure = mars_df.groupby('month')['pressure'].mean()
        avg_pressure

![image](https://github.com/user-attachments/assets/b05465b3-46ec-4fdc-9e52-e03787a4e246)

Plot the average pressure by month

    avg_pressure.plot(kind='bar', color='blue', figsize=(10, 6))
    plt.title('Average Pressure by Month on Mars')
    plt.xlabel('Month')
    plt.ylabel('Average Pressure')
    plt.xticks(rotation=0)
    plt.legend(['Avg Pressure'])
    plt.show()

![image](https://github.com/user-attachments/assets/43f70110-e663-4b65-ae24-342f49cd0cc0)

Identify the lowest and highest pressure months in Curiosity's location by sorting the previous graph

    lowest_pressure = avg_pressure.min()
    lowest_pressure_month = avg_pressure.idxmin() 
    highest_pressure = avg_pressure.max()
    highest_pressure_month = avg_pressure.idxmax()

    print(f'The lowest pressure month on Mars is month number {lowest_pressure_month} at {lowest_pressure}')
    print(f'The highest pressure month on Mars is month number {highest_pressure_month} at {highest_pressure}')
    
    sorted_avg_pressure = avg_pressure.sort_values()

Plot the results of the resorted values

    sorted_avg_pressure.plot(kind='bar', color='blue', figsize=(10, 6))
    plt.title('Sorted Average Pressure by Month on Mars')
    plt.xlabel('Month')
    plt.ylabel('Average Pressure (Celsius)')
    plt.xticks(rotation=0)
    plt.legend(['Avg Pressure'])
    plt.show()

![image](https://github.com/user-attachments/assets/4175c2d4-4c94-4160-96e3-0540cb5d4c29)

5. How many terrestrial (Earth) days are there in a Martian year?

Visually estimate the result by plotting the daily minimum temperature of each observation in the data set.

    days_in_year = range(len(mars_df))
    plt.figure(figsize=(12, 6))
    plt.plot(days_in_year, mars_df['min_temp'], color='blue')
    plt.title('Daily Minimum Temperature on Mars')
    plt.xlabel('Terrestrial Days')
    plt.ylabel('Minimum Temperature (Celsius)')
    plt.xticks(rotation=45)
    plt.legend(['Min Temp'])
    plt.show()

![image](https://github.com/user-attachments/assets/9f24ad88-b1a9-40c0-b16e-e2048f33d117)

Divide the data into a smaller section to isolate a period of time.  This will help to determine if there is a pattern

    mars_sliced = mars_df.loc[435:1110]
    mars_sliced.min_temp.plot(kind='line', color='blue', figsize=(8, 4))
    plt.title('Minimum Temperature by Sol on Mars')
    plt.xlabel('Sol')
    plt.ylabel('Minimum Temperature (Celsius)')
    plt.show()

![image](https://github.com/user-attachments/assets/48356c1b-e4a4-4a9d-ae13-3d3f07c1cf5c)

Taking the value of 675 as obtained from the graph above, apply the same metric to another portion of data to see if the pattern is consistent

    mars_sliced = mars_df.loc[725:1350]
    mars_sliced.min_temp.plot(kind='line', color='blue', figsize=(8, 4))
    plt.title('Minimum Temperature by Sol on Mars')
    plt.xlabel('Sol')
    plt.ylabel('Minimum Temperature (Celsius)')
    plt.show()

![image](https://github.com/user-attachments/assets/99bf5f59-93ea-4c54-af10-bdc68ac6bba2)

Plot to see where these values align on the graph.  If the peaks are aligned to the same general areas, then its safe to conclude that the data is likely to be a year.

    plt.figure(figsize=(12, 6))
    plt.plot(days_in_year, mars_df['min_temp'], color='blue')
    plt.title('Daily Minimum Temperature on Mars')
    plt.axvline(x=435, color='red', linewidth=3)
    plt.axvline(x=1110, color='red', linewidth=3)
    plt.axvline(x=700, color='green', linewidth=3)
    plt.axvline(x=1350, color='green', linewidth=3)
    plt.xticks(list(plt.xticks()[0]) + [435, 1110, 700, 1350])
    plt.xlabel('Terrestrial Days')
    plt.ylabel('Minimum Temperature (Celsius)')
    plt.xticks(rotation=90)
    plt.legend(['Min Temp'])
    plt.show()

![image](https://github.com/user-attachments/assets/f9264082-46cc-48ec-8c4c-2ebac9526208)

#### Minimum Temperature

Based on the data provided the 3rd month of the year is, on average, the coldest tempreture on record.  The data also suggests that the observed area experiences seasonal temperature variations.  The colder periods on record are observed between the 2 - 5 months and the warmer periods happeneing between the 7-10 months.  The environment in this region appears to undergo predictable temperature fluctuations throughout the year. However, additional data would be necessary to determine whether these trends persist over multiple years and if the average temperatures remain consistent year over year.

• The coldest month on Mars is month number 3 at -83.30729166666667

• The hottest month on Mars is month number 8 at -68.38297872340425

#### Atmospheric Pressure

After analysis on the data provided, it was cocluded that the 6th month of the year is, on average, the lowest pressure on record with the 9th month having the highest.  This suggests that the pressure fluctuations in the observed area are not aligned with its seasonal temperature variations. This lack of alignment indicates that temperature may not have a direct correlation with the pressure changes in this region.

• The lowest pressure month on Mars is month number 6 at 745.0544217687075

• The highest pressure month on Mars is month number 9 at 913.3059701492538

#### Year Length

The "Daily Minimum Temperature" graph illustrates the daily temperature variations over a specific period based on terrestrial dates. By focusing on distinct peaks and valleys within the data, the aim to investigate whether temperature fluctuations can serve as indicators for estimating the length of a Martian year.  

Initially, a period between 400 and 1100 days was isolated based on observed patterns in the data. Upon closer examination, trends suggested refining this range to 435 and 1110 days, as it provided a more accurate representation of the underlying patterns. This refined period yielded a value of 675 days.

To validate this estimate, the 675-day period was applied to a different section of the data to assess alignment with the observed patterns (700 and 1350 days). After plotting both sections on the graph, it became evident that the patterns were consistent with the 675-day estimate, suggesting that this value offers a reasonable approximation for the length of a Martian year.  

Based on the extract below from the Nasa Website, a year on Mars is estimated to be 687.  This is based on how long it takes each of the planets in our solar system to orbit around the Sun (in Earth days).

Our estimated value of 675, is close enough to reinforce the conclusion that temperature variation patterns can serve as reliable indicators for determining the length of a Martian year.

**Extra Step: Pull data directly from Nasa Website to confirm hypothesis** 

URL of Nasa website

    url_nasa = "https://spaceplace.nasa.gov/years-on-other-planets/en/"
    response = requests.get(url_nasa)
    soup = BeautifulSoup(response.text, 'html.parser')

    mars_section = soup.find('strong', string='Mars')
    
    mars_text = mars_section.next_sibling.strip()  
    mars_days = int(mars_text.replace(':', '').split()[0])

    print(f"A Martian year is {mars_days} Earth days according to Nasa's website.")

![image](https://github.com/user-attachments/assets/5ce845c4-8746-4936-8ff5-d9594e4ef534)

**Step 6: Save the Data**

Export the DataFrame to a CSV file.

Write the data to a CSV
    
    mars_df.to_csv('mars_data.csv', index=False)

    browser.quit()
