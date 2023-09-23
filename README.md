# Post-Incident Report

<p align="center">
<img src="https://github.com/djtoler/dp3-1/blob/main/assets/Screenshot%202023-09-22%20at%209.23.46%20PM.png">
</p>
<p align="center">
<img src="https://github.com/djtoler/dp3-1/blob/main/assets/500error.PNG" width="50%">
</p>


#### _Our application was down for 23 minutes in a single day but we have a SLA that only allows for 15 minutes per year._ 

<p align="center">
<img src="https://github.com/djtoler/dp3-1/blob/main/assets/Screenshot%202023-09-22%20at%209.24.05%20PM.png">
</p>

#### _Prevent downtime or signifigantly reduce downtime in the areas it cant be prevented_

<p align="center">
<img src="https://github.com/djtoler/dp3-1/blob/main/assets/Screenshot%202023-09-22%20at%209.25.16%20PM.png">
</p>

| Wrong JSON Method                   | Correct JSON Method                 |
| ----------------------------------- | ----------------------------------- |
| ![aaaaaa.png](https://github.com/djtoler/dp3-1/blob/main/assets/loads.PNG) | ![aaaaaa.png](https://github.com/djtoler/dp3-1/blob/main/assets/load.PNG) | 
#### _The URL shortener application was down because of a recent update made by a new hire. The application error resulted in a 500 internal server error, which caused the website to be unavailable._
#### _A new hire committed version 2 of our application to the main branch, which had an incorrect usage of a JSON method._
> ```
> json.loads(urls_file)  #Wrong method
> json.load(urls_file)   #Correct method
> ```
#### _The first method takes a JSON string. The second method takes a file. Since the first method was used, the new hire essentially tried to pass a string into a method that expects a file. This caused a server error that made our site unavailable_


<p align="center">
<img src="https://github.com/djtoler/dp3-1/blob/main/assets/Screenshot%202023-09-22%20at%209.27.15%20PM.png">
</p>

<p align="center">
<img src="https://github.com/djtoler/dp3-1/blob/main/assets/Screenshot%202023-09-22%20at%209.46.20%20PM.png">
</p>

#### _The site was down for a total of 23 minutes._
<p align="center">
<img src="https://github.com/djtoler/dp3-1/blob/main/assets/Screenshot%202023-09-22%20at%209.46.59%20PM.png">
</p>


#### _Step 1: EBS logs were examined using the following bash command:_ 
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
> | Version2                            | Version1                 |
> | ----------------------------------- | ----------------------------------- |
> | ![aaaaaa.png](https://github.com/djtoler/dp3-1/blob/main/assets/loads.PNG) | ![aaaaaa.png](https://github.com/djtoler/dp3-1/blob/main/assets/load.PNG) | 
> ##### In application.py we changed json.loads() but version2 used json.load()
> ##### We go back to our application and enter a URL to be shortened and it works
> <p align="center">
> <img src="https://github.com/djtoler/dp3-1/blob/main/assets/dp3-1.PNG">
> </p>

<p align="center">
<img src="https://github.com/djtoler/dp3-1/blob/main/assets/Screenshot%202023-09-22%20at%209.47.23%20PM.png">
</p>

##### The incident was fully resolved after identifying and fixing the error in the application.py file.

<p align="center">
<img src="https://github.com/djtoler/dp3-1/blob/main/assets/Screenshot%202023-09-22%20at%2010.51.40%20PM.png">
</p>

#### To prevent this from happening again...

##### 1) We initiate a problem solving process that will help us identify oppertunities for immediate countermeasures, preventitive measures & a root cause...
##### 2) We breakdown each step that happend in our incedent to identify points in our response process where we can make improvements. Our process looked like this...

> <p align="center">
> <img src="https://github.com/djtoler/dp3-1/blob/main/assets/5.drawio.png">
> </p>

##### 3) We identify which problem we want to solve for
##### 4) We ask our 5 whys and identify their causes
##### 5) We identify the overall root cause of our problem

> ### _Problem Solving_
> <p align="center">
> <img src="https://github.com/djtoler/dp3-1/blob/main/assets/6.drawio.png">
> </p>

##### 6) We implement 2 preventitive fixes to our CICD pipeline. This will prevent any similar incedent from causing any amount of downtime.
##### 7) We implement 1 responsive fix that protects us from violating our SLA, by automating a process that immediatly restores our application to the last working version. This will keep downtime to a matter of seconds.
##### 8) We implement 1 responsive improvement by automating a process that instantly sends us error logs after a server goes down. This will signigantly speed up troubleshooting if the other 3 methods happen to fail.

#### Our new error response process currently looks like this:

<p align="center">
<img src="https://github.com/djtoler/dp3-1/blob/main/assets/7.drawio.png">
</p>

#### Our new error response process explained:
> ##### If an engineer writes bad code: _Error will be caught in their unit test that they now have to have with their code._
> ##### If bad code makes it pass the unit test: _Error will be caught in the staging enviornment that we now push our new code to before deploying to production_
> ##### If bad code makes it into the production enviornment: _A Jenkins job will be triggered that runs a script will that automatically rollback our application to the last working version_ 
> ##### If bad code makes us have to rollback our application: _DataDog will alert us that our server went down, then trigger Jenkins (through a webhook) to run a script that downloads and filters our AWS Beanstalk logs for use to troubleshoot._ 
