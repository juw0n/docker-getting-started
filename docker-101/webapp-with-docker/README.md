Simple web application deployment with docker

### Minimal 3 tier web application
* React frontend: Uses react query to load data from the two apis and display the result
* Node JS and Golang APIs: Both have / and /ping endpoints. / queries the Database for the current time, and /ping returns pong
* Postgres Database: An empty PostgreSQL database with no tables or data. Used to show how to set up connectivity. The API applications execute SELECT NOW() as now; to determine the current time to return.