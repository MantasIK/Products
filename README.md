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


Below is a gist journal that I kept in order to record my "experiments" for any curious minds out there.

https://gist.github.com/MantasIK/1491e7192ecc0951a656cec4ba56f380
