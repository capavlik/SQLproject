What are your risk areas? Identify and describe them.

When I am writing code to answer any question, I always run the code in small pieces to make sure each section is doing what I want before I combine them
together, then I also re-analyze the whole result to make sure it makes sense.

For instance, when trying to get the orders per category among countries and cities, I was coming up with an improbable high number of counts, and realized
I hadn't told the function to only count rows with transactions > 0, so it was just counting all the rows. This in itself is interesting, but not what
I was looking for. It was corrected by parsing the data correctly in the cte that was calling up the count.

I also describe in the data cleaning section several times when deleting duplicate rows where you should run the original query that identified the duplicates
again afterward to confirm that they had been deleted. As well, as you are running the code to drop the rows, you can make note of how many rows were dropped
and check that against how many you thought would be dropped by the original query you wrote to fing them.
