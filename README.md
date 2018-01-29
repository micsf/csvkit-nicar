Csvkit, a command-line tool for reporters
=========================================

Ever struggled with large data sets? Or need to quickly join or merge data sets without benefit of a database? We will show you how to harness the awesome power of CSVKit to wrangle large datasets on the command line. It's easy to use, fast and powerful. It's a must in every data journalist's tool box.
 
This session is good for: People who want a solution for working with multiple​​ CSV files without having to open Excel or to join or merge files without a database.

Created for the 2018 CAR Conference on March 8-11, 2018 in Chicago. By [Christian McDonald](http://github.com/critmcdonald).

This lecture assumes use of Macintosh with csvkit installed globally using Python 3.6.

## Our scenario

We have CSV files of [Mixed Beverage Gross Receipts](https://data.texas.gov/Government-and-Taxes/Mixed-Beverage-Gross-Receipts/naix-2893) for five different counties that make up the MSA that includes Austin, Texas. The data convers the December 2017 reporting period.

We want to write a story that illustrates the top alcohol sellers in each of the five counties, and some summaries for the area as a whole. But there is a catch ... the county name is not in the file. We do, however, have a lookup table to match county ID with a name.

We'll use the command-line tool [csvkit](https://csvkit.readthedocs.io) to do this work. It can handle quickly some tasks that might otherwise take many manual steps and be more prone to human error. It is NOT a complete data analysis package, but it can get you some basic answers if that is all you need.

### Our goals

- Learn some basic command-line commands to get around: pwd, cd, ls
- Inspect one of our files to learn about it
- Merge multiple files into one
- Join the merged file with the counties lookup file
- Pare down the file so we have just the columns we need
- Get some summary stats
- Order rows to see top sellers
- Filter rows to get top sellers for specific locations
- *MAYBE* csvquery for sums in counties?

## Getting around the terminal

For those unfamiliar with "the command line", Terminal is an application that uses a "Command Line Interface" (cli) to communicate with your computer. The language it is using is called Bash, even though the tool we are talking to mostly will be csvkit, which is built in Python.

Confused? Don't worry about it ... you'll do fine. And no, you won't destroy the computer. It'll be ok.

### About commands

When you look at your command prompt in Terminal, the last character is a dollar sign: $

So, to designate input you type in vs output the computer spits out, I will include the  `$` to show you type it in. But DON'T TYPE IN THE `$`.

`$ ls -l` is the command. Type just "ls -l" and then hit return.

and this is the  output:

```
105.csv                   28.csv                   
11.csv                    Mixed_Beverage_Layout.pdf
227.csv                   README.md
246.csv                   counties.csv
```

### Launch the Terminal app.

  - It may be in the applications bar: A black box with `>_` inside it.
  - You can type command-space to get a search bar, and type in Terminal. Hit return.
  - If the type is really small, you can to "command +" to make it bigger.

### pwd - print working directory

Type `$ pwd` in terminal, and it will respond with something like:

/Users/username/Documents/path_to/where_you_are

This is important, because it can get confusing about where you are on the computer. Each of those "/" is a folder, and they are the same ones that you normally browse through on your computer.

### cd - change directory

Now we are going to change our working directory to where we need to be. Because this is being written before the computers were built for the conference, the instructor will correct this for you in class. But let's pretend we know where we are going.

Type this into your terminal:

`$ cd /Users/nicar/Desktop/hands_on/csvkit-nicar2018/`

The first two segments find the logged in user, "nicar" in this case. If you were logged in as "nicar" and folder surfing on your computer, you would go to your Desktop, double-click on the "hands_on" folder, which would contain the "csvkit-nicar2018" folder, which you would then open.

What we've done instead is to "change directory" to go inside this folder in our terminal. This is where we'll stay for remainder of the class.

### ls - list

Now let's set what is inside this folder. Type this into your terminal:

`$ ls`

You should get in return something like this:

``` txt
105.csv       28.csv
11.csv        Mixed_Beverage_Layout.pdf
227.csv       README.md
246.csv       counties.csv
```

This lists all the files in the folder in two columns. There are five .csv files that start with numbers, one called counties.csv, a pdf and this README.md file.

Many cli tools like this take an argument, or flag, that changes or extends the behaivor of the command. Try:

`$ ls -l`

You should get something like:

``` txt
total 616
-rw-r--r--@ 1 christian  staff   20730 Jan 26 23:54 105.csv
-rw-r--r--@ 1 christian  staff    5066 Jan 26 23:54 11.csv
-rw-r--r--@ 1 christian  staff  184491 Jan 26 23:55 227.csv
-rw-r--r--@ 1 christian  staff   40543 Jan 26 23:55 246.csv
-rw-r--r--@ 1 christian  staff    1717 Jan 28 14:06 28.csv
-rw-r--r--@ 1 christian  staff   28583 Jan 26 21:55 Mixed_Beverage_Layout.pdf
-rw-r--r--  1 christian  staff    6152 Jan 28 14:59 README.md
-rw-r--r--@ 1 christian  staff    3886 Jan 26 22:33 counties.csv
```

This is the "long" listing that includes file permissions, owners, size, date, etc.

### wc - word count

Before we use the "wc" command, let me tell you about tab completion. When in the terminal, you often don't have to type in entire names of files or folders. You type the beginning of a file name, then hit "tab" to complete the rest. Try it with the 11.csv file.

Type in `$ wc 11` then hit tab. It should finish it out as:

`$ wc 11.csv`

Hit return and you get back:

``` txt
      25     285    5066 11.csv
```

- "25" is the number of lines
- "285" is the number of words
- "5006" is the number of bytes

Later, we'll use "wc" with an "-l" flag to get onlty the number of lines: `wc -l 11.csv`

### head

Now we know there are 25 lines in this file, let's look at a little of it.

`$ head 11.csv`

(Don't forget tab complete!)

Hit return, and you'll see the first 10 lines of the "11.csv" file run across your screen.

Scroll back and look at the top, and you can see the headers, but I have a better way below.

### More terminal

This is all the bash/terminal basics we'll cover, because csvkit is our focus. I do have a work-in-progress [cli-tools](https://github.com/utdata/cli-tools) lesson if you want further study later.

## csvkit basics

Now we'll concentrate the power of csvkit.

### csvcut -n

[csvcut](https://csvkit.readthedocs.io/en/1.0.2/tutorial/1_getting_started.html#csvcut-data-scalpel) allows you to select, delete and reorder the columns in your CSV.

It also has a special flag "-n" to get the header names. Let's use:

`$ csvcut -n 11.csv`

We get:

``` text
  1: Taxpayer_Number
  2: Taxpayer_Name
  3: Taxpayer_Address
  4: Taxpayer_City
  5: Taxpayer_State
  6: Taxpayer_Zip
  7: Taxpayer_County
  8: Location_Number
  9: Location_Name
 10: Location_Address
 11: Location_City
 12: Location_State
 13: Location_Zip
 14: Location_County
 15: TABC_Permit_Number
 16: Inside/Outside_City_Limits
 17: Responsibility_Begin_Date
 18: Responsibility_End_Date
 19: Obligation_End_Date
 20: Liquor_Receipts
 21: Wine_Receipts
 22: Beer_Receipts
 23: Cover_Charge_Receipts
 24: Total_Receipts
```

The names of the columns kinda speak for themselves. I have included the [Mixed_Beverage_Layout.pdf](Mixed_Beverage_Layout.pdf) file in case you really want to look further.

Suffice to say, the columns we are interested here are the Location columns and the Receipts columns.

### csvstack

Before we look at these further, take my word for it and know that all five numbered files have the same header, though there are more rows in some files than others. The number of the file corresponds to the county it came from, but the county name is not in the data.

We want to combine all the files into a single new file before we join with another file to get the county names.

"csvstack" merges like files on top of each other, or "stacks" them. At it's most basic, it just takes the file names as an argument. 

`$ csvstack 11.csv 28.csv 105.csv 227.csv 246.csv`

Do that, and it will stack all the text and print it to your screen. Not particurly useful. What we need to do is redirect the output to a file, which we do with the ">" command. Let's call our files "stacked.csv":

`$ csvstack 11.csv 28.csv 105.csv 227.csv 246.csv > stacked.csv`

- Do `ls` to see that "stacked.csv" was created.
- Do `wc -l 227.csv` to see how many lines were in the "227" file.
- Do `wc -l stacked.csv` to see that the "stacked" file has more.

Congrats, you just merged five files into one with a single command!

NOTEWORTHY: If these files did not have a Location_County id at all, but were named by their counties, like "bastrop.csv", you could use `csvstack -g bastrop.csv caldwell.csv > newfile.csv` to combine the files, and it would add a "group" column using the file names, so you know where they came from.

### redirecting commands, csvcut -c and csvlook

Now that we have a 1127 line file, it might make sense to just look at the top when we want to explore it. Above you redirected output with "csvstack" into another file using `>`, but we can also redirect output into another command using `|`, and in fact chain many commands together like this. That's what we'll work through here.

- start with `$ head stacked.csv` to see the first 10 lines.

"csvcut -c" allows us to select specific columns by their name or position. "csvlook" makes our output pretty. Let's work through them.

- hit the up arrow on your keyboard to get back that last command, then add to it as follows:

`$ head stacked.csv | csvcut -c Location_Name,Location_County`

You end up with:

``` text
Location_Name,Location_County
CABANA BEVERAGES INC,11
COPPER SHOT DISTILLERY,11
BONE SPIRITS LLC,11
LOBLOLLY SPIRITS,11
DERELICT AIRSHIP DISTILLERY,11
NEIGHBOR'S,11
URBAN COWBOY VENTURES,11
JOSEPH'S STEAKHOUSE,11
MORELIA MEXICAN CAFE,11
```

We can make this easier to read with "csvlook":

- hit the up arrow to get the last command, then add on "| csvlook" to get this:

`$ head stacked.csv | csvcut -c Location_Name,Location_County | csvlook`

Your output shoudl be:

``` text
| Location_Name               | Location_County |
| --------------------------- | --------------- |
| CABANA BEVERAGES INC        |              11 |
| COPPER SHOT DISTILLERY      |              11 |
| BONE SPIRITS LLC            |              11 |
| LOBLOLLY SPIRITS            |              11 |
| DERELICT AIRSHIP DISTILLERY |              11 |
| NEIGHBOR'S                  |              11 |
| URBAN COWBOY VENTURES       |              11 |
| JOSEPH'S STEAKHOUSE         |              11 |
| MORELIA MEXICAN CAFE        |              11 |
```

So, we proved the Location_County column sucks. Let's get some county names.

### csvjoin

Before we talk about the join, let's take a quick peek at the "counties.csv" file so we understand what we are doing.

`$ head -n 15 counties.csv | csvlook`

gives you this:

``` text
| id | county    | code |
| -- | --------- | ---- |
|  1 | Anderson  |    1 |
|  2 | Andrews   |    2 |
|  3 | Angelina  |    3 |
|  4 | Aransas   |    4 |
|  5 | Archer    |    5 |
|  6 | Armstrong |    6 |
|  7 | Atascosa  |    7 |
|  8 | Austin    |    8 |
|  9 | Bailey    |    9 |
| 10 | Bandera   |   10 |
| 11 | Bastrop   |   11 |
| 12 | Baylor    |   12 |
| 13 | Bee       |   13 |
| 14 | Bell      |   14 |
```

We passed the "-n 15" into "head" to change the number of lines to 15, so we could see the value for Bastrop, whose code is 11. This matches our "11.csv" filename, so we are in the right track.

Now we know that "Location_County" in "stacked.csv" matches "code" in the "counties.csv". Now we can use "csvjoin":

`$ csvjoin -c "Location_County,code" stacked.csv counties.csv > joined.csv`

Let's break that down: We use csvjoin and use -c to pass in the column names "Location_County,code". Make sure there is no space after the comma. Then we give it the two file names, in the same order as the column names. We then redirect theoutput to a file called "mixbev.csv".

Peak at the columns names:

`$ csvcut -n joined.csv`

We've added to columns at the end:

```
 25: county
 26: code
```

Take a look, using csvcut to look:

`$ csvcut -c Location_Name,county joined.csv | csvlook`

At the bottom of the file we have the Williamson county name:

``` txt
| Y'ALL DOWN HOME CAFE                               | Williamson |
| LONE ROCK BAR                                      | Williamson |
| TUSK GRILL                                         | Williamson |
```

Congratulations, you've joined to files together without creating a database.

### csvcut -c for ranges

Let's show one last function with csvcut -- to select a range of columns to print.

If we look at all the column names:

`$ csvcut -n joined.csv`

We get this:

```
  1: Taxpayer_Number
  2: Taxpayer_Name
  3: Taxpayer_Address
  4: Taxpayer_City
  5: Taxpayer_State
  6: Taxpayer_Zip
  7: Taxpayer_County
  8: Location_Number
  9: Location_Name
 10: Location_Address
 11: Location_City
 12: Location_State
 13: Location_Zip
 14: Location_County
 15: TABC_Permit_Number
 16: Inside/Outside_City_Limits
 17: Responsibility_Begin_Date
 18: Responsibility_End_Date
 19: Obligation_End_Date
 20: Liquor_Receipts
 21: Wine_Receipts
 22: Beer_Receipts
 23: Cover_Charge_Receipts
 24: Total_Receipts
 25: county
 26: code
```

We really only need the Location_, Receipts_ and county column. Let's create a file with just those, based on the column numbers listed here. You can pick and choose rows by number, and string ranges together with a hyphen.

`$ csvcut -c 8-14,20-25 joined.csv > mixbev.csv`

That created our new file. Peek at the columns: `$ csvcut -n mixbev.csv`

You now have:

``` text
  1: Location_Number
  2: Location_Name
  3: Location_Address
  4: Location_City
  5: Location_State
  6: Location_Zip
  7: Location_County
  8: Liquor_Receipts
  9: Wine_Receipts
 10: Beer_Receipts
 11: Cover_Charge_Receipts
 12: Total_Receipts
 13: county
```

## Value of what you've learned so far

Those two major functions above, csvstack and csvjoin, can often be done in csvkit much quicker, easier and more precisely than in Excel. Especially when you have a spreadsheet with more than one sheet. I once [wrote a script](https://github.com/utdata/cli-tools/blob/master/lectures/csvkit/APD%20Demographics%20data%20pipeline.ipynb) to rip apart a dozen Excel files that each had eight sheets to combine them into one file. I never would've been able to copy/paste 96 sheets together without making an error. The script takes about 30 seconds to run.

There, you've gotten value from this class.

At this point, you might be able to open up mixbev.csv in Excel and do these next steps faster, but sometimes you just need an answer or two and csvkit can do the trick, so let's explore more.

### csvstats

Now that we have our merged and joined file in "mixbev.csv", let's learn some more about it.

`csvstat mixbev.csv`

This will take a couple of seconds to run.

What can we learn from this?
- The sum of all alcohol sales in the Austin MSA in December 2017 was "$70,354,620"
- This highest Total_Reciepts value is "$869,818". We'll want to figure out who that is next.

### csvsort

Let's find out that top seller. "csvsort" will sort your output to screen or redirection into another file, but it does not change the sort order of the original file.

Let's type in all in, then break it down:

`$ csvsort -c Total_Receipts -r mixbev.csv | csvcut -c Location_Name,Location_Address,county,Total_Receipts | head | csvlook --max-column-width 20`

- `csvsort -c Total_Receipts -r` is our new command. `csvsort` is the command, and we are passing the `-c` flag and the name of a column to sort. The `-r` that follows says to reverse sort, so we have the largest number at the top. Journalists almost always want the largest number first. We have to do this first (or at least before our `head` command) so it considers the whole file.
- We pipe this into `csvcut` passing in our name, address, county and receipts fields. This is just for nice output of fields we need.
- We pass through to `head` to look at just the top 10 rows intead of all of them.
- We pass through to `csvlook` to make it pretty. I added the `--max-column-width 20` because some of the restaurant names are really long, and would break each row into multiple lines.

So, our result is this:
```
| Location_Name        | Location_Address     | county | Total_Receipts |
| -------------------- | -------------------- | ------ | -------------- |
| WLS BEVERAGE CO      | 110 E 2ND ST         | Travis |        869,818 |
| W HOTEL AUSTIN       | 200 LAVACA ST        | Travis |        581,798 |
| DH BEVERAGE LLC      | 604 BRAZOS ST        | Travis |        563,253 |
| ROSE ROOM/ 77 DEG... | 11500 ROCK ROSE AVE  | Travis |        527,145 |
| 400 BAR/CUCARACHA... | 400 E 6TH ST         | Travis |        466,017 |
| SAN JACINTO BEVER... | 98 SAN JACINTO BLVD  | Travis |        459,049 |
| THE ROOSEVELT ROO... | 307 W 5TH ST # 307B  | Travis |        436,238 |
| THE DOGWOOD DOMAIN   | 11420 ROCK ROSE A... | Travis |        419,467 |
| KUNG FU SALOON       | 11501 ROCK ROSE A... | Travis |        411,707 |
```

A quick [Google Maps search](https://goo.gl/maps/TdsMbkd3Jjm) tells me "110 E 2ND ST" is the J.W. Marriott hotel in downtown Austin. I can tell you they have lead alcohol sales in the city of Austin each month since they opened in February 2015.

### csvgrep - filter rows of data through matching

All of the results above are in Travis County, which includes Austin. What about sales in the suburban counties (Bastrop, Caldwell, Hays, Williamson)?

We can use `csvgrep` to match rows based on a pattern or regular expression. We'll use a pattern, and we'll start with Bastrop.

We are basically using the same command as the last one (so you can arrow up to get that) and editing the beginning to include the `csvgrep` part:

`$ csvgrep -c county -m "Bastrop" mixbev.csv | csvsort -c Total_Receipts -r | csvcut -c Location_Name,Location_Address,county,Total_Receipts | head | csvlook --max-column-width 20`

Let's break down the csvgrep part of this:

- `csvgrep` is the command.
- "county" is the column we want to search, so we pass that in with `-c county`.
- We are using a pattern match, so we pass in `-m` and then then the word or phrase we are searching. It could be more than one word, like "MAIN ST".
- And of course the file we are searching.

To review the order of all the commands in plain English we are: Searching the mixbev.csv file in the county column for "Bastrop", then we sort on Total_Receipts in reverse order, then we cut out just the columnds we need (and name them), then we get the top 10 results, then we make them look pretty, limiting the width of columns to 20 characters. Got it?

Now we have just Bastrop:

```
| Location_Name        | Location_Address     | county  | Total_Receipts |
| -------------------- | -------------------- | ------- | -------------- |
| LOST PINES BEVERA... | 575 HYATT LOST PI... | Bastrop |        290,195 |
| OLD TOWN RESTURAN... | 931 MAIN ST          | Bastrop |         94,519 |
| SP BASTROP THEATR... | 1600 CHESTNUT ST     | Bastrop |         43,273 |
| CHILI'S GRILL & BAR  | 734 HIGHWAY 71 W     | Bastrop |         42,059 |
| NEIGHBOR'S           | 601 CHESTNUT ST U... | Bastrop |         26,995 |
| RED ROCK STEAKHOU... | 101 S LENTZ ST       | Bastrop |         26,945 |
| VIEJO'S TACOS Y T... | 912 MAIN ST          | Bastrop |         23,349 |
| COACH Q'S SOCIAL ... | 303 MAIN ST          | Bastrop |         21,630 |
| MORELIA MEXICAN G... | 696 HIGHWAY 71 W ... | Bastrop |         20,665 |
```

We could exchange the word "Bastrop" for the other county names (or even part of the name, like "Will" to get Williamson).

But let's look at it another way. What are the top sellers outside of Austin? The `csvgrep` also has `-i` which is the inverse of a search.

`$ csvgrep -c Location_City -m "AUSTIN" -i mixbev.csv | csvsort -c Total_Receipts -r | csvcut -c Location_Name,Location_Address,Location_City,Total_Receipts | head | csvlook --max-column-width 15`

```
| Location_Name   | Location_Add... | Location_City | Total_Receipts |
| --------------- | --------------- | ------------- | -------------- |
| LOST PINES B... | 575 HYATT LO... | LOST PINES    |        290,195 |
| CHUY'S          | 4911 183A TO... | CEDAR PARK    |        172,579 |
| CHUY'S ROUND... | 2320 N INTER... | ROUND ROCK    |        165,153 |
| JACK ALLEN'S... | 2500 HOPPE TRL  | ROUND ROCK    |        148,393 |
| MAVERICKS       | 1700 GRAND A... | PFLUGERVILLE  |        147,058 |
| THIRD BASE R... | 3107 S INTER... | ROUND ROCK    |        137,628 |
| RICK'S CABARET  | 3105 S INTER... | ROUND ROCK    |        137,563 |
| TWIN PEAKS R... | 100 LOUIS HE... | ROUND ROCK    |        134,566 |
| TRATTORIA LI... | 13308 FM 150 W  | DRIFTWOOD     |        121,814 |
```

### csvsql - like peanut butter and chocolate

The [csvsql](http://csvkit.readthedocs.io/en/1.0.2/tutorial/3_power_tools.html?highlight=query#csvsql-and-sql2csv-ultimate-power) can help you import and export your csv to and from database formats. But it will also do a simple query of the data itself using the sqlite package built into csvkt. This is more just to show you can do it. (I even get a lot of python errors, but the math is sound.)

Let's sum the total sold by county:

```
$ csvsql --query "select county,sum(Total_Receipts) from mixbev group by 1;" mixbev.csv | csvlook
| county     |      Total |
| ---------- | ---------- |
| Bastrop    |    720,659 |
| Caldwell   |     63,406 |
| Hays       |  3,274,053 |
| Travis     | 59,526,973 |
| Williamson |  6,769,529 |

```

or by city:

```
$ csvsql --query "select Location_City,sum(Total_Receipts) as Total from mixbev group by 1;" mixbev.csv | csvlook
| Location_City    |      Total |
| ---------------- | ---------- |
| AUSTIN           | 58,165,637 |
| BASTROP          |    314,416 |
| BEE CAVE         |    407,434 |
| BEE CAVES        |    102,144 |
| BRIARCLIFF       |          0 |
| BUDA             |    398,563 |
| CEDAR PARK       |  1,739,899 |
| COUPLAND         |     14,760 |
| DEL VALLE        |    115,888 |
| DRIFTWOOD        |    234,303 |
| DRIPPING SPRINGS |    242,868 |
| ELGIN            |     41,419 |
| FLORENCE         |     21,512 |
| GEORGETOWN       |    913,469 |
| GRANGER          |     15,768 |
| HUTTO            |    105,223 |
| JONESTOWN        |          0 |
| KYLE             |    210,581 |
| LAGO VISTA       |     48,934 |
| LAKEWAY          |    723,034 |
| LEANDER          |    129,526 |
| LIBERTY HILL     |     76,366 |
| LOCKHART         |     63,386 |
| LOST PINES       |    290,195 |
| LULING           |         20 |
| MANOR            |     38,049 |
| PFLUGERVILLE     |    585,617 |
| RED ROCK         |     26,945 |
| ROLLINGWOOD      |     29,846 |
| ROUND ROCK       |  2,566,691 |
| SAN MARCOS       |  2,169,050 |
| SMITHVILLE       |     47,684 |
| SPICEWOOD        |    101,268 |
| SUNSET VALLEY    |    195,306 |
| TAYLOR           |     76,425 |
| VOLENTE          |      5,502 |
| WEST LAKE HILLS  |     91,059 |
| WIMBERLEY        |     45,833 |
```


## Things we won't do that's cool

- You can turn an Excel spreadsheet into a csv with [in2csv](https://csvkit.readthedocs.io/en/1.0.2/tutorial/1_getting_started.html#in2csv-the-excel-killer)

## Setting up your computer

- I recommend [conda](https://conda.io/docs/user-guide/install/index.html)

## Running Python in a browser

* python anywhere?
* Jupyter notebooks
  - The is a [bash kernel](http://slhogle.github.io/2017/bash_jupyter_notebook/) for Jupyter.

## Resources

https://source.opennews.org/articles/eleven-awesome-things-you-can-do-csvkit/