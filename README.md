**"Products" is a system design project that I worked on which (combined with other back-end projects) replaces the database for
an e-commerse website with webscale in mind. This includes an ETL process, cleaning and formatting the data, building out a 
new architecture, making design decisions, stress testing, cloud storage and hosting**
___

*"Products"* quite literally focuses on all the data related to the products found on an e-commerce website such as
name, id, sizes, styles, descriptions, related items, pictures, etc.

Originally, much of the data came from **different** tables *ex: (the product descriptions, styles, and related items were separated)*, making the database a mess to navigate. I combined the tables into one organized and clean **mongoDB** schema that holds all the data about a Product.

During my time working on this project I became interested in **Node.js** clustering capabilities and their practical applications
to handle server requests simultaneously (something **Node.js** has trouble with, since it is written in JS).

Finally, I ran stress tests to see how much I can squeeze out of **Node** without relying on extra tools *ex: (caching and load balancing)*.


I recorded the whole learning process and my *"experiments"* in a **gist journal**. The original can be found here:

https://gist.github.com/MantasIK/1491e7192ecc0951a656cec4ba56f380



>### Wednesday, February 19th, 2020
> ____________________________________
>
>
>
> ### Goals:
>   Research docker, choose database
>
>### Results:
>   Chose mongoDB due to its fast reading speed. Started working on the schema. Didn't get to docker.
>
>### Struggles: 
>   Seem to have become rusty on modularizing mongoose models.
>   


>### Thursday, February 20th, 2020
>_________________________________
>
>### Goals: 
>Research ETL, import CSV data into mongoDB with custom _id
>
>### Results: 
>   Managed to import bits of data from the CSV files and get a custom _id, my approach was to first convert the CSV text into JSON the mongoimport it into the >database.
>
>### Struggles: 
>   MongoDB is very particular about its _id. Being able to import data and set that property to a custom value proved harder than expected. I am able to convert >small amounts of data into JSON format and then import it into the database, but when one is faced with a staggering amount of data, many simple actions like >copy/paste will not work. I began thinking about buffering and will look into and npm module called etl, which could take care of my problem as well as merging my >separate CSV files into one collection.

\


>### Friday, February 21st, 2020
>____________________________________
>
>
>
>### Goals:
>   Populate the mongo database with a cleaned up products CSV, potentially start merging and aggregating multiple collections.
>
>### Results:
>   I managed to populate said database with a clean products CSV. 
>
>### Struggles: 
>   It took a long time to figure out how to use the etl dependency and fine tune the data provided to be clean. Doing the research to find a solution took longer >than I'd hoped. I took a deeper look into a DBS management/visualization tool called DBschema and while it seems very powerful it doesn't appear to be optimized for >mongoDB. Importing files was not possible, maybe I will use it to merge my collections later on in the project.

\


>### Saturday, February 22nd, 2020
>____________________________________
>
>
>
>### Goals:
>   Import all the CSV files into the database and merge all my collections into one.
>
>### Results:
>   I managed to import all the CSV files into the database as separate CLEAN collections as well as creating an executable ETL pipeline.
>
>### Struggles: 
>   The CSV data was messy and needed to be cleaned up before importing. That took longer than expected. At first I looked into cleaning AND formatting the data >during the ETL pipeline process. While that is definitively possible, doing the research and setting it up would cost more time than I could afford and seeing how >mongoDB has built in aggregation, the natural choice was to do the merge process within the database. Maybe I will look into it in the future.

\


>### Monday, February 24th, 2020
>____________________________________
>
>
>
>### Goals:
>   Merge all my collections into one and create a backup to be used for docker.
>
>### Results:
>   Merged all my collections into one and created a backup file of clean collection. Began work on adjusting my server model to support the collection.
>
>### Struggles: 
>   I tried to aggregate two of my 6 collections and that process took 3 hours with no end in sight. I got concerned during the first hour since the two collections I >was merging were smaller in size compared to the others. I began researching different ways to aggregate my collections and consulted fellow engineers, wondering if >anyone encountered a similar problem. I began to consider pivoting out of mongoDB and going back to an SQL database but knowing the purpose of my API I didn't want >to give up on mongoDB just yet, seeing how it fits my needs perfectly. Just when I thought I couldn't find the answer to my problem, a senior engineer who had prior >experience with mongoDB mentioned that custom indexes created by me do not have to be unique, something I completely overlooked as I focused on mongos "_id" index. >Instead of searching through the entire collection, my $lookup method would instead search through the indices. My aggregation time was cut down by over 100%. Taking >anywhere from 5 minutes to 10 seconds.
\


>### Tuesday, February 25th, 2020
>____________________________________
>
>
>
>### Goals:
> Fully set up routes for the API and configure the mongoose schema as well as the queries to comply with the collection.
>
>### Results:
> Routes, schema and queries are configured. Set up pagination count limits so a client cannot request enormous amounts of data and crash the server.
>
>### Struggles: 
>   I spent the majority of the day making sure my mongoose queries do not return all properties within a document. Things like "product_id" and others that are meant >for the database configurations shouldn't be shown to the client. Instead of retrieving that data from the database and then transforming it on the server side, I >searched for a query parameter that excludes specific properties. The second major hurdle of the day was setting pagination for my "products/id/list" endpoint. >Seeing how the current implementation caps the number of entries returned to 10000, I had to make sure my api did the same, otherwise a simple query of one billion >could potentially break my server.
\


>### Wednesday, February 26th, 2020
>____________________________________
>
>
>
>### Goals:
> Research and set up docker, research and set up local stress testing.
>
>### Results:
>   I managed to set up a docker container with an unseeded mongo database that is connected to another container which houses my API server. I achieved that using >docker-compose which creates one container. The next step would be to figure out how to either: 
>
>- a) Create mongo inside a separate container and allow it to communicate with the server container by exposing its ports (comes with security risks). That would >allow me to have the freedom of adjusting the server container without ever  shutting down my database container and thus losing the data.
>
>- b) Continue to use docker-compose but do more research on how to seed the mongo container and possibly find flexible options on how to operate the server without >affecting the database.
>
>- c) For documentations sake: MongoDB provides a cloud service called Atlas that can host my database using AWS. While it provides free services, I do believe the >amount of data that can be stored for free does not meet my demands.
>
>I was also able to set up a local stress testing tool and run some test to compare my API server to the currently implemented one. As a result of those tests I >discovered that Node.js has built on clustering. Using this tool allowed my server to fork itself based on the number of cores available on the machine. The >performance increase was over 94% while under 20x the load.
>
>### Struggles: 
>  The two major issues were containerizing mongoDB and the API server while maintaining a connection as well as installing a stress testing tool. The current >solution regarding docker is not ideal and I shall continue to do research and as for the testing tool, I made the decision to go with artillery but the npm >installation kept failing. At first I believe this to be an issue on my part (possible problems with zsh and my .bashrc / .bash_profile configurations) but it turns >out the npm package for artillery has an error for a dependency of a dependency. Adding a flag that ignores it, allowed me to finally install the package.
\


>### Thursday, February 27th, 2020
>____________________________________
>
>
>
>### Goals:
>   Set up mongoDB and API server containers, deploy mongoDB container  on AWS. Mostly research how to accomplish these goals.
>
>### Results:
>   I gained a deeper understanding of docker volumes and how docker files and docker-compose work. I managed to create an EC2 instance on AWS. On top of everything I >managed to draw a road map of how to approach this final phase of my project (not counting stress testing). 
>
>- I will have to deploy a mongo container on my currently running EC2 instance (with ports open and security set up). 
>
>- The database MUST have a named docker volume mapped to it. This will allow the data to persist even if the container is shut down.
>
>- From there I will have to figure out how to connect to that database from MY host (my computer). Doing so will allow me to seed the database remotely using the >data I have on my machine.
>
>- Next I must deploy another EC2 instance with my API server container running on it. 
>
>- I have to make sure both EC2 instances and the containers within those have access to each other in order to communicate.
>
>- Which leads me to question wether or not I need to containerize mongoDB if it will the the only thing running on the EC2. A major con for the current plan is the >need to map volumes and ensure data persistence. The very nature of this approach seems counterintuitive.
>
>### Struggles: 
>   I ended up having to do a lot of research and not much practical work. The next phase of this project requires a lot of knowledge that must be 
>acquired before any meaningful progress can be made. Setting up the EC2 instance on AWS while easy on the surface, provides the user with so many options that need >to be investigated carefully as to not make a mistake or accumulate extra charges.
\


