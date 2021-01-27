# Covid-evolution-Illinois

README file from Laura&#39;s part

1. News Webscraping

In the folder News\_webscraping there is a Jupiter notebook called &quot;Chicago\_suntimes\_webscraping\_and\_sentiment\_analysis.ipynb&quot; that collects all news articles from Chicago Suntimes published between Jan 1 and April 30. All these news articles are saved in a CSV file called &quot;Chicago\_suntimes.csv&quot; with the columns &quot;Body&quot;, &quot;Date&quot; and &quot;Title&quot;. The Jupiter notebook further calculates, for each date, the percentage of news that are related to Covid by identifying whether the news article (body + title), after making all words lowercase, contains any of the words &#39;covid&#39;, &#39;coronavirus&#39;, &#39;pandemic&#39;, &#39;virus&#39;, &#39;covid-19&#39; or &#39;covid19&#39;. Finally, the Jupiter notebook assigns, for each day, an average polarity score between -1 and 1 to the news articles using the TextBlob natural language processing package ([https://textblob.readthedocs.io/en/dev/](https://textblob.readthedocs.io/en/dev/)). The final results are stored in a CSV called &quot;Chicago\_suntimes\_date\_perc\_score.csv&quot; with the columns &quot;Date&quot;, &quot;Percentage Covid&quot; and &quot;Polarity Score&quot;. The results are also used to plot the figure called &quot;plot\_perc\_score.png&quot;, which is then annotated in Viewer on a MacBook to get the figure &quot;plot\_perc\_score\_annotated.png&quot;.

