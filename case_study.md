
# Case Study: Generating a Spotify Playlist from An Aggregated Database of Vinyl Records

<a name="top"></a>

<a name="overview"></a>

## Overview

Modern music listeners often find themselves jumping back and forth between musical-mediums in a single day. This may entail playing a CD in the car, listening to music on Spotify while at work, or playing vinyl records on a turntable at home. 

With their “Discover Weekly” playlist, Spotify does an adequate job of exposing listeners to music they may or may not be familiar with based on their listening history and implied musical tastes; however, the playlist Spotify generates with their algorithms tends to be on the shorter side and can be easily consumed in a single workday. This leaves listeners waiting an entire week for Spotify to generate a new playlist.

As an avid record collector, I find myself curating my musical tastes carefully—vinyl isn’t cheap anymore! I can only assume that most collectors (unless they’re working with a near limitless amount of money) take the same prudent approach to curating their collection as I do.

## Contents

* [Overview](#overview)
* [Requirements](#requirements)
	* [Memberships](membership)
	* [Software](software)
	* [Files](#files)
* [Read The Doc](#read)
	* [Gather Data Resources](#gather)
	* [Create A Relational Database For The Collection](#create)
	* [Lookup Artist Codes in Spotify](#lookup)
	* [Register A Client ID For A Spotify Application ](#register)
* [Appendix](#appendix)
	* [Install SQLite](#install)
	* [Enjoy](#enjoy)

<a name="requirements"></a>

## Requirements 

Before you attempt this Case Study, please verify that you have the following items:

<a name="memberships"></a>

### Memberships

1. [Discogs](https://www.discogs.com)
2. [Spotify](https://www.spotify.com/us/)
3. [Spotify For Developer](https://developer.spotify.com/dashboard)

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

Before we can create a relational database, we need to gather datasets (or tables) that will populate it. To begin, we'll start with my own personal record collection logged in [Discogs](https://www.discogs.com/user/ericmz23/collection). Click the "Export" link in the navbar.

![Discogs - Collection Screen](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i3.png "Discogs")

Click the "Collection" radio button and then click the "Request Data Export" button.

![Discogs - Collection Screen](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i28.png "Collection")

Since we need more data to make our database interesting, I socially-sourced a few more **Discogs** record collections from friends on Facebook.

![Discogs - Collection Screen](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i29.png "Collection")

[Back To Top](#top)

<a name="create"></a>

### Create A Relational Database For The Collection

Now that we have gathered the datasets that we will use for our database, we need to begin the process of importing them into our SQL application. All of this will be done at the command line level in our Terminal application. Launch terminal and initialize **SQLite Studio**.

```console
sqlite3
```

Now that SQLite is initialized, we need to import our datasets into it. This allows SQLite to convert our flat CSV files into tables that we can eventually import into a relational database. We have 4 files that we will work with and we have identified each of their paths as follows:

* Eric's Record Collection: ```/Users/ericzrinsky/Downloads/Eric's Record Collection.csv```
* Leigh’s Record Collection: ```/Users/ericzrinsky/Downloads/Leigh's Record Collection.csv```
Steve's Record Collection: ```/Users/ericzrinsky/Downloads/Steve's Record Collection.csv```
* Paul's Record Collection: ```/Users/ericzrinsky/Downloads/Paul's Record Collection.csv```

In order to import these files, we need to first create a database to hold them. Within **Terminal**, enter the following command to navigate to a folder we previously created to host our Case Study project: 

```console
cd Documents/collection/database
```

Then, create a new database with the following command:

```console
sqlite3 record_collection.db;
```

Now that we have our database file created, we can exit the **Terminal** and launch **SQLiteStudio**. We need to add our new database file to the application. To do that, click **Database**—>**Add a database** in the navbar.

![SQLiteStudio](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i30.png "Add A Database")

Browse to the database file we created earlier, give your database a recognizable name (we used "Record Collection"), and then click the "OK" button.

![SQLiteStudio](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i31.png "Add A Database")

Now, we need to import each of our .CSV files into **SQLiteStudio** so we can use them as tables in our database. To do that, click **Tools**—>**Import** in the navbar.

[Back To Top](#top)

<a name="lookup"></a>

### Lookup Artist Codes in Spotify

XXX

[Back To Top](#top)
<a name="register"></a>


### Register A Client ID For A Spotify Application 

After you have gained access to the Spotify for Developers portal, navigate to the dashboard at the following address:

```
https://developer.spotify.com/dashboard/applications
```

Since this is the first time we've accessed the Spotify For Developers portal, we need to first create a "Client ID" for the web application we will build in this step. Click the "Create A Client ID Button."

![Spotify For Developers - Dashboard](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i4.png "Create A Client ID")

Fill out the text boxes for "Step 1." In this step, we provide Spotify with details about our application and what it will do. When you're finished, click the "Next" button.

![Spotify For Developers - Create A Client ID](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i5.png "Step 1")

Since we are not developing this application to sell it, we can click the "No" button in "Step 2."

![Spotify For Developers - Create A Client ID](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i6.png "Step 2")

After reading the "Developer Terms of Service," "Branding Guidelines," and "Privacy Policy," click the checkboxes next to each term and then click the "Submit" button.

![Spotify For Developers - Create A Client ID](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i7.png "Step 3")

We have no successfully created our "Client ID" and our application is registered with Spotify.

![Spotify For Developers - Create A Client ID](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i8.png "Registration Complete")

[Back To Top](#top)
<a name="register"></a>

### Use Postman to Access the Spotify API

Now that our application is registered with the **Spotify For Developers Portal**, we have the permissions we need in place to access the Spotify API. 

<p class="callout info">Please note, <a href="https://www.linkedin.com/in/ankit-sobti/"><b>Ankit Sobti</b></a> created a guide for generating playlists with **Postman**. Though his instructions provide a good starting point, they are often light on details and assume as the basis for our application, but with some tweaks that we will implement for our specific use case. You can access the original version of that guide  on the <a href="https://blog.getpostman.com/2016/11/09/generate-spotify-playlists-using-a-postman-collection"><b>Postman Blog</b></a></p>


In a new tab (leave the **Spotify for Developers** open), browse to the **[Postman Blog](https://blog.getpostman.com/2016/11/09/generate-spotify-playlists-using-a-postman-collection/)**. and clone Ankit's "Collection" and "Environment" to Postman by clicking the "Run in Postman" button.

![Postman Blog](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i9.png "Open In Postman")

Launch **Postman** and then click the drop-down for "Environments." Then, click the "SpotifyGenV1.template" from the list.

![Postman](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i10.png "Environment Selection")

Then, click the "Environment Quick Look" button.

![Postman](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i11.png "Environment Quick Look")

In the new window, click the "Edit" link in the "SpotifyGenV1.template" header.

![Postman](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i12.png "Edit")

Return to the **Spotify For Developers Portal** and copy the "Client ID" and "Client Secret" and paste them it into their respective fields in **Postman**. Then, click the "Update" button.

![Postman](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i13.png "Manage Environments")

<p class="callout warning">Please note, the "Client Secret" text-boxes are blurred in the screenshot above to protect the security of the application.</p>

Return to the **Spotify For Developers Portal** once more and then click the "Edit Settings" button.

![Spotify For Developers](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i14.png "Edit Settings")

Paste the link below into the "Redirect URIs" textbox and then click the "Add" button. After you're finished, click the "Save" button at the bottom of the window.

```
https://www.getpostman.com/oauth2/callback
```

![Spotify For Developers](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i15.png "Edit Settings")

Return to **Postman** and click the "View more actions (...)" button next to the "Spotify Playlist Generator" collection and then click "Add Request."

![Postman](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i16.png "New Request")

Give your request a name and a quick description and then click the "Save to Spotify Playlist Generator" button.

![Postman](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i17.png "New Request")

Click the "Authorization" tab, click "OAuth 2.0" from the "Type" drop-down, and then click the "Generate New Access Token" button.

![Postman](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i18.png "New Access Token")

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

You may be asked to log into your Spotify account in the next step. Provide your Spotify username and password to login in. In the new modal, scroll to the bottom, copy the "refresh_token" and then click the "Use Token" button.

![Postman](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i20.png "Get New Access Token") 

Click the "Environment Quick Look" button.

![Postman](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i21.png "Environment Quick Look") 

Click the "Edit" button next to the "SpotifyGenV1.template" header.

![Postman](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i22.png "Environment Quick Look") 

Paste the "refresh_token" into the "refresh_token" textboxes and fill out the remainder of the fields with the following details:

* user_id: ```your spotify username```
* country_code: ``country code in ISO 3166-1 format``
* N: ```defaults to 5 and provides this number of tracks from an included artist in the playlist```
* artists: ```List of seed artists that we generated in the [Lookup Artist Codes in Spotify](#lookup) section above```

The table below includes the artist names and corresponding artist codes we generated from our database:

<table>
	<tr>
		<th>Artist</th>
		<th>Artist Code</th>
	</tr>
	<tr>
		<td>RVIVR</td>
		<td>1nfqdFH2ssg8oV0TYG2h6p</td>
	</tr>
	<tr>
		<td>Microwave</td>
		<td>7ptm7G8z8VVvwBnDq8fAmD</td>
	</tr>
	<tr>
		<td>RJD2</td>
		<td>1O3ZOjqFLEnbpZexcRjocn</td>
	</tr>
	<tr>
		<td>Sleater-Kinney</td>
		<td>4wLIbcoqmqI4WZHDiBxeCB</td>
	</tr>
	<tr>
		<td>Rocket From The Tombs</td>
		<td>5gjODPcOiAmqb2iPzt4cbo</td>
	</tr>
	<tr>
		<td>Pavement</td>
		<td>3inCNiUr4R6XQ3W43s9Aqi</td>	
</table>			
						
All of the "Artist Codes" must be formatted in an array of strings:

```
["1nfqdFH2ssg8oV0TYG2h6p","7ptm7G8z8VVvwBnDq8fAmD","4wLIbcoqmqI4WZHDiBxeCB","5gjODPcOiAmqb2iPzt4cbo", "3inCNiUr4R6XQ3W43s9Aqi"]
```

Paste the array of strings into the "artists"  textbox and then click the "Update" button.

![Postman](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i23.png "Update Environment") 

Click the "Runner" button to launch the "Collection Runner."

![Postman](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i24.png "Collection Runner") 

Click "Spotify Playlist Generator" under "All Collections," select "SpotifyGenV1.template" from the "Environment" drop-down, and then click the "Start Run" button. 

![Postman](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i25.png "Start Run")

If your collection runs were successful, "Collection Runner" will display 12 passed requests.

![Postman](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i26.png "Success")

Launch **Spotify** and at the top of your "Playlists" list you should also see the newly generated playlist.

![Spotify](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i27.png "New Playlist")

[Back To Top](#top)
<a name="appendix"></a>

## Appendix

[Back To Top](#top)
<a name="install"></a>

### Install SQLite

If you do not already have SQL application installed on your computer, you will need to install one before you can interact with your databases. There are many options available, but SQLite is free, easy to use, and lightweight. You can download the files required to install SQLite [here](https://www.sqlite.org/download.html).

<p class="callout info">Please note, you will need XCode Command Line Tools Installed on your computer to proceed. If you don't have that installed, you can enter the following terminal command to install it:

<pre>
xcode-select --install
</pre>
</p>

Once you've downloaded the file, unzip it, and then launch a new terminal window. If you're unfamiliar with the terminal, [MakeUseOf](https://www.makeuseof.com) has a great [guide for beginners](https://www.makeuseof.com/tag/beginners-guide-mac-terminal/). 

![Terminal Window](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i1.png "Terminal Window")

Within the terminal window, enter the following code to access your "Downloads" folder.

```console
cd Downloads/
```

Next, we need to access the folder for the file we just unzipped. In our instance, the folder name is <code>sqlite-autoconf-3290000</code>. Next, we'll paste the command below into the terminal to change the directory. Remember to swap out the file name if need be.

```console
cd sqlite-autoconf-3290000
```

Next, we need to enter the command below:

```console
./configure --prefix=/usr/local
```

When the process completes, enter the last command to finish the installation:

```console
make
```

It may take a few moments for the installation to complete, but once it does, you can verify that it was successful by using the command below:

```console
sqlite3
```

![Terminal - Install Successful](http://elearning.monetate.net.s3.amazonaws.com/z/records/img/i2.png "Install Successful")

[Back To Top](#top)
<a name="enjoy"></a>

### Enjoy

You can listen to the playlists that I created during this case study on Spotify with the links below:

* [RVIVR Mix](https://open.spotify.com/playlist/1ojzxWARZkifrZXkPrrnsE?si=Bua9OhB5QOuj7uf6kYHtkw)

[Back To Top](#top)
