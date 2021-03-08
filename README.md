# ITP449EDA
Spring2021 ITP449 EDA Project
import pandas as pd
import numpy as np
import matplotlib as mpl
import matplotlib.pyplot as plt
import seaborn as sn
#importing the dataset from github:
url = 'https://raw.githubusercontent.com/fivethirtyeight/data/master/college-majors/recent-grads.csv'
df = pd.read_csv(url)
# understand the basic information about this dataset:
# print the first 5 columns of the dataset and obtain a description about the dataset:
pd.set_option('display.max_columns', None)
print(df.head())
print(df.shape)
print(df.info())
# note the dataset contains 21 columns and 173 rows. There is one empty cell under the columns "Men", "Women", and "ShareWomen" respectively
# hypothesis1: there are more males with physical science, life science and engineering majors, and more females with humanities, arts and social science majors
# identify and exclude the row with null values in columns 'Men' and 'Women':
copy1 = df.copy()
copy1.dropna(subset=['Men', 'Women'], inplace=True)
# calculate the sum of students of the same major category, based on their genders: 
men = copy1.groupby('Major_category')['Men'].sum()
women = copy1.groupby('Major_category')['Women'].sum()
# make a grouped bar chart to compare the number of men and women for each major category: 
x = copy1['Major_category'].unique()
plt.figure(figsize=(10, 8))
w = 0.4
bar1 = np.arange(len(x))
bar2 = [i+w for i in bar1]
plt.bar(bar1, men, w, label='men')
plt.bar(bar2, women,w, label='women')
plt.xlabel('Major Categories')
plt.ylabel('Number of Students')
plt.xticks(bar1+w/2, x, rotation=90)
plt.legend()
plt.title('Number of Students by Gender and Major Category')
plt.show()
# hypothesis2: it is easier to find a fulltime job for graduates of a business or science major than for those of an arts or humanities major
copy2=df.copy()
# measure the percentage of graduates with a full time job by major category:
major_category = copy2['Major_category'].unique()
graduates = copy2.groupby('Major_category')['Total'].sum()
fulltime = copy2.groupby('Major_category')['Full_time'].sum()
fulltime_rate = fulltime/graduates
# plot a sorted bar graph showing the full time rate from high to low by category:
fulltime_rate.sort_values(ascending=True, inplace=True)
print(fulltime_rate)
ax = fulltime_rate.plot.barh(figsize=(10,8))
plt.xlabel('Fulltime Employment Rate')
plt.ylabel('Major Category')
plt.title('Fulltime Employment Rate by Major Category in Descending Order')
# hypothesis3: there is a correlation between the income level and the number of people choosing that major
copy3 = df.copy()
# first check if there are outliers(defined as less than Q1-1.5IQR or more than Q3+1.5IQR):
Q1 = copy3.Median.quantile(0.25)
Q3 = copy3.Median.quantile(0.75)
print(Q1)
print(Q3)
IQR = Q3 - Q1
lower_limit = Q1 - (1.5*IQR)
upper_limit = Q3 + (1.5*IQR)
print(lower_limit)
print(upper_limit)
# drop the outliers before analyzing the relationships:
drop_indexes = copy3[(copy3['Median'] > upper_limit)|(copy3['Median'] < lower_limit)].index
copy3.drop(drop_indexes, inplace=True)
print(copy3.info())
# plot graphs to show the relationship between the number of graduates of a major and the respective incomes:
# a graph showing the relationship between number of graduates and the median income:
fig = plt.figure(figsize=(20,6))
plt.title('Graphs Showing the Relationships Between Income Levels and Number of Graduates')
ax1 = fig.add_subplot(1,3,1)
sn.regplot(x=copy3['Median'],y=copy3['Total'] , color='g')
plt.xlabel('Median Income')
plt.ylabel('Number of Graduates')
# a graph showing the relationship between number of graduates and the bottom 25% income:
ax2 = fig.add_subplot(1,3,2)
sn.regplot(x=copy3['P25th'],y=copy3['Total'] , color='r')
plt.xlabel('Bottom 25% Income')
plt.ylabel('Number of Graduates')
# a graph showing the relationship between number of graduates and the top 25% income:
ax3 = fig.add_subplot(1,3,3)
sn.regplot(x=copy3['P75th'],y=copy3['Total'] , color='y')
plt.xlabel('Top 25% Income')
plt.ylabel('Number of Graduates')
plt.show()