>### Friday, February 28th, 2020
>____________________________________
>
>
>
>### Goals:
>
>- Set up an EC2 instance 
>
>- Deploy and seed MongoDB on the EC2 instance
>
>### Results:
>   Initially I planned on using a docker container with MongoDB on it and pull the image from the hub into my EC2 instance. As I made progress on this step I began >to realize that from an engineering standpoint it didn't make sense. Docker containers provide a consistent work environment and the images are easy to transport, >just like git repositories. And while it makes perfect sense to containerize my database if I plan to run it on the same virtual machine as my API server, it is >absolutely redundant to run my database within a container if the only thing on that EC2 will be MongoDB. This eliminates the issue of data persistence within docker >and having to map docker volumes. I could have dockerized everything and ran both containers on one EC2 instance, but the reason I'm working on this project is to >emulate web-scalability without relying on AWS services. Setting up mongo on the virtual machine took no more than 5 minutes. Next I managed to connect my local API >server to the mongo instance on the EC2 and make postman requests. All that's left is to fire up another EC2 and run a container with my API server on it. After that >is done, I will begin stress tests and start looking into multi-server scalability.

>### Struggles: 
>   Today went by without a hitch, how lucky am I?
\


>### Saturday, February 29th, 2020
>____________________________________
>
>
>
>### Goals:
>   Deploy API server on EC2, connect to database and begin stress tests
>
>### Results:
>   I managed to deploy my API server on the EC2 using docker and connect it to my MongoDB. I began doing stress tests and the results are mediocre. Testing 100 >clients over 1 minute gave an average response time of 22ms while testing 300 clients over 1 minute resulted in an average of 2576ms per response with 191 time outs >and 11015 successes. Finally, running 500 clients over 1 minute returned an average response time of 4108ms with 3100 time outs and 8990 successes. All of this while >running the clustered version of my node express server. The current EC2 I am using has 1 core to work with so for scaling I could set up a balance loader and >multiple EC2 instances. I know that this would improve the performance of my server so for the sake of research I will launch a new EC2 with 2-4 cores and test >wether or not node clustering works on a virtual machine. If it does I should see a dramatic decrease in latency.
>
>### Struggles: 
>   Choosing the best approach to implement my server using docker was very problematic since the code within needs to reference the ip of my database. Now if I ever >restart my MongoDB EC2 instance I would have to change the ip within the docker instance that is already running on my server dedicated EC2. I ended up going the >route of always rebuilding an image locally that I would then push onto a private docker repository.
\


>### Monday, March 2nd, 2020
>____________________________________
>
>
>
>### Goals:
>   Stress test the server and deploy on a multi-core EC2 to test clustering performance. Optimize the EC2 for mongo.
>
>### Results:
>
>- **All tests are done on a randomized query with a range of 1 - 1000000 documents.**
>
>   The initial test on a 4 core 8GB EC2 with 100 clients per second for a one minute duration resulted in an average response time of 15 ms and a min/max of 14/49 >ms. At 300 clients per second for one minute the results are 16 ms average response time and a min/max of 14/262 ms. With 500 clients per second for one minute the >average response time skyrocketed to 1887 ms with a min/max of 27/2210 ms and an error rate of 43.2%. 13229 responses were successful while 10055 timed out. Needless >to say, 500 is more than the server can handle. Now after using Node.js clusters on my server and essentially bypassing the single threaded nature of node, the >performance increased dramatically. Testing on the 4 core 8GB EC2 and clustering the server with 100 clients s/min resulted in an average response time of 15 ms and >a min/max of 14/65 ms. Increasing the load to 300 clients s/min already showed signs of increased performance by maintaining an average 15 ms response time and a >min/max of 14/32 ms. Going up to 500 clients s/min we get and average response time of 16 ms and a min/max of 14/66 ms. Note the 0 number of timeouts. Going up to >800 clients s/min we see no signs of stress as the average response time remains under 20 ms at 19 ms and the min/max remaining a steady 14/61 ms. Still no timeouts >or errors. 850 clients s/min is where we begin to see errors and an inability to perform optimally. The average response time jumped to 1411 ms while the min/max  >became 24/2203. We discover an error rate of 5.8% with 46298 successful responses and  2856 timeouts. The numbers speak fo themselves. The simple act of clustering >within your express server doubles the performance even within a virtual machine. All that without taking into account possible linux optimizations for the MongoDB >service.
>
>### Struggles: 
>   While trying to optimize linux for the best mongo performance I seem to have terminated mongod inappropriately. This resulted in multiple errors that I had to dig >into by exploring the deep parts of my EC2 directory. Eventually I managed to solve the problem but in my terminal command I made the mistake of using sudo which led >me down a drain of entirely new problems that needs to get fixed.
\
