# Project text

## Step 1 - Data gathering
Because our project revolves around security people on Twitter, the first natural step was to scrape twitter for tweets regarding cyber security. To extract data from Twitter one needs a Twitter developer account.
The process of applying for a developer account was a bit tedious, so we rooted against that and used an existing personal app the team member Peter Bom Jakobsen already had in place.
With this in place we could begin extracting data.

For data extraction we used the Python library [Tweepy][1], which wraps the Twitter API for easy usage. 
The library parses the JSON output of Twitter API into objects, which made it a lot easier to get the information we needed from the Twitter.

The dataset was built from an initial list of 3546 recent tweets acquired using the following search query:

```
(infosec OR cve OR cybersec OR cybersecurity OR ransomware) -filter:retweets min_faves:10
```

ie. searching for original tweets containing the selected keywords and a minimum of ten likes. The tweets were then scraped for twitter usernames by using the simple regular expression `@\w+` to find mentions (denoted by a "@" followed by a screen name).
From this initial list of security people on twitter, we once again used Tweepy to find their friends within this Twitter sphere.

Further on in the project, the biography's and location's of these security people were gathered to examine the existence of communities within this Twitter sphere. A list of the 20 most recent tweets were also gathered for our tweeters to examine the sentiment of communities.

The data is a collection of many CSV files that in whole makes up our dataset.

### The dataset contains:
* Screen names.
* Tweets containing security words "infosec", "cve",  "cybersec", "cybersecurity" or "ransomware".
* Friends' ids and their screen names.
* Timeline tweets - 20 unfiltered tweets.
* User descriptions and locations.

Screen names is the Twitter handle of a user. This is unique for every user on Twitter.

## Step 2 - Creating a network
<!-- Explain what you're interested in -->
In the data gathering state, we used Tweepy to gather a list of security persons on twitter and their friends within the gathered Twitter sphere. As an example we can describe person two persons $A,B$ where we have the following relations

1. $A <-> B$ ie $A$ follows $B$ and $B$ follows $A$.
2. $A -> B$ ie $A$ follows $B$ whereas $B$ does not follow $A$.
3. $A <- B$ ie $B$ follows $A$ whereas $A$ does not follow $B$.

In these three instances only (1) is classified as a friendship meaning both $A$ and $B$ follow each other. In (2) and (3) no friends exist.

<!-- Describe the tool -->
This definition is important for the way we construct our graph. As a friend is only valid if both $A$ and $B$ follows each other (1), we can construct our graph as a undirected graph, where our twitter people are our nodes and their friends are edges.

This is done using the NetworkX graph method.

<!-- Here we apply the tool and create our graph. woohooo -->

As it can be seen, the graph is very compact and does not give a very good overview of the network. Therefore we apply the Force Atlas 2 layout algorithm to create a more visually pleasing graph[1]:

<!-- Here we apply the tool and create our graph. woohooo -->

<!-- discuss the outcome -->
Our undirected graph contains a total of `2050` nodes (ie. security people) and `18040` edges (ie friendships)
As it can be seen on the graph, there seems to be a clustering of high-edge nodes in the middle of the graph.

This can be confirmed by looking at the following histogram where we can clearly see that the distribution of friends follows the power law distribution.

<!-- INSERT HISTOGRAM HERE -->

Looking at the 10 twitter profiles with the most friends we can see some known people within security on Twitter:

<!-- insert top 10 profiles here in a properly formatted list -->

As an example we can see **@HackingDave** who is the founder and CEO of the well known cyber security company TrustedSec[2] within the United States. The rest of the list are also security people who are known for tweeting about cyber security.

This confirms our theory that people who tweet about security know each other and form some kind of community. This will be further explored in the next step where we explore the communities within our cyber security Twitter sphere.

## Step 3 - Communities
<!-- Explain what you're interested in -->
Cyber security is usually split into different areas of expertise. Usually this is seen as either defensive security or offensive security. Therefore we want to analyze our security Twitter sphere in regards to communities, to see if they match the common expectation or not.

The first step of this analysis is to group the security people into different communities and explore the people within these communities. To do this we apply the Louvain method to our graph to extract the best partitions of the graph. The following histogram shows the distribution of community sizes:

<!-- INSERT HISTOGRAM HERE -->

<!-- discuss the outcome -->
Once again, this distribution follows a power law distribution as we have many small communities and few large ones. Furthermore, if we take the five largest communities and sum up the nodes, we can see that `top-five-number-of-nodes / total numbers of nodes`% of our nodes are within these communities.

The initial hypothesis that our security people were split in two can not be validated with the analysis done so far, as we have more than two communities within our network. To further analyze our Twitter sphere, we now move towards exploring the members of our communities, to see if the Twitter sphere is split in other ways.

### Step 3.1 - Wordclouds
<!-- Explain what you're interested in -->
Now that we have established that our graph contain five large communities that include `top-five-number-of-nodes / total numbers of nodes`% of our nodes, we would like to explore these communities. As mentioned in the previous analysis step, we had a hypothesis that our Twitter sphere were divided into offensive and defensive security. As the analysis shown, our view of security people were far too simply. To explore the types of people in our communities we have decided to analyse the bios of ur community members.