1. Google Mobility Data ([https://www.gstatic.com/covid19/mobility/Global\_Mobility\_Report.csv?cachebust=c050b74b9ee831a7](https://www.gstatic.com/covid19/mobility/Global_Mobility_Report.csv?cachebust=c050b74b9ee831a7))

The Global mobility data, which shows visits to various categories (Grocery\_pharmacy, Parks, Residential, Transit, Retail\_recreation, Workplace) around the world from Febr 15 until the present, was downloaded and opened in Vim editor in bash. All entries not for Cook county were deleted and labeled according to the categories were added. The resulting csv file was opened in Numbers (the equivalent of Excel on a Macbook) and a plot of the Mobility in the different categories from Febr 15 until April 11 was created.

1. SAFEGRAPH Social Distancing Data

The Safegraph Database is a huge database that aggregates GPS pings from millions of phones across the US. It has, among others, datasets tracking foot traffic to thousands of points of interest (restaurants, cafes, malls etc) in the US, as well as a dataset tracking how long people stay at home, how far they travel etc. Due to time restrictions, only the latter data set was used.

In order to get access to the data, Laura Tociu joined the Slack of Safegraph and got access to a Google doc with all the links:

![](RackMultipart20210127-4-zpgnp2_html_4de02c595565ccc0.png)

The data is organized in terms of Census Block Groups (CBG), which are small areas delimited by polygons, used by SafeGraph to perform their data collection. The Census Block dataset is available here:

[https://www.safegraph.com/open-census-data](https://www.safegraph.com/open-census-data)

The crucial file from above has the name &quot; cbg\_geographic\_data.csv&quot; and contains the latitude and longitude of the center of the polygon enclosing the CBG area.

Since our dataset is based on ZIP codes, it was necessary to map the CBG codes to ZIP codes. This was done using the latitude and longitudes of the CBG&#39;s, and a dataset ([https://www.kaggle.com/joeleichter/us-zip-codes-with-lat-and-long](https://www.kaggle.com/joeleichter/us-zip-codes-with-lat-and-long)) downloaded from Kaggle that maps ZIP codes to a latitude / longitude pair.

NOTE: When we downloaded the dataset from kaggle, it had a different name, &quot;datasets\_5391\_8097\_zip\_lat\_long.csv&quot;, and now that name cannot be found at all on Google, but it should be the same dataset. The file &quot;datasets\_5391\_8097\_zip\_lat\_long.csv&quot; is provided in our upload.

Due to the size of the Social Distancing data (~ 60 GB), and the fact that we were only looking for Cook county ZIP Codes, the first step in data cleaning was to truncate the file &quot;datasets\_5391\_8097\_zip\_lat\_long.csv&quot; to only keep the rows corresponding to the Illinois ZIP codes, 60000 - 63000 (it was easy to do after ordering by ZIP code).

Then, to further minimize code execution time, we looked at the minimum and maximum latitudes in the truncated &quot;datasets\_5391\_8097\_zip\_lat\_long.csv&quot; file. We ordered and deleted all rows in the file &quot;cbg\_geographic\_data.csv&quot; that were far from those latitudes. Then we looked at minimum and maximum longitudes in the &quot;datasets\_5391\_8097\_zip\_lat\_long.csv&quot; file and further removed entries in &quot;cbg\_geographic\_data.csv&quot; that were far from those longitudes. The end result was a smaller file &quot;datasets\_5391\_8097\_zip\_lat\_long.csv&quot; that contains only Illinois ZIP codes, and a much smaller file &quot;cbg\_geographic\_data.csv&quot; that contains only the CBG codes with geographic location inside or very close to Illinois.

All of the above was done manually in Vim. To even further reduce execution time, only the Cook county ZIP codes were extracted from the file &quot;Location\_DataWorld.csv&quot; and saved in a file &quot;cook\_zipcodes.csv&quot;, also manually. All these truncated files are available in the upload.

Finally, a Jupyter notebook, &quot;Limit\_to\_Cook.ipynb&quot;, was run to match CBG codes to the ZIP codes in Cook county by calculating, for each CBG latitude and longitude, the closest ZIP code latitude and longitude. Subsequently, social distancing metrics we thought could be useful were extracted from the SAFEGRAPH database for each CBG code in Cook county, and the ZIP code was added as another column. Since there are many CBG codes per ZIP code, the metrics were aggregated per ZIP code. The latter two steps, even though they are included in the &quot;Limit\_to\_Cook.ipynb&quot; notebook, were run on the Midway cluster as separate Python scripts and took \&gt; 24 hours to run.

The final result of the social distancing data cleaning is a CSV file called &quot;Social\_distancing\_data\_Cook\_febr\_april.csv&quot; that contains data for 170 ZIP codes in Cook county. Some data was missing or looked anomalous in the SAFEGRAPH files so the Python script was only successful in cleaning data for 170 out of about 220 ZIP codes in Cook county. The social distancing metrics in the file &quot;Social\_distancing\_data\_Cook\_febr\_april.csv&quot; are:

[&#39;zipcode&#39;, &#39;date&#39;, &#39;device\_count&#39;, &#39;distance\_traveled\_from\_home&#39;, &#39;completely\_home\_device\_count&#39;, &#39;median\_home\_dwell\_time&#39;, &#39;part\_time\_work\_behavior\_devices&#39;, &#39;full\_time\_work\_behavior\_devices&#39;, &#39;delivery\_behavior\_devices&#39;, &#39;median\_non\_home\_dwell\_time&#39;, &#39;candidate\_device\_count&#39;, &#39;median\_percentage\_time\_home&#39;]

In order to visualize data better in Tableau, a series of queries were performed in the Python notebook called &quot;MY\_SQL\_queries\_for\_Tableaux\_visuals.ipynb&quot;. The results of the queries, &quot;Average\_delivery\_behavior\_devices\_per\_10\_days.csv&quot;, &quot;Average\_full\_time\_work\_behavior\_devices\_per\_10\_days.csv&quot; and &quot;Average\_perc\_time\_home\_per\_10\_days.csv&quot; were manually concatenated into the file &quot;Social\_distancing\_per\_10\_days.csv&quot;, which was used in Tableaux to visualize the data using a Metric filter.
