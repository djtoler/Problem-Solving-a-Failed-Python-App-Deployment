# Post-Incident Report

<p align="center">
<img src="https://github.com/djtoler/dp3-1/blob/main/assets/Screenshot%202023-09-22%20at%209.23.46%20PM.png">
</p>

##### _Our application was down for 23 minutes in a single day but we have a SLA that only allows for 15 minutes per year._ 

## Objective:
##### _Prevent downtime & signifigantly reduce downtime in the areas it cant be prevented_
## Incident Summary:

##### _The URL shortener application was down because of a recent update made by a new hire. The application error resulted in a 500 internal server error, which caused the website to be unavailable._

## What was the reason for the incident?
> ### A new hire committed version 2 of our application to the main branch, which had an incorrect usage of a JSON method. "json.loads(urls_file)" was used instead of the correct "json.load(urls_file)".

## How long was the site down or malfunctioning?
> ### The site was down for a total of 23 minutes.

## What steps were taken to resolve the incident?
### _Step 1: EBS logs were examined using the following bash command:_ 
> ```
> eb logs | grep -i -C 10 "error" > error_hunt.txt
> ```
> #### _The command above had overlapping, repetitive text so we tried the "awk" command remove them but we ended up using the "sort" command because we can logically think through how to get our result faster than researching "awk"_
> * ##### _* We want to generate the logs from our AWS Beanstalk application_
> * ##### _We want to filter the logs, returning every line that mentions "error" and 3 lines of context above and below it_
> * ##### _We want to number each of the filtered lines to preserve their original order because the "sort" command is going to order them_
> * ##### _We want to use "sort" to get access to the -u flag to simply remove duplicate lines_
> * ##### _We want "sort" to function on every line AFTER its first 3 digits and colon we attached to the begining of each line_
> * ##### _We want to re-sort each line based on its 3 digit number, which will keegive it its original log order_
> * ##### _We want to write the processed list to a new file_
> ```
> eb logs | grep -C 3 'error' | nl -w3 -s':' | sort -u -k2,2 | sort -n -k1,1 > error_hunt_filtered.txt
> ```

### _Step 2: GitHub was searched for the JSON method found in the logs:_ 
> ##### We spotted some lines mentioning errors related to JSON processing. We searched for json.loads() in our application.py file. 

### _Step 3: Compare current application.py version to the last working version_
> ##### We couldnt find anything that stood out about the current application.py file so we, searched GitHub logs using...
> ```
> git log -p
>```
> #### This is where we found of version 1 of application.py used json.load() but version2 used json.loads()

### _Step 4: Make the change to the JSON method and push the update to trigger a redeployment_
> ##### In application.py we changed json.loads() but version2 used json.load()

### _Step 5: Test our URL shortening feature again_
> ##### We go back to our application and enter a URL to be shortened and it works

## Was the incident fully resolved?
> ##### The incident was fully resolved after identifying and fixing the error in the application.py file.

## What steps would you take to prevent this from happening again?
> #### 1) To prevent this from happening again, we initiate a problem solving process that will help us identify oppertunities for immediate countermeasures, preventitive measures & a root cause...
> #### 2) We breakdown each step that happend in our incedent to identify points in our response process where we can make improvements...

> ## Old Response Process
> <p align="center">
> <img src="https://github.com/djtoler/dp3-1/blob/main/assets/5.drawio.png">
> </p>

> #### 3) We identify which problem we want to solve for
> #### 4) We ask our 5 whys and identify their causes
> #### 5) We identify the overall root cause of our problem

> #### 6) We implement 2 preventitive fixes to our CICD pipeline. This will prevent any similar incedent from causing any amount of downtime.
> #### 7) We implement 1 responsive fix that protects us from violating our SLA, by automating a process that immediatly restores our application to the last working version. This will keep downtime to a matter of seconds.
> #### 8) We implement 1 responsive improvement by automating a process that instantly sends us error logs after a server goes down. This will signigantly speed up troubleshooting if the other 3 methods happen to fail.


* > ### With these new fixes in place...
* > #### If an engineer writes bad code: _Error will be caught in their unit test that they now have to have with their code._
* > #### If bad code makes it pass the unit test: _Error will be caught in the staging enviornment that we now push our new code to before deploying to production_
* > #### If bad code makes it into the production enviornment: _A Jenkins job will be triggered that runs a script will that automatically rollback our application to the last working version_ 
* > #### If bad code makes us have to rollback our application: _DataDog will alert us that our server went down, then trigger Jenkins (through a webhook) to run a script that downloads and filters our AWS Beanstalk logs for use to troubleshoot._ 

> ## New Response Process
<p align="center">
<img src="https://github.com/djtoler/dp3-1/blob/main/assets/7.drawio.png">
</p>