<!-- Describe the tool -->
The best way of getting an overview of a number of Twitter profiles is to look at their bio. A Twitter bio often explains what the person behind the profile is interested about. To do this for our communities we create a word cloud for each community. This is done by first analyzing the word frequency of all the individual communities bios. The frequency of words is then used to determine the size of the word in the word cloud. The higher the frequency the larger the word. Furthermore the Inverse Document Frequency is used in conjunction with the word frequency to filter out stop words from the wordclouds.

The result of this can be seen in the following word clouds. Communities are ordered by their size, so community one is the largest and five is the smallest (of the top five communities)

<!-- Here we apply the tool -->

<!-- discuss the outcome -->
#### Community 1
The word cloud contains words such as hacker, infosec, queer, blacktechtwitter, pronoun, hacknotcrime, adhd, artist and more.
This suggests that this community is centered mainly around hacker and infosec. Furthermore it seems that many members of this community cares about their identity and social issues such as racism.

#### Community 2
The word cloud contains words such as mcafeee, malware, ransomware, cyber, kaspersky, threat, cve, intelligence, etc.
This suggests that this community is centered around anti virus and malware research.

#### Community 3
The word cloud contains words such as blockchain, socialmedia, influencer, ai, datascience, bigdata, digitaltransformation, etc.
This suggests that this community is centered around more "soft" security related topics. Most of the words in this word cloud are new terms and technologies such as blockchain, influencer, fintech and bigdata.

#### Community 4
The word cloud contains words such as words european, union, sécurité, ireland, governance, etc.
This suggests that this community is centered around security related to europe and the european union. The word cloud also has non-english words, suggesting that this community consists of non-native english speakers.

#### Community 5
The word cloud contains words such as facebook, resist, subscribe, youtube, instagram, daily, etc.
This suggests that this community is centered around social media and news related topics. This could very likely be Twitter profiles belonging to news sites that write about cyber security.
### Step 3.2 - Word cloud for location
<!-- Explain what you're interested in -->
The word cloud analysis of our communities showed that there are different communities within our Twitter sphere. Twitter is a global social network, and we therefore want to examine the location of these communities, to see if security people are also spread around the globe. We decided to include this analysis in our final project, as the fourth community was centered around the European Union. Therefore we found it interesting to examine whether or not this was related to the location of the tweeters.
🦭👍
<!-- Describe the tool -->
Once again we utilize word clouds to find the most frequent locations and match this with the information found in the previous analysis on what these security people write in their bio.
🦭👍

<!-- Here we apply the tool -->

<!-- discuss the outcome -->
As it can be seen in the word clouds, community 1, 2, 3 and 5 are primarily dominated by american residents. But community 4, that we previously discovered were dominated by words related to the European Union, is filled primarily with locations from Europe. These are locations such as Brussels, Belgium, England and Germany, and therefore supports our theory that this community is located within Europe. Certain words related to Brazil can also be found, though we are unsure why.
🦭👍

### Step 3.3 - Sentiment of communities
<!-- Explain what you're interested in -->
The communities we found previously in this notebook are very different from each other, even though all the Twitter profiles are within the cyber security domain. In the beginning of the project we were interested in knowing if security people were split in two communities, an offensive and defensive. And with this split of communities, we wanted to know if they were equally happy. The analysis of communities showed that we in fact had more communities, and that they were not divided as we thought. With this said, it is still very interesting to analyze how happy the individual communities are.  

<!-- Describe the tool -->
To describe the happiness of each community, we fetch the latest 20 tweets for each of the profiles in the communities. Using the results from the paper "Temporal Patterns of Happiness and Information in a Global Social Network: Hedonometrics and Twitter" [3], we extract known words from tweets and apply a sentiment value to each. We then find the average sentiment value for each community and compare these results to each other
<!-- Here we apply the tool -->

<!-- discuss the outcome -->
Surprisingly, the results show no significant variance in the sentiment value of our communities. They all have a sentiment value between 5.42 and 5.52 which is a very neutral sentiment value.


## Discussion
This project was conceived with the belief that the cyber security twitter sphere consisted primarily of offensive and defensive Twitter profiles. This turned out to be a very simplified version of the actual world, where we discovered that a lot of communities exist within our Twitter sphere. While the most popular Twitter profiles were well-known, the word cloud analysis discovered more diverse communities than we initially thought existed. However, the project clearly shows that Twitter communities exist, and interestingly enough, most communities consist of profiles located in USA.

With this said, the sentiment analysis did not show a significant variance between communities, and this is something that could be looked further into. Maybe more data could be included into this, including data such as replies, GIF reactions and simply more tweets. The sentiment analysis also excluded tweets written in a language other than english, as the dataset we used to compare sentiment only contained english words. Approximately 15% of tweets were not in english.

At last, maybe a larger dataset of more tweets and search words could be used to further expand upon the Twitter cyber security world to see if more communities can be discovered. As the entire dataset is gathered from the initial 3546, a larger initial dataset could reveal entirely different communities and a broader network.


## Contribution

In the following table we put in the main and secondary contributor for each part of the project.

| Section                       | Main Contributor   | Secondary Contributor |
|:------------------------------|--------------------|-----------------------|
| Code                          | Peter Bom Jakobsen |                       |
| Web page                      | Yucheng Ren        |                       |
| Motivation                    |                    |                       |
| Basic stats                   |                    |                       |
| Tools, Theory and Analysis    | Søren Fritzbøger   |                       |
| Discussion                    |                    |                       |
| Contribution                  |                    |                       |


## References
[1] https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0098679#s1
[2] https://www.trustedsec.com/
[3] https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0026752#s4