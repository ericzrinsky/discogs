
# Generate a Spotify Playlist From Aggregated Affinity Data Stored In a Relational Database

<a name="top"></a>

<a name="overview"></a>

## Overview

Modern music listeners often find themselves jumping back and forth between multiple mediums in a single day. This may entail playing terrestrial or satellite radio in the car, listening to music on a streaming service like **Spotify** while at work, or playing vinyl records on a turntable at home. 

**Spotify's** “Discover Weekly” feature does an adequate job of exposing listeners to music they may be unfamiliar with based on their listening history and implied musical tastes. This approach achieves limited success. 

The generated playlist often includes music that the listener already knows. If you regularly listen to music that spans similar genres, you may find lots of repetition in your “Discover Weekly” playlist. After all, there are only so many bands making each type of music and **Spotify's** algorithms may not even realize a particular artist is related. 

![Overview](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i45.png "Discover Weekly")

In addition, the playlist itself is typically around 2 hours in length and short enough that it can be consumed in a single workday. This may leave listeners waiting an entire week for **Spotify** to generate the next "Discover Weekly" playlist. Even worse, the new "Discover Weekly" playlist may fail to engage a listener at all.

I began to think about my own musical tastes and how different mediums affect my listening behavior and preferences. As an avid record collector, I find myself curating my collection carefully—vinyl isn’t cheap anymore! With that in mind, I assume that this type of purchase behavior holds true for most collectors. Very few people spend $20-30 dollars for a new record unless it's something they really want.

I took this thought one step further. People share music from **Spotify** all the time, but because it's "free" and there is a low barrier to entry, it almost becomes a reflex. Sometimes the music is engaging and well worth the listen. Other times, the music is nothing special or so prevalent that it seems like everyone has already heard it. 

How much of this lackluster sharing behavior is a result of over saturation and over exposure **Spotify's** algorithmic approach? If there was an easy way to capture the items in someone's record collection and use that data to inform **Spotify** decisions, it might help to generate playlists that hit the mark more often.

I set to the task of testing this hypothesis.

### Goals

This project will attempt to accomplish the following goals:

1. Socially-source vinyl record affinity data from other music aficionados
2. Assemble a relational database of artist and album information 
3. Use SQL queries to interact with with the database to pull out the information that I need
3. Create a web-application that interacts with **Spotify's** REST API with the aid of **Postman** and by consulting <a href="https://www.linkedin.com/in/ankit-sobti/"><b>Ankit Sobti</b></a>'s guide
4. Generate new **Spotify** playlists on the fly with socially-sourced vinyl-record affinity data that excludes artists from my own record collection
5. Document the entire process in a project guide that is thorough, but easy to follow

## Contents

