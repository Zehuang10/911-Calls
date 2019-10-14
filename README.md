# 911 Calls Capstone Project

For this capstone project we will be analyzing some 911 call data from [Kaggle](https://www.kaggle.com/mchirico/montcoalert). The data contains the following fields:

* lat : String variable, Latitude
* lng: String variable, Longitude
* desc: String variable, Description of the Emergency Call
* zip: String variable, Zipcode
* title: String variable, Title
* timeStamp: String variable, YYYY-MM-DD HH:MM:SS
* twp: String variable, Township
* addr: String variable, Address
* e: String variable, Dummy variable (always 1)

Just go along with this notebook and try to complete the instructions or answer the questions in bold using your Python and Data Science skills!

## Data and Setup

____
**Import numpy and pandas**

    import numpy as np
    import pandas as pd

**Import visualization libraries and set %matplotlib inline.**

    import matplotlib.pyplot as plt
    import seaborn as sns
    sns.set_style('whitegrid')
    %matplotlib inline

**Read in the csv file as a dataframe called df**

    df = pd.read_csv('911.csv')

**Check the info() of the df**

    df.info()

**Check the head of df**

    df.head()

**What are the top 5 zipcodes for 911 calls?**

    df['zip'].value_counts().head()

**What are the top 5 townships (twp) for 911 calls?**

    df['twp'].value_counts().head()

**Take a look at the 'title' column, how many unique title codes are there?**

    df['title'].nunique()

## Creating new features

    x = df['title'].iloc[0]

    x.split(':')[0]

    df['Reason'] = df['title'].apply(lambda title: title.split(':')[0])

**What is the most common Reason for a 911 call based off of this new column?**

    df['Reason'].value_counts()

**Now use seaborn to create a countplot of 911 calls by Reason.**

    sns.countplot(x=df['Reason'],palette='viridis')

___
**Now let us begin to focus on time information. What is the data type of the objects in the timeStamp column?**

    type(df['timeStamp'][0])

**You should have seen that these timestamps are still strings. Use [pd.to_datetime](http://pandas.pydata.org/pandas-docs/stable/generated/pandas.to_datetime.html) to convert the column from strings to DateTime objects.**

    df['timeStamp'] = pd.to_datetime(df['timeStamp'])

    time = df['timeStamp'].iloc[0]

    time

**You can now grab specific attributes from a Datetime object by calling them. For example:**

    time.month_name()

    time.day_name()

    df['Hour'] = df['timeStamp'].apply(lambda time: time.hour)
    df['Month'] = df['timeStamp'].apply(lambda time: time.month)
    df['Day of Week'] = df['timeStamp'].apply(lambda time: time.dayofweek)

    df['Day of Week'].value_counts()

**Notice how the Day of Week is an integer 0-6. Use the .map() with this dictionary to map the actual string names to the day of the week:**

    dmap = {0:'Mon',1:'Tue',2:'Wed',3:'Thu',4:'Fri',5:'Sat',6:'Sun'}
    df['Day of Week'] = df['Day of Week'].map(dmap)

    df['Day of Week'].value_counts()

**Now use seaborn to create a countplot of the Day of Week column with the hue based off of the Reason column.**

    sns.countplot(x=df['Day of Week'],hue=df['Reason'])
    plt.legend(bbox_to_anchor=(1.05,1), loc=2, borderaxespad=0)

**Now do the same for Month:**

    sns.countplot(x=df['Month'],hue=df['Reason'])
    plt.legend(bbox_to_anchor=(1.05,1), loc=2, borderaxespad=0)

**Now create a gropuby object called byMonth, where you group the DataFrame by the month column and use the count() method for aggregation. Use the head() method on this returned DataFrame.**

    byMonth = df.groupby('Month').count()

    byMonth.head()

**Now create a simple plot off of the dataframe indicating the count of calls per month.**

    byMonth['Day of Week'].plot()

** Now see if you can use seaborn's lmplot() to create a linear fit on the number of calls per month. Keep in mind you may need to reset the index to a column. **

    byMonth = byMonth.reset_index()

    sns.lmplot(x='Month',y='twp',data=byMonth)

**Create a new column called 'Date' that contains the date from the timeStamp column. You'll need to use apply along with the .date() method.** 

    df['Date'] = df['timeStamp'].apply(lambda t:t.date())
    df.head()

**Now groupby this Date column with the count() aggregate and create a plot of counts of 911 calls.**

    df.groupby('Date').count()['twp'].plot()
    plt.tight_layout()

**Now recreate this plot but create 3 separate plots with each plot representing a Reason for the 911 call**

    df[df['Reason'] == 'Traffic'].groupby("Date").count()['lat'].plot()
    plt.title('Traffic')
    plt.tight_layout()

    df[df['Reason'] == 'Fire'].groupby("Date").count()['lat'].plot()
    plt.tight_layout()
    plt.title('Fire')

    df[df['Reason'] == 'EMS'].groupby("Date").count()['lat'].plot()
    plt.tight_layout()
    plt.title('EMS')

____
** Now let's move on to creating  heatmaps with seaborn and our data.

    df.groupby(by=['Day of Week','Hour']).count().head()

    dayHour = df.groupby(by=['Day of Week','Hour']).count()['Reason'].unstack()

    dayHour.head()

**Now create a HeatMap using this DataFrame.**

    plt.figure(figsize = (12,6))
    sns.heatmap(dayHour, cmap='viridis')

**Now create a clustermap using this DataFrame.**

    sns.clustermap(dayHour,cmap='Greens')

**Now repeat these same plots and operations, for a DataFrame that shows the Month as the column.**

    dayMonth = df.groupby(by=['Day of Week','Month']).count()['Reason'].unstack()
    dayMonth.head()

    plt.figure(figsize=(12,5))
    sns.heatmap(dayMonth, cmap='viridis')

    sns.clustermap(dayMonth,cmap='viridis')
