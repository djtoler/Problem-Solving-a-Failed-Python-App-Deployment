<p align="center">
<img src="https://github.com/djtoler/dp3-1/blob/main/assets/Screenshot%202023-09-23%20at%208.56.51%20AM.png">
</p>

<p align="center">
<img src="https://github.com/djtoler/dp3-1/blob/main/assets/Screenshot%202023-09-22%20at%209.23.46%20PM.png">
</p>

### _Our application was down for 23 minutes in a single day but we have a SLA that only allows for 15 minutes per year._ 

<p align="center">
<img src="https://github.com/djtoler/dp3-1/blob/main/assets/Screenshot%202023-09-22%20at%209.24.05%20PM.png">
</p>

### _Prevent downtime or signifigantly reduce downtime in the areas it cant be prevented, by improving our incident response process._

<p align="center">
<img src="https://github.com/djtoler/dp3-1/blob/main/assets/Screenshot%202023-09-22%20at%209.25.16%20PM.png">
</p>

### _The URL shortener application was down because of a recent update made by a new hire. The application error resulted in a 500 internal server error, which caused the website to be unavailable._ 
> <p align="left">
> <img src="https://github.com/djtoler/dp3-1/blob/main/assets/500error.PNG" width="70%">
> </p>

> ##### _A new hire committed version 2 of our application to the main branch, which had an incorrect usage of a JSON method._
> ```
> json.loads(urls_file)  #Wrong method
> json.load(urls_file)   #Correct method
> ```
> #### _Below, we can see that the code is nearly identical  besides an single, additional wrong letter (.loads vs .load) in version 2._
> 
> | Wrong JSON Method (v2)                  | Correct JSON Method (v1)                |
> | ----------------------------------- | ----------------------------------- |
> | ![aaaaaa.png](https://github.com/djtoler/dp3-1/blob/main/assets/loads.PNG) | ![aaaaaa.png](https://github.com/djtoler/dp3-1/blob/main/assets/load.PNG) |
> ##### _The first method requires a JSON string. The second method requires a file. Since the first method was used, the new hire tried to pass a string into a method that expects a file._

#### Because this method cant work with a file, our application cant complete this task and our server responds with an error that made our application unavailable.

<p align="center">
<img src="https://github.com/djtoler/dp3-1/blob/main/assets/Screenshot%202023-09-22%20at%209.27.15%20PM.png">
</p>

<p align="center">
<img src="https://github.com/djtoler/dp3-1/blob/main/assets/Screenshot%202023-09-22%20at%209.46.20%20PM.png">
</p>

#### _The site was down for a total of 23 minutes._
<p align="left">
<img src="https://github.com/djtoler/dp3-1/blob/main/assets/Screenshot%202023-09-22%20at%209.46.59%20PM.png">
</p>


#### _Step 1: EBS logs were examined using the following bash command:_ 
> ```
> eb logs | grep -i -C 5 "error" > error_hunt.txt
> ```

> * ##### This command  will pull the most recent logs from outlr application using "ebs logs"
> * ##### #We take the output of the "ebs logs" command and run the "grep" command on it that will return us every line that has the word "error" on it.
> * ##### We use the "-i" flag to make it case insensitive so that we account for rvery form an error can be output in
> * ##### To give ourselves a little more context to help troubleshooting, we use the "-C 5" flag to also capture the the 5 lines before and after the line that actually has "error" on it


> #### _The output from that command had overlapping, repetitive text so we tried the "awk" command remove them but we ended up using the "sort" command because we can logically think through how to get our result faster than researching "awk"_
> * ##### _We want to generate the logs from our AWS Beanstalk application_
> * ##### _We want to filter the logs, returning every line that mentions "error" and 3 lines of context above and below it_
> * ##### _We want to number each of the filtered lines to preserve their original order because the "sort" command is going to order them_
> * ##### _We want to use "sort" to get access to the -u flag to simply remove duplicate lines_
> * ##### _We want "sort" to function on every line AFTER its first 3 digits and colon we attached to the begining of each line_
> * ##### _We want to re-sort each line based on its 3 digit number, which will give it its original log order_
> * ##### _We want to write the processed list to a new file_
> ```
> eb logs | grep -C 3 'error' | nl -w3 -s':' | sort -u -k2,2 | sort -n -k1,1 > error_hunt_filtered.txt
> ```

#### _Step 2: GitHub was searched for the JSON method found in the logs:_ 
> ##### We spotted some lines mentioning errors related to JSON processing. We searched for json.loads() in our application.py file.
> > <p align="center">
> <img src="https://github.com/djtoler/dp3-1/blob/main/assets/Screenshot%202023-09-23%20at%2012.40.17%20AM.png" width="75%">
> </p>

#### _Step 3: Compare current application.py version to the last working version_
> ##### We couldnt find anything that stood out about the current application.py file, so we searched GitHub logs using...
> ```
> git log -p
>```
> #### This is where we found of version 1 of application.py used json.load() but version2 used json.loads()
> | Version 2                           | Version 1                 |
> | ----------------------------------- | ----------------------------------- |
> | ![aaaaaa.png](https://github.com/djtoler/dp3-1/blob/main/assets/loads.PNG) | ![aaaaaa.png](https://github.com/djtoler/dp3-1/blob/main/assets/load.PNG) | 
> ##### In application.py we changed json.loads() from version2 to json.load() and pushed our updated code.
> ##### We go back to our application and enter a URL to be shortened and it works
> <p align="left">
> <img src="https://github.com/djtoler/dp3-1/blob/main/assets/dp3-1.PNG" width="75%">
> </p>

<p align="center">
<img src="https://github.com/djtoler/dp3-1/blob/main/assets/Screenshot%202023-09-22%20at%209.47.23%20PM.png">
</p>

##### The incident was fully resolved after identifying and fixing the error in the application.py file.

<p align="center">
<img src="https://github.com/djtoler/dp3-1/blob/main/assets/Screenshot%202023-09-22%20at%2010.51.40%20PM.png">
</p>

#### _FIRST: Breakdown our current incedent response and prevention process..._

> ##### 1) We initiate a problem solving process that will help us identify oppertunities for immediate countermeasures, preventitive measures & a root cause...
> ##### 2) We layout each step that happend in our incedent to identify points in our response process where we can make improvements. Our process looked like this...

> <p align="center">
> <img src="https://github.com/djtoler/dp3-1/blob/main/assets/5.drawio.png">
> </p>

> * #### Our Sequence of Events
> * ##### _GREEN Block 2: Why didnt our CICD pipline protect us from deploying bad code???
> * ##### _GREEN Block 3: Why did we use a deployment strategy that leaves room for our application to go down & we're in a SLA that only allows for 15 mins of downtime/yr???_
> * ##### _RED Block 1: If our downtime timer starts as soon as our application goes down, why didnt we have a way to get it back up instantly???_
> * ##### _RED Block 2 & 3: If we had to use the EBS UI and search Google for a bash command to pull and filter our log, why isnt that automated???_
> * ##### _RED Block 4: Is there a faster, more reliable, automated way to have our error logs interpreted???_
> * ##### _RED Block 5: Is there a faster, more reliable, automated way to compare our applications file versions???_
> * ##### _RED Block 6: Is there a faster but reliable, automated way to fix, push and test possible error fixes in our applications??? Can we test all possible fixes simtaniously, in safe isolated enviornments and use a rolling deployment strategy for a fix that actually worked???_

> * #### Users Sequence of Events
> * ##### _RED Block 1-3: Can we handle these errors in a way that keeps our server up? Can we still give our user what they came for but in a different way if the first method fails???_

#### _SECOND: Work through each section of our problem solving process..._

> ##### 3) We identify which problem we want to solve for
> ##### 4) We ask our 5 whys and identify their causes
> ##### 5) We identify the overall root cause of our problem

> <p align="center">
> <img src="https://github.com/djtoler/dp3-1/blob/main/assets/6.drawio.png">
> </p>

#### _THIRD: Generate 4 stratagies to help us prevent downtime or signifigantly reduce downtime in the areas it cant be prevented..._

> ##### 6) We implement 2 preventitive fixes to our CICD pipeline. This will prevent any similar incedent from causing any amount of downtime.
> ##### 7) We implement 1 responsive fix that protects us from violating our SLA, by automating a process that immediatly restores our application to the last working version. This will keep downtime to a matter of seconds.
> ##### 8) We implement 1 responsive improvement by automating a process that instantly sends us error logs after a server goes down. This will signigantly speed up troubleshooting if the other 3 methods happen to fail.

<p align="center">
<img src="https://github.com/djtoler/dp3-1/blob/main/assets/Screenshot%202023-09-23%20at%208.21.19%20AM.png">
</p>

> <p align="center">
> <img src="https://github.com/djtoler/dp3-1/blob/main/assets/Screenshot%202023-09-23%20at%208.58.35%20AM.png" width="75%">
> </p>

#### Our new error response process currently looks like this:

<p align="center">
<img src="https://github.com/djtoler/dp3-1/blob/main/assets/newDiagram1.drawio.png">
</p>

---


#### Explination of our new error response process:
##### If an engineer writes bad code:
*  ##### _Error will be caught in their unit test that they now have to have with their code._
##### If bad code makes it pass the unit test:
*  ##### _Error will be caught in the staging enviornment that we now push our new code to before deploying to production_
##### If bad code makes it into the production enviornment:
*  ##### _A Jenkins job will be triggered that runs a script will that automatically rollback our application to the last working version_ 
##### If bad code makes us have to rollback our application:
*  ##### _DataDog will alert us that our server went down, then trigger Jenkins (through a webhook) to run a script that downloads and filters our AWS Beanstalk logs for use to troubleshoot._
  
---