* [Overview](#overview)
* [Requirements](#requirements)
	* [Memberships](#memberships)
	* [Software](#software)
	* [Files](#files)
* [Read The Doc](#read)
	* [Gather Data Resources](#gather)
	* [Create A Relational Database For The Collection](#create)
	* [Lookup Artist Codes in Spotify](#lookup)
	* [Register A Client ID For A Spotify Application ](#register)
* [Appendix](#appendix)
	* [Install SQLite](#install)
	* [Enjoy](#enjoy)
	* [Thanks](#thanks)

<a name="requirements"></a>

## Requirements 

Before you attempt to build this project, please verify that you have the following items:

<a name="memberships"></a>

### Memberships

1. [Discogs](https://www.discogs.com)
2. [Spotify](https://www.spotify.com/us/)
3. [Spotify For Developers](https://developer.spotify.com/dashboard)

<a name="software"></a>

### Software

1. [SQLite (Command Line Tool)](https://sqlite.org/download.html)
2. [SQLite Studio](https://sqlitestudio.pl/index.rvt)
2. [Postman](https://www.getpostman.com/downloads/)
3. [Spotify Music Player](https://www.spotify.com/us/download/other/)
4. Terminal (with [Xcode Developer Tools](https://developer.apple.com/xcode/) Installed)

<a name="files"></a>

### Files

1. [Eric’s Record Collection](http://elearning.monetate.net.s3.amazonaws.com/z/records/datasets/Eric%27s%20Record%20Collection.csv) (CSV)
2. [Leigh’s Record Collection](http://elearning.monetate.net.s3.amazonaws.com/z/records/datasets/Leigh%27s%20Record%20Collection.csv) (CSV)
3. [Steve’s Record Collection](http://elearning.monetate.net.s3.amazonaws.com/z/records/datasets/Steve%27s%20Record%20Collection.csv) (CSV)
4. [Paul's Record Collection](http://elearning.monetate.net.s3.amazonaws.com/z/records/datasets/Paul%27s%20Record%20Collection.csv) (CSV)

[Back To Top](#top)

<a name="read"></a>

## Read The Doc

<a name="gather"></a>

### Gather Data Resources

In order to construct a relational database, we need datasets to populate it. It may seem counter-intuitive, but to generate a playlist that exposes me to music that I'm less likely to know, I determined that I needed to start with my own musical affinity data. This was easy to come by since I have [catalogued my entire record collection](https://www.discogs.com/user/ericmz23/collection) on the **Discogs** website. To export a personal collection on **Discogs**, log in and click the "Export" link in the navbar.

![Discogs - Collection Screen](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i3.png "Discogs")

Click the "Collection" radio button and then click the "Request Data Export" button.

![Discogs - Collection Screen](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i28.png "Collection")

**Discogs** emails a link to the .CSV file when it's ready to download. 

With my own collection data in-hand, I needed to gather additional collections from friends who also log their record collections on **Discogs**. To do this, I posted a plea on **Facebook**.

![Discogs - Collection Screen](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i29.png "Collection")

Within a few moments, I received three additional datasets from supportive friends and I was ready to build a relational database.

[Back To Top](#top)

<a name="create"></a>

### Create A Relational Database For The Collection

Before we can import anything, we need a database and that means we must first generate a database file. We'll do that in the the **Terminal** with the help of the **SQLite** command line tool. 

Please note, if you haven't already installed the **SQLite** command line tool, you need to do that first. Detailed instructions are available in the [Appendix](#install) of this guide.

Launch the **Terminal** and initialize **SQLite** with the command below.

```console
sqlite3
```

Create a project repository within your "Documents" folder (or some other folder that you can easily find) first. Once it's created, use the command below (by filling in the appropriate path to the folder you created) to navigate to the project in the **Terminal**: 

```console
cd Documents/xxFolderxx/xxDatabaseFolderxx
```

Next, create a new database with the following command:

```console
sqlite3 record_collection.db;
```

The database file is now ready and we can exit the **Terminal** and launch **SQLiteStudio**. From here, we need to add our new database file ("record_collection.db") to **SQLiteStudio**. To do that, click **Database** —> **Add a database** in the navbar.

![SQLiteStudio](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i30.png "Add A Database")

Browse to the database file, give your database a name (we used "Record Collection"), and then click the "OK" button.

![SQLiteStudio](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i31.png "Add A Database")

Verify that you have all 4 record collection .CSV files. Those files include:

* Eric's Record Collection.csv
* Leigh’s Record Collection.csv
* Steve's Record Collection.csv
* Paul's Record Collection.csv

Take the steps below to import each of the files into **SQLiteStudio** for use in the database.
 
1. Double-click the "Record Collection" database in the "Databases" panel
2. Click "Tables" under "Record Collection" to highlight it
3. Click **Tools** —> **Import** in the navbar.

![SQLiteStudio](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i32.png "Import Table")

Start with the .CSV file named "Eric's Record Collection.csv." 

In SQL, datasets are organized in "tables." Thus, any .CSV file that we import is referred to as a table. Give the table for "Eric's Record Collection" a name. We used "eric_records" for the first file and followed the same schema for each subsequent file. Then, click the "Continue" button.

![SQLiteStudio](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i33.png "Import Table")

The subsequent screen allows you to adjust how **SQLiteStudio** imports and processes your data. Follow the steps below to configure the new table.

1. Browse to the file for the first dataset on your computer. 
2. Verify that it says "CSV" in the "Data source type" drop-down. 
3. Click the checkbox for "First line represents CSV column names." 
4. Verify that ", (comma)" is set for the "Field separator" drop-down. 
5. Click the "Done" button. 

![SQLiteStudio](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i34.png "Import Table")

Repeat this process for the remaining 3 .CSV files:

* "Leigh’s Record Collection" —> "leigh_records"
* "Steve's Record Collection" —> "steve_records"
* "Paul's Record Collection" —> "paul_records"

Once you're finished with the import process, define the "Data type" for each column in the tables. Use the "data types" below for the columns in the "eric_records" table and then repeat this process for the 3 remaining tables.

<table>
	<tr>
		<th>Name</th>
		<th>Data type</th>
	</tr>	
	<tr>
		<td>Catalog</td>
		<td>STRING</td>
	</tr>
		<tr>
		<td>Artist</td>
		<td>STRING</td>
	</tr>		
		<tr>
		<td>Title</td>
		<td>STRING</td>
	</tr>
	</tr>		
		<tr>
		<td>Label</td>
		<td>STRING</td>
	</tr>		
	</tr>		
		<tr>
		<td>Format</td>
		<td>STRING</td>
	</tr>
	</tr>		
		<tr>
		<td>Rating</td>
		<td>STRING</td>
	</tr>	
	</tr>		
		<tr>
		<td>Released</td>
		<td>INTEGER</td>
	</tr>
	</tr>		
		<tr>
		<td>release_id</td>
		<td>INTEGER</td>
	</tr>
	</tr>		
		<tr>
		<td>CollectionFolder</td>
		<td>STRING</td>
	</tr>				
	</tr>		
		<tr>
		<td>Date Added</td>
		<td>DATETIME</td>
	</tr>
	</tr>		
		<tr>
		<td>Collection Media Condition</td>
		<td>STRING</td>
	</tr>		
	</tr>		
		<tr>
		<td>Collection Sleeve Condition</td>
		<td>STRING</td>
	</tr>	
	</tr>		
		<tr>
		<td>Collection Notes</td>
		<td>STRING</td>
	</tr>	
</table>	

![SQLiteStudio](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i35.png "Data Type")

Now that the data is in place and the columns are appropriately defined, it's time to experiment with the database. To do that, launch the "SQL Editor" in **SQLiteStudio**. Click **Tools** —> **Open SQL Editor** in the navbar.

![SQLiteStudio](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i36.png "Open SQL Editor")

Before we run any complicated queries in the "SQL Editor," we should first verify that we can readily access all of the data in the tables. We'll do that with a few basic UNION parameters in the query. 

Paste the code below into the "SQL Editor":

```SQL
SELECT artist, 
       title 
FROM   eric_records AS e 
UNION 
SELECT artist, 
       title 
FROM   leigh_records AS l 
UNION 
SELECT artist, 
       title 
FROM   steve_records AS s 
UNION 
SELECT artist, 
       title 
FROM   paul_records AS p 
ORDER  BY artist; 
```

If the query was successful, you should see "1462" for the "Total rows loaded" at the top of your results and each row should contain data for the "Artist" and "Title" columns.

![SQLiteStudio](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i37.png "UNION Query - All")

Let's tweak the query to only include artists in the results since that's what we'll need in order to generate our playlists in **Spotify**.

Paste the code below into the "SQL Editor":

```SQL
SELECT artist
FROM   eric_records AS e 
UNION 
SELECT artist
FROM   leigh_records AS l 
UNION 
SELECT artist
FROM   steve_records AS s 
UNION 
SELECT artist
FROM   paul_records AS p 
ORDER  BY artist; 
```

If your query was successful, you should now see "882" for the "Total rows loaded:" since it is likely that some artists had more than one album (or "Title") associated with them. Your results should also only display the "Artist" column

![SQLiteStudio](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i38.png "UNION Query - Artists")

This gets a us a little bit closer to our end result, but 882 is still unwieldy for a **Spotify** playlist. 

Let's dig into one of the goals for this project: 

* Generate new **Spotify** playlists on the fly with socially-sourced vinyl-record affinity data that excludes artists from my own record collection

In order to accomplish that result, we need to compare the "Artists" column in the "eric_records" table to each of the other tables ("leigh_records," "steve_records," and "paul_records") individually, exclude the artists found in both the "eric_records" table and comparison table, and then add those results to a new table called "new_artists." It sounds confusing, but it will make more sense after we run our SQL queries. 

Right click "Tables" in the "Databases" panel and then click "Create A Table."

![SQLiteStudio](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i39.png "Create A New Table")

Type "new_artists" in the "Table name:" textbox and then click the "Add column" button.

![SQLiteStudio](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i40.png "Add a column")

Next, configure the column in the "new_artists" table with the instructions below:

1. Type "artist" in the "Column name:" textbox. 
2. Type "STRING" in the "Data type:" checkbox.
3. Click the "OK" button.

![SQLiteStudio](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i41.png "Data Type")

The "new_artists" table is now ready to accept data. 

Return to the "SQL Editor." Tweak the code to include an "EXCEPT" parameter that evaluates when an artist name in the "Artist" column is found in the "eric_records" table and comparison table. This comparison and exclusion process repeats with each subsequent record collection table. To make things a little more interesting, we will also add a "WHERE" parameter to each comparison to only consider vinyl records (and their associated artist) released after 2010. Finally, we'll insert all of the results into the "new_artists" table.

Paste the code below into the "SQL Editor":

```SQL
INSERT INTO new_artists 
SELECT DISTINCT artist AS Artist, 
                released 
FROM   leigh_records AS l 
WHERE  released > 2010 
EXCEPT 
SELECT DISTINCT artist AS Artist, 
                released 
FROM   eric_records AS e 
WHERE  released > 2010 
UNION 
SELECT DISTINCT artist AS Artist, 
                released 
FROM   paul_records AS l 
WHERE  released > 2010 
EXCEPT 
SELECT DISTINCT artist AS Artist, 
                released 
FROM   eric_records AS e 
WHERE  released > 2010 
UNION 
SELECT DISTINCT artist AS Artist, 
                released 
FROM   steve_records AS l 
WHERE  released > 2010 
EXCEPT 
SELECT DISTINCT artist AS Artist, 
                released 
FROM   eric_records AS e 
WHERE  released > 2010 
ORDER  BY artist; 
```

If the request was successful, you should see 294 rows contained within the "new_artists" table. We've eliminated 588 rows with our query, but 294 is still too large for our purposes. 

Since the results of the previous query are now store within a table (new_artists), we can run one more additional query to limit the results to 30 entries and randomize the results. This allows us to generate a manageable and differentiated list of artists with each new query.

Paste the code below into the "SQL Editor":
<a name="final"></a>
```SQL
SELECT DISTINCT artist 
FROM   new_artists 
ORDER  BY Random() 
LIMIT  30; 
```

**SQLiteStudio** returned the following artists with the query:

```
Orchid, G.L.O.S.S., Royal Headache, 
Czarface, MF Doom, New Order, 
St. Paul & The Broken Bones, The Cure, Minus The Bear
The Civil Wars, Siouxsie & The Banshees, Indecision,
The Impalers, Hot Chip, Neil Young & Crazy Horse,
Cryptic Slaughter, The Devil Makes Three, The Pillows,
Glassjaw, The Specials, Built To Spill, 
The Both, Ostraca, Pestilence,
Snapcase, Boygenius, Preoccupations,
Fang, Sleep, Minor Threat, Mandolin Orange
```

Now that we have the list of artists, we're ready to gather "Spotify Artist" codes for use in the calls to the **Spotify** API.

[Back To Top](#top)

<a name="lookup"></a>

### Look Up Artist Codes in Spotify

There isn't a way to gather "Artist Codes" within the mobile or desktop versions of the **Spotify** application; however, we can get what we need if we use the web-application. 

Navigate to [https://open.spotify.com](https://open.spotify.com) and log in.

![Spotify](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i42.png "Spotify Web Application")

Click "Search" and type the name of the first artist in the "Start typing..." textbox.

![Spotify](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i43.png "Spotify Web Application")

Browse to the appropriate artist listing in the search results and copy the alphanumeric "Artist Code" at the end of the URL in the address bar.

![Spotify](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i44.png "Artist Code")

Repeat this process until you have obtained all 30 of the "Artist Codes."

|Number|Artist|Artist Code|
|--- |--- |--- |
|1|Orchid|6tEdQbmg3bKE6IjmH5hO9d|
|2|G.L.O.S.S.|2s4gtd98phMFZf7dMagxjU|
|3|Royal Headache|01Jsi7Q2a1GdLvNShahaj1|
|4|Czarface, MF Doom|4John8fJ3LKqFho0pselVr|
|5|New Order|0yNLKJebCb8Aueb54LYya3|
|6|St. Paul & The Broken Bones|4fXkvh05wFhuH77MfD4m9o|
|7|The Cure|7bu3H8JO7d0UbMoVzbo70s|
|8|The Civil Wars|6J7rw7NELJUCThPbAfyLIE|
|9|Siouxsie & The Banshees|1n65zfwYIj5kKEtNgxUlWb|
|10|The Impalers|5KL1Z3v4Q9zOMh74WEg7UT|
|11|Hot Chip|37uLId6Z5ZXCx19vuruvv5|
|12|Neil Young & Crazy Horse|6v8FB84lnmJs434UJf2Mrm|
|13|Cryptic Slaughter|20ml1U2ibB2HPnPpWrJIKT|
|14|The Devil Makes Three|63knPlGzLHTNDf1J78Fvte|
|15|Glassjaw|7nt6S4klYHg4I7Q4lTSmc0|
|16|The Specials|6xnvNmSzmeOE1bLKnYXKW3|
|17|Built To Spill|3kbBWco9PZ5eSQsNScwG6U|
|18|The Both|6nH7HjT8QPbZI2wnTjtJui|
|19|Ostraca|12QhXwxSPz6xu42dDkDuCV|
|20|Pestilence|6JcFn4PlXFuXmhRXpOpsan|
|21|Snapcase|1egTA9mNgTwglPEQLmMd9W|
|22|Boygenius|5BRORKnC2HD5xhgUyR31SH|
|23|Preoccupations|2bs3QE2ZMBjmb0QTqAjCj3|
|24|Fang|6vBFJ3JwYIzw3nRxy4vpbq|
|25|Sleep|4Mt6w4tDGiPgV5q6JWPlrI|
|26|Minor Threat|07PiZYrhllpSXtELkUxlrf|
|27|Mandolin Orange|675tsBPpaZtqyiBwEf3ZEP|
|28|The Pillows|6ilYV5oF8whllOnm4VZlYR|
|29|Indecision|3WdlM6O8p5wxmx3p7hrPHM|
|30|Minus The Bear|0YQBN02bmZvwGNrrWsg2sT|

We'll need to insert all of the "Artist Codes" into an array of strings during a future step, so let's take care of that now:

```
["6tEdQbmg3bKE6IjmH5hO9d", "2s4gtd98phMFZf7dMagxjU", 
"01Jsi7Q2a1GdLvNShahaj1", "4John8fJ3LKqFho0pselVr", 
"0yNLKJebCb8Aueb54LYya3", "4fXkvh05wFhuH77MfD4m9o", 
"7bu3H8JO7d0UbMoVzbo70s", "6J7rw7NELJUCThPbAfyLIE", 
"1n65zfwYIj5kKEtNgxUlWb", "5KL1Z3v4Q9zOMh74WEg7UT", 
"37uLId6Z5ZXCx19vuruvv5", "6v8FB84lnmJs434UJf2Mrm", 
"20ml1U2ibB2HPnPpWrJIKT", "63knPlGzLHTNDf1J78Fvte", 
"7nt6S4klYHg4I7Q4lTSmc0", "6xnvNmSzmeOE1bLKnYXKW3", 
"3kbBWco9PZ5eSQsNScwG6U", "6nH7HjT8QPbZI2wnTjtJui", 
"12QhXwxSPz6xu42dDkDuCV", "6JcFn4PlXFuXmhRXpOpsan", 
"1egTA9mNgTwglPEQLmMd9W", "5BRORKnC2HD5xhgUyR31SH", 
"2bs3QE2ZMBjmb0QTqAjCj3", "6vBFJ3JwYIzw3nRxy4vpbq", 
"4Mt6w4tDGiPgV5q6JWPlrI", "07PiZYrhllpSXtELkUxlrf", 
"675tsBPpaZtqyiBwEf3ZEP", "6ilYV5oF8whllOnm4VZlYR", 
"3WdlM6O8p5wxmx3p7hrPHM", "0YQBN02bmZvwGNrrWsg2sT"]
```
	
We've got what we need to build an application that generates a **Spotify** playlist from affinity data and we're ready to register an application on the [Spotify For Developers Portal](https://developer.spotify.com/dashboard).	

[Back To Top](#top)

<a name="register"></a>

### Register A Client ID For A Spotify Application 

If you haven't already done so, register for access to the [Spotify For Developers Portal](https://developer.spotify.com/dashboard). After you have gained access, navigate to the dashboard at the following address:

```
https://developer.spotify.com/dashboard/applications
```

We need to create a "Client ID" for the web application we will build in this step. Click the "Create A Client ID Button."

![Spotify For Developers - Dashboard](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i4.png "Create A Client ID")

In "Step 1," provide **Spotify** with details about the application and what it will do. When you're finished, click the "Next" button.

![Spotify For Developers - Create A Client ID](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i5.png "Step 1")

Since this is not a commercial application, click the "No" button in "Step 2."

![Spotify For Developers - Create A Client ID](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i6.png "Step 2")

After reading the "Developer Terms of Service," "Branding Guidelines," and "Privacy Policy" in "Step 3," click the checkboxes next to each item and then click the "Submit" button.

![Spotify For Developers - Create A Client ID](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i7.png "Step 3")

We have now created our "Client ID" and the application is registered with **Spotify**.

![Spotify For Developers - Create A Client ID](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i8.png "Registration Complete")

It's time to build.

[Back To Top](#top)

<a name="register"></a>

### Use Postman to Access the Spotify API

Now that the application is registered with the **Spotify For Developers Portal**, we have the permissions in place to access the **Spotify** API. 

Please note, the steps that follow were informed by <a href="https://www.linkedin.com/in/ankit-sobti/"><b>Ankit Sobti</b></a>'s guide for generating **Spotify** playlists with **Postman**. Though his instructions provide a good starting point, they are somewhat light on details and assume a more advanced understanding of REST APIs in general. 

In a new tab (leave the window for the **Spotify for Developers Portal** open), browse to the **[Postman Blog](https://blog.getpostman.com/2016/11/09/generate-spotify-playlists-using-a-postman-collection/)** and clone Ankit's "Collection" and "Environment" to Postman by clicking the "Run in Postman" button.

![Postman Blog](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i9.png "Open In Postman")

Launch **Postman** and then click the drop-down for "Environments." Then, click the "SpotifyGenV1.template" from the list.

![Postman](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i10.png "Environment Selection")

Click the "Environment Quick Look" button.

![Postman](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i11.png "Environment Quick Look")

In the new window, click the "Edit" link in the "SpotifyGenV1.template" header.

![Postman](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i12.png "Edit")

Return to the **Spotify For Developers Portal**, copy the "Client ID" and "Client Secret," and paste them into their respective fields in **Postman**. Then, click the "Update" button.

![Postman](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i13.png "Manage Environments")

Please note, the "Client Secret" text-boxes are blurred in the screenshot above to protect the security of the application.

Return to the **Spotify For Developers Portal** once more and then click the "Edit Settings" button.

![Spotify For Developers](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i14.png "Edit Settings")

Paste the link below into the "Redirect URIs" textbox and then click the "Add" button. After you're finished, click the "Save" button at the bottom of the window.

```
https://www.getpostman.com/oauth2/callback
```

![Spotify For Developers](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i15.png "Edit Settings")

Return to **Postman** and click the "Get Auth Key" under the "Spotify Playlist Generator" collection and then click the "Authorization" tab. 

![Postman](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i16.png "New Request")

Click "OAuth 2.0" from the "Type" drop-down and then click the "Generate New Access Token" button.

![Postman](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i17.png "New Access Token")

Fill in the details below for each textbox in the "Get New Access Token" modal:

* Token Name: ```postman-spotify```
* Auth URL: ```https://accounts.spotify.com/authorize```
* Access Token URL: ```https://accounts.spotify.com/api/token```
* Client ID: ```client_id from Spotify For Developers Portal```
* Client Secret: ```client_secret from Spotify For Developers Portal```
* Scope: ```playlist-read-private playlist-modify-private```
* ```Grant Type: Authorization Code```

When you're finished, click the "Request Token" button.

![Postman](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i19.png "Get New Access Token")

You may be asked to log into your **Spotify** account. Provide your **Spotify** username and password to login in. In the new modal, scroll to the bottom, copy the "refresh_token" and then click the "Use Token" button.

![Postman](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i20.png "Get New Access Token") 

Click the "Environment Quick Look" button.

![Postman](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i21.png "Environment Quick Look") 

Click the "Edit" button next to the "SpotifyGenV1.template" header.

![Postman](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i22.png "Environment Quick Look") 

Paste the token in your clipboard into the "refresh_token" textboxes and fill out the remainder of the fields with the following details:

* user_id: ```your Spotify username```
* country_code: ``country code in ISO 3166-1 format``
* N: ```defaults to 5 and provides this number of tracks from each related artist.```
* artists: ```List of artists generated in an array of strings. One is selected at random as the key for the generated playlist```
						
We'll re-use our array from the "[Lookup Artist Codes in Spotify](#lookup) step above to populate our request:

```
["6tEdQbmg3bKE6IjmH5hO9d", "2s4gtd98phMFZf7dMagxjU", 
"01Jsi7Q2a1GdLvNShahaj1", "4John8fJ3LKqFho0pselVr", 
"0yNLKJebCb8Aueb54LYya3", "4fXkvh05wFhuH77MfD4m9o", 
"7bu3H8JO7d0UbMoVzbo70s", "6J7rw7NELJUCThPbAfyLIE", 
"1n65zfwYIj5kKEtNgxUlWb", "5KL1Z3v4Q9zOMh74WEg7UT", 
"37uLId6Z5ZXCx19vuruvv5", "6v8FB84lnmJs434UJf2Mrm", 
"20ml1U2ibB2HPnPpWrJIKT", "63knPlGzLHTNDf1J78Fvte", 
"7nt6S4klYHg4I7Q4lTSmc0", "6xnvNmSzmeOE1bLKnYXKW3", 
"3kbBWco9PZ5eSQsNScwG6U", "6nH7HjT8QPbZI2wnTjtJui", 
"12QhXwxSPz6xu42dDkDuCV", "6JcFn4PlXFuXmhRXpOpsan", 
"1egTA9mNgTwglPEQLmMd9W", "5BRORKnC2HD5xhgUyR31SH", 
"2bs3QE2ZMBjmb0QTqAjCj3", "6vBFJ3JwYIzw3nRxy4vpbq", 
"4Mt6w4tDGiPgV5q6JWPlrI", "07PiZYrhllpSXtELkUxlrf", 
"675tsBPpaZtqyiBwEf3ZEP", "6ilYV5oF8whllOnm4VZlYR", 
"3WdlM6O8p5wxmx3p7hrPHM", "0YQBN02bmZvwGNrrWsg2sT"]
```

Paste it into the "artists" textbox and then click the "Update" button.

![Postman](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i23.png "Update Environment") 

Click the "Runner" button to launch the "Collection Runner."

![Postman](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i24.png "Collection Runner") 

Click "Spotify Playlist Generator" under "All Collections," select "SpotifyGenV1.template" from the "Environment" drop-down, and then click the "Start Run" button. 

![Postman](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i25.png "Start Run")

"Collection Runner" should display results where all requests passed and no requests failed.

![Postman](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i26.png "Success")

Launch **Spotify** and at the top of your "Playlists" list you should also see the newly generated playlist.

![Spotify](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i27.png "New Playlist")

Any time you'd like to generate a new playlist and discover new artists, launch "Collection Runner" and replay the "Spotify Playlist Generator" collection. When the results aren't interesting or unique any longer, swap out artist codes with new batches of artists generated from the final [query of the "Record Collection" database](#final) as needed.

[Back To Top](#top)

<a name="appendix"></a>

## Appendix

[Back To Top](#top)
<a name="install"></a>

### Install SQLite

Although we did most of the SQL work in **SQLiteStudio**, we used the **SQLite** command line tool to initialize our database file. There are many SQL application options available, but **SQLite** is free, easy to use, and lightweight. 

Please note, you will also need XCode Command Line Tools Installed on your computer to instal **SQLite**. If you don't have that installed, you can enter the following command in **Terminal** to install it:

```
xcode-select --install
```

If you're unfamiliar with the terminal, [MakeUseOf](https://www.makeuseof.com) has a great [guide for beginners](https://www.makeuseof.com/tag/beginners-guide-mac-terminal/). 

With Xcode installed, you can download **SQLite** [here](https://www.sqlite.org/download.html). Once you've downloaded the file, unzip it and return to the **Terminal**. 

![Terminal Window](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i1.png "Terminal Window")

Within the **Terminal**, enter the following code to access your "Downloads" folder.

```console
cd Downloads/
```

Next, access the folder for the file you just unzipped. In this instance, the folder is named <code>sqlite-autoconf-3290000</code>. Paste the command below into the **Terminal** to change the directory. Remember to swap out the file name if your's doesn't match.

```console
cd sqlite-autoconf-3290000
```

Next, enter the command below to begin the installation:

```console
./configure --prefix=/usr/local
```

When the process completes, enter the last command to finish the installation:

```console
make
```

It may take a few moments to complete, but once it does, you can verify that your installation was successful with the command below:

```console
sqlite3
```

![Terminal - Install Successful](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i2.png "Install Successful")

The **SQLite** command line tool is now installed and ready to generate database files.

[Back To Top](#top)

<a name="enjoy"></a>

### Enjoy

You can listen to the playlists that I generated for this project on **Spotify** with the links below:

* [Built To Spill Mix](https://open.spotify.com/playlist/3pdKPOX2FafbulICMm6I0m?si=wfqA9Js1RdeLLmvFkN_HIQ) - Indie Rock
* [New Order Mix](https://open.spotify.com/playlist/0DOwHUcfuubIIJdlH1Xi0o?si=rG2a0yDuRAeIxNzpyfT74Q) - New Wave
* [Mandolin Orange Mix](https://open.spotify.com/playlist/7C5UhAteTywXcxWy52rONP?si=z_D_7vW-RtKR1wyhPVpbsw) - Alt-Country/Americana
* [Glassjaw Mix](https://open.spotify.com/playlist/6x0nWb95oBzBJUWsEwoNMI?si=_08rDXF5SICew50socKzng) - Hardcore 
* [Minor Threat Mix](https://open.spotify.com/playlist/0k3v01z7krUjs0iD7UtRFY?si=32BPD4DnQu-UtfSwCIRr4g) - Punk Rock
* [The Impalers Mix](https://open.spotify.com/playlist/7cPGCkAXeSXcaFt3CLQDnX?si=7JBcP61eQSuJGz0Z9gM48g) - Reggae
* [CZARFACE Mix](https://open.spotify.com/playlist/0havuxrBJUzAhDoCgNqdib?si=0do_PuN1SDWZZtoWm4SWtA) - Rap/Hip-Hop
* [Minus The Bear Mix](https://open.spotify.com/playlist/3kvfPdi38KvOjEVm55KF5V?si=KLY_nm5fSwWFE2sXxgMPjw) - Emo
* [St. Paul & The Broken Bones Mix](https://open.spotify.com/playlist/49pxs8Sm1SjY2laTfbw1gp?si=ccY9Qd6zR_KGyC1pMKlMiA) - Soul
* [Neil Young Mix](https://open.spotify.com/playlist/5n2GWs9vSineMNcsTh4QkD?si=G10tG0MqTnyyTYroFHX2-g) - Folk

[Back To Top](#top)

<a name="thanks"></a>

### Thanks

Many thanks go out to my good friends (Stephen Dyer, Paul Caddaciottu, and Leigh Stuckey) who quickly volunteered to help me and provided their record collection data for my experimentation.

[Back To Top](#top)
