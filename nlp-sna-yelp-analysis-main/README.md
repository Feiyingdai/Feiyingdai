# 🤖 Yelp Datasets: Using Sentiment & Social Networks Analysis

This project presents a comprehensive analysis of the Yelp Open Dataset by integrating multiple perspectives — **textual**, **relational**, and **geospatial**. It combines **Natural Language Processing (NLP)**, **Social Network Analysis (SNA)**, and **Geospatial Visualization** to uncover insights from user reviews, social connections, and physical locations.

<img width="445" height="209" alt="image" src="https://github.com/user-attachments/assets/c4ad8ccd-4698-4549-8d4c-dd04d9b564e5" />


## 1. Project Overview

### 1.1 Key Objectives
- Understand **business characteristics**, **user behavior**, and **review sentiment** across cities
- Visualize **spatial distributions** of businesses and **highly active users’ movement patterns**
- Detect **user communities** and **influencers** using network structure
- Identify **key themes and sentiments** in positive and negative reviews

### 1.2 Techniques Used
This project leverages a diverse set of analytical techniques across different domains:

#### Data Wrangling & Exploratory Data Analysis (EDA)
- Merged and filtered multiple datasets (`business`, `review`, `user`) from the Yelp Open Dataset  
- Performed statistical profiling, aggregation, and exploratory visualizations

#### Geospatial Mapping
- Used **Folium** and **Plotly** to visualize:
  - Geographic distribution of businesses  
  - Movement patterns of highly active users across business locations  

#### Natural Language Processing (NLP)
- Conducted sentiment analysis using:
  - **AFINN Lexicon**  
  - **VADER Sentiment Analyzer** (for social media-style text) 
- Extracted:
  - Word frequency and bigrams  
  - Sentiment-specific word clouds  

#### Social Network Analysis (SNA)
- Constructed **user-user friendship graphs** using `networkx`  
- Computed **degree centrality** to identify top influencers  
- Visualized ego networks and overall network topology  

#### Community Detection
- Applied the **Louvain algorithm** to detect tightly-knit user communities  
- Measured **modularity score** to evaluate community structure strength  
- Visualized communities using layout optimization and color-coded nodes  

#### Visualization & Reporting
- Created interactive dashboards using **Tableau** and **Python-based plots**  
- Generated:
  - Network plots  
  - Geospatial heatmaps  
  - Sentiment distribution and trend charts


## 2. Dataset Overview
This project uses the [Yelp Open Dataset](https://www.yelp.com/dataset), which includes six interrelated tables covering businesses, users, reviews, and activity logs. Below is a summary of each dataset and its key columns:

| Dataset               | # Records     | Key Columns                                                           |
|-----------------------|---------------|-----------------------------------------------------------------------------------------------------------------------|
| `yelp_business`       | 174,567       | business_id, name, address, city, state, postal_code, latitude, longitude, stars, review_count, is_open, categories   |
| `yelp_business_hours` | 174,567       | Weekly opening hours for each business (Mon–Sun)                            |
| `yelp_checkin`        | 146,350       | business_id, hour, weekday, checkins                                                                                  |
| `yelp_review`         | 5,261,668     | review_id, user_id, business_id, stars, date, text, useful, funny, cool (feedback from other users)                   |
| `yelp_tip`            | 1,098,324     | text, date, likes, user_id, business_id                          |
| `yelp_user`           | 1,326,100     | user_id, name, review_count, yelping_since, friends, elite (years), fans, Feedback counts (useful, funny, cool)|


## 3. Exploratory Data Analysis (EDA)

### 3.1 Business Overview

#### 3.1.1 Star Rating distribution
The majority of Yelp businesses fall within the 3.0 to 4.5 star range, with the highest concentration at 4.0 stars, followed by 3.5 stars and 5.0 stars. This suggests a generally positive sentiment trend among businesses, with relatively few receiving very low ratings.

This distribution aligns with the common observation that users are more likely to leave reviews when they are either highly satisfied or dissatisfied, leading to a polarized rating distribution.

<img width="1025" height="393" alt="Image" src="https://github.com/user-attachments/assets/5425f02f-c2a0-4a41-b278-fd006a0eba74" />


#### 3.1.2 Top 20 Categories
Since businesses on Yelp can belong to multiple categories (e.g., "Restaurants; Bars; Nightlife"), we split the categories field by semicolon and retained only the first-listed category to compute the frequency distribution.

As expected, “Restaurants” dominate the platform with 17,842 listings, far exceeding other categories. This is followed by Shopping and Food, confirming Yelp’s primary focus on food and lifestyle services.

These results give a high-level view of what types of businesses are most prevalent on Yelp, and guide deeper segmentation in future analysis (e.g., sentiment by category).

<img width="1280" height="549" alt="Image" src="https://github.com/user-attachments/assets/317c531e-d382-4d65-85f9-e987884850dd" />

#### 3.1.3 Geospatial Mapping
To analyze business distribution, we visualized geospatial data at multiple levels by using `latitude` and `longitude` from `yelp_business.csv`:

**Global Level**

A 3D globe map illustrates the worldwide distribution of businesses. The majority are concentrated in North America and parts of Europe.
<img width="486" height="494" alt="Image" src="https://github.com/user-attachments/assets/70bf3a8e-0f6e-40f4-8c29-c942537a5421" />

**District Level**

Heatmaps of North America and Europe show more granular distribution patterns. Dense clusters are visible in U.S. metropolitan areas such as Las Vegas, Phoenix, and Toronto, as well as key European cities like Edinburgh.
<img width="638" height="500" alt="Image" src="https://github.com/user-attachments/assets/23604ba1-14c1-4bca-8f97-332c353a2523" />

<img width="412" height="501" alt="Image" src="https://github.com/user-attachments/assets/2994073f-773a-4abb-83dd-3e30c2da1370" />

**City Level**

The bar chart above displays the top 20 cities with the highest number of businesses in the Yelp dataset.
Given the significantly high business density in Las Vegas, Phoenix, and Toronto, and the presence of Edinburgh among the top 20, we selected these four cities—Las Vegas, Phoenix, Toronto, and Edinburgh—for more detailed city-level geospatial analysis to better understand business clustering and urban patterns.

<img width="1355" height="464" alt="image" src="https://github.com/user-attachments/assets/97f5c351-18a1-4c12-bd8a-ce34aafae74b" />


Detailed scatter plots show the street-level business distribution in selected cities:
- Las Vegas & Phoenix: Businesses are densely packed along main streets and exhibit a clear grid pattern.
- Toronto & Edinburgh: The layouts reflect local geography and urban planning, with Toronto showing linear concentration along main avenues and Edinburgh having a more organic spread.
<img width="1242" height="616" alt="Image" src="https://github.com/user-attachments/assets/fa44c2b8-9847-4a7f-ab39-69e42a155028" />

<img width="1248" height="622" alt="Image" src="https://github.com/user-attachments/assets/d1fbcc14-2051-4d0c-8f4f-e8ce9eef7a4f" />

#### 3.1.4 Heatmap by Star Rating in Las Vegas (Folium)

To investigate whether business star ratings are geographically clustered in Las Vegas, we used Folium's `HeatMapWithTime` plugin to create an interactive time-lapse heatmap.

Businesses were grouped by their star ratings (from 1.0 to 5.0 in 0.5 increments), and the geographic coordinates (latitude and longitude) of each group were extracted. The resulting animated heatmap illustrates how the spatial distribution of businesses changes with star ratings, allowing us to explore potential spatial patterns or hotspots associated with highly rated or poorly rated establishments.

**Key Insights:**

- High- and low-rated businesses are generally intermixed across the city, with no strong evidence of rating-based geographic clustering.
- Central Las Vegas (e.g., The Strip) shows high density across all rating levels, likely due to heavy tourist activity and a dynamic business environment.
- Peripheral areas are more sparsely populated but not dominated by low-rated businesses, suggesting that star ratings are not primarily driven by geographic location.


**[Click here to explore the interactive heatmap visualization](https://feiyingdai.github.io/nlp-sna-yelp-analysis/folium-2.html)**


#### 3.1.5 Business Checkin Time Heatmap

This heatmap illustrates the aggregated check-in volume across different hours of the day and days of the week on Yelp businesses. Each cell represents the intensity of user check-ins at a specific hour on a given weekday, with darker shades indicating higher activity.

**Key insights:**

- **Peak activity occurs during evening hours (18:00–22:00)**, especially on Fridays and Saturdays, suggesting higher foot traffic during weekends and nightlife hours.
- **Early morning hours (6:00–10:00)** across all days show **minimal check-in volume**, reflecting lower user activity during that period.
- **Weekends (Saturday and Sunday) have the highest total check-in counts**, while Tuesdays and Wednesdays tend to be the least active.

This temporal pattern provides valuable guidance for business staffing, promotion timing, and operational planning, especially for service-oriented businesses like restaurants, cafes, and entertainment venues.

<img width="1337" height="875" alt="image" src="https://github.com/user-attachments/assets/652828a4-90f8-4ca3-b398-f3163c037714" />


### 3.2 User Overview
To understand user engagement and the impact of Yelp’s elite user program, we analyze both **active users** and **elite users**:

- **Active users** are defined as users who have posted at least one review, based on the `reviews` table (`user_id` appearing in reviews).
- **Elite users** are identified from the `user` table's `elite` column, where we split multi-year values to determine elite status by year.

#### 3.2.1 Active vs Elite Users by Year
- From 2004 to 2017, both active and elite user counts increased significantly.
- Active users grew exponentially, reaching over **450,000** by 2017.
- Elite users increased steadily, but much more selectively — around **35,000** by 2017.
- This highlights that Yelp has scaled its user base while keeping the elite program exclusive.

<img width="1214" height="581" alt="image" src="https://github.com/user-attachments/assets/7eab642a-0370-4b94-871e-19390896d3e9" />

#### 3.2.2 Year-over-Year Retention Rate
- **Elite users** consistently show high retention rates (>80%), indicating strong platform loyalty.
- **All users** display a much lower retention rate (~30%), suggesting that most users are casual participants.
- This gap underscores the importance of elite users in sustaining high-quality, ongoing engagement.

<img width="1188" height="580" alt="image" src="https://github.com/user-attachments/assets/f5e514f3-cc84-4c59-a62a-dd9563e6e67a" />


#### 3.2.3 Elite vs Non-Elite User Behavior Comparison

- **Elite users** are far more active and socially recognized:
  - They contribute **many more reviews** and attract **significantly more fans**.
  - Their reviews receive higher levels of **useful**, **funny**, and **cool** feedback.
- These stats confirm the **higher quality and influence** of elite contributors on the platform.

| Metric         | Elite Users | Non-Elite Users |
|----------------|-------------|-----------------|
| **Avg Stars**      | 3.85        | 3.70            |
| **Avg Fans**       | 21.94       | 0.47            |
| **Avg Reviews**    | 226.88      | 13.32           |
| **Useful Rate**    | 2.10        | 0.59            |
| **Funny Rate**     | 1.14        | 0.21            |
| **Cool Rate**      | 1.63        | 0.21            |


#### 3.2.4 Most Engaged User Behavior Stalking
To stalk the most engaged user's behavior, we also used Folium's `HeatMapWithTime` plugin to create an interactive time-lapse heatmap.
This Yelp elite user, identified as **CxDOIDnH8gp9KXzpBHJYXw**, exhibits a **highly active and exploratory dining pattern** across different locations in **Toronto, Canada**, over an extended period.

**Key Insights:** 

- **Consistent Activity**: The user frequently writes reviews across multiple dates, indicating she is an active Yelp participant—not just passively rating places, but likely exploring a wide variety of restaurants and establishments.
- **Geographic Range**: While the heatmap is particularly around the Greater Toronto Area, the user travels across several neighborhoods and cities, showing a diverse geographic footprint. This suggests she may enjoy trying new places rather than frequenting just a few favorites and have a lifestyle or profession that involves commuting or traveling.
- **Potential Influencer**: Given the number and spread of her reviews, she likely has high visibility within the Yelp ecosystem, and her reviews might influence business visibility and reputation.

**[Click here to explore the interactive heatmap visualization](https://feiyingdai.github.io/nlp-sna-yelp-analysis/folium-3.html)**


### 3.3 Review Overview
We randomly sampled 100,000 reviews to explore patterns in user review behavior. The analysis yielded the following key insights:

#### 3.3.1 Review Frequency
Most users are infrequent reviewers.
As shown in the left plot, the distribution of reviews per user is highly right-skewed.
Over 80% of users wrote only 1 review, indicating that the majority of Yelp users are one-time contributors.
The cumulative distribution (right plot) confirms this, with nearly all users having submitted fewer than 5 reviews.

<img width="1280" height="594" alt="image" src="https://github.com/user-attachments/assets/90544e43-4eae-426b-895e-04ce80d0452f" />


#### 3.3.2 Review Usefulness

We analyzed how the number of "useful" votes a user receives correlates with their behavior:

Higher usefulness correlates with:
Lower ratings (both average and max) — perhaps due to more critical and detailed reviews being perceived as helpful.
Longer review length — reviewers with higher useful counts tend to write more in-depth feedback.
Fewer reviews overall — suggesting that more thoughtful reviewers post less frequently but contribute higher-value content.

<img width="1462" height="375" alt="image" src="https://github.com/user-attachments/assets/a2a8cd74-016b-44cb-a2d4-5d473df6181f" />

#### 3.3.3 Review Funny 

We also examined patterns among users who receive more "funny" votes:

Maximum and average ratings tend to decrease as funny votes increase — these reviews may include humorous critiques.
Review length increases — funny reviews also tend to be longer.
Number of reviews decreases — like useful reviews, funny reviews may require more effort and creativity, thus less frequent.

<img width="1472" height="414" alt="image" src="https://github.com/user-attachments/assets/ac60a3aa-b097-4d3f-8b17-17a84caca233" />

### 3.4 Interactive Dashboard  

To facilitate an intuitive exploration of Yelp's multifaceted dataset, we designed an interactive dashboard using Tableau Public. This dashboard offers a visual summary of key analytical themes across three perspectives: businesses, users, and reviews. Each section highlights insights drawn from geospatial patterns, user behavior, and review content, providing a comprehensive lens into Yelp's ecosystem.

**Key exploration areas include:**

**Business**
- Geographic distribution of businesses and their star ratings
- Performance patterns based on business attributes

**Users**
- Growth and trends in user engagement and average ratings over time
- Behavioral patterns of elite vs. non-elite users

**Reviews**
- Relationship between review styles (e.g., length, sentiment) and star ratings

The dashboard enables dynamic filtering and drill-down, allowing users to uncover patterns and generate insights interactively.



**[Click here to explore the dashboard](https://public.tableau.com/views/YelpDatasetsAnalysis/Story?:language=zh-CN&:display_count=n&:origin=viz_share_link)**


## 4. Sentiment Analysis

To better understand customer experiences and feedback patterns on Yelp, we conducted a multi-faceted sentiment analysis on user-generated reviews. This section explores both the emotional tone and lexical content of reviews, using multiple techniques including **TextBlob, VADER, AFINN, and bigram networks**.

**Key goals include**:

- Comparing sentiment distributions between elite and regular users to detect behavioral or attitudinal differences.
- Visualizing lexical patterns through word clouds to understand the most common themes in positive and negative reviews.
- Identifying key sentiment drivers and influential expressions using sentiment lexicons and weighted contribution scores.
- Extracting high-frequency bigram collocations to preserve context and inform business optimization strategies.

This analysis helps reveal how users feel about their experiences, what they tend to praise or criticize, and how sentiment patterns can differ across user types or high-profile businesses.


### 4.1 Sentiment Analysis by User Groups

We applied TextBlob to evaluate sentiment scores of Yelp reviews in 2012, comparing **Elite Users** and **Regular Users**.

- The histogram shows that Elite Users' sentiment scores are more concentrated around **0.25**, indicating consistently positive reviews.
- In contrast, Regular Users display a wider sentiment range, with a longer tail on the negative side.
- **Conclusion**: Elite Users tend to provide more stable and constructive feedback, possibly due to higher engagement or platform experience.
  
<img width="1401" height="883" alt="image" src="https://github.com/user-attachments/assets/1035eb2f-8046-4037-b155-2916e9ce27cb" />


### 4.2 Word Clouds for Positive and Negative Reviews

We randomly sampled 10,000 reviews and segmented them based on sentiment score:
- **Positive**: `sentiment > 0.3`
- **Negative**: `sentiment < -0.3`

Then, we visualized the top 100 most frequent words in each group using word clouds.

- **Positive words**: `great`, `place`, `service`, `food`, `love`, `delicious`, etc., highlighting praise for food and experience.
- **Negative words**: `worst`, `terrible`, `rude`, `bad`, `wait`, `awful`, etc., reflecting frustration about service or long wait times.

These visualizations reveal that **positive feedback is mostly food-related**, while **negative reviews focus on customer service or delays**.

<img width="1467" height="455" alt="image" src="https://github.com/user-attachments/assets/75e89f79-8045-4419-91e6-c8093d3b1ad6" />



### 4.3 Sentiment Analysis on a Top-Rated Business

We selected one of the most-reviewed and highest-rated restaurants on Yelp (e.g., Business ID: `eLFfWcdb7VkqNyTONksHiQ`) for a deeper sentiment analysis using **NLTK**, **VADER**, and **AFINN**.

#### 4.3.1 Top Keywords and Word Cloud

- Using NLTK, we extracted frequent terms from the reviews.
- The resulting word cloud includes terms like `korean`, `bbq`, `food`, `server`, `service`, `great`, and `place`, suggesting that food quality and service are central themes in the reviews.

<img width="1378" height="689" alt="image" src="https://github.com/user-attachments/assets/35434d29-6079-48e4-8018-14ac3c9c46ef" />


#### 4.3.2 VADER + AFINN Sentiment Contribution

**Methodology**:
1. VADER was used to label each sentence in the reviews as positive or negative.
2. We extracted **positive words** from positive reviews and **negative words** from negative reviews.
3. Sentiment contribution was calculated as:  
   **`Sentiment Contribution = Word Frequency × Sentiment Score (from AFINN)`**

**Benefits**:
- This method captures both **sentiment direction** and **intensity**, offering a more nuanced evaluation.

<img width="733" height="893" alt="image" src="https://github.com/user-attachments/assets/90cf599a-8324-44b0-862e-7918d36ba6f1" />

<img width="1453" height="408" alt="image" src="https://github.com/user-attachments/assets/61d3485c-f1fe-486c-89bd-cd4b64bbdeb2" />


**Key Insights**:
- Top positive words: `great`, `good`, `awesome`, `love`, `recommend`, `amazing`
- Top negative words: `bad`, `terrible`, `poor`, `disappointed`, `awful`, `horrible`

Both bar plots and word clouds help visualize how each term contributes to overall sentiment.

#### 4.3.3 Bigram Co-occurrence Network

We built a **bigram network** using NLTK and NetworkX by:
- Extracting word pairs (bigrams) appearing more than 50 times
- Filtering and visualizing networks around key keywords such as `pork`, `service`, and `bbq`

<img width="1371" height="689" alt="image" src="https://github.com/user-attachments/assets/c8ff8046-b4c4-48a1-a293-33e257867f7c" />
<img width="1357" height="688" alt="image" src="https://github.com/user-attachments/assets/b600bdc7-7c5d-4731-8477-5cb07130cfd5" />
<img width="1342" height="677" alt="image" src="https://github.com/user-attachments/assets/863b6272-6d79-49a4-92af-602f6b2d6ac8" />
<img width="1384" height="663" alt="image" src="https://github.com/user-attachments/assets/c824a040-ea63-4c0b-ab2e-6b576a10a10d" />


**Examples**:
- `pork`: co-occurs with `belly`, `spicy`, `garlic`, `bulgogi`
- `service`: co-occurs with `great`, `customer`, `excellent`, `slow`, `fast`
- `bbq`: co-occurs with `korean`, `place`, `restaurant`, `gen`

**Value**:
- Bigram analysis preserves **semantic context**, enabling businesses to pinpoint recurring experiences like:
  - "great service"
  - "pork belly"
  - "korean bbq"
- These insights are useful for **customer experience optimization** and **marketing strategy**.

## 5. Social Network Analysis

This section explores the user interaction network in Stuttgart, constructed from Yelp review data. We model the social graph, analyze its structure, and perform community detection using the Louvain algorithm. After removing disconnected users, the analysis focuses on a **core active network** of engaged users. This filtered subgraph enables:
  - More accurate **community detection**
  - Simulations of **information diffusion** and **topic propagation**

### 5.1 Network Structure Analysis

#### 5.1.1 Graph Density: 0.0569

- The network density is **0.0569**, which indicates a sparse network where most users are not directly connected.
- This suggests that:
  - Most Yelp users are **active in isolation**, posting reviews without forming connections.
  - Yelp’s platform is **content-driven** rather than socially driven.

**Insight:**  
If Yelp aims to boost user retention and engagement, it could explore features that promote social interaction, such as:
- Friend recommendations
- Local user groups or events

#### 5.1.2 Average Degree: 15.65

- The average degree is **15.65**, meaning each user (in the influencer subgraph) is connected to ~15 others.
- This reflects **highly active social users**, likely frequent reviewers or community leaders.

**Insight:**  
These users are valuable for:
- **User growth initiatives** (e.g., referral programs)
- **Brand partnerships**
- **Recommendation engine improvements** (based on their reviews)

#### 5.1.3 Degree Centrality & Influencer Identification

- We used `degree_centrality` and `heapq.nlargest` to identify the **top 500 most connected users**.
- These users:
  - Bridge multiple communities
  - Are likely prolific reviewers, friends with many users, and write useful content

**Insight:**  
These individuals can serve as **word-of-mouth influencers**. Yelp may consider:
- Including them in the **Yelp Elite Squad**
- Offering them perks like coupons, early access, or invites to events

<img width="1137" height="826" alt="image" src="https://github.com/user-attachments/assets/62ae97de-dc9e-4770-a86c-e2d638f11d1a" />

#### 5.1.4 Top 10 Influencers by Degree Centrality
These users have the highest number of connections in the Stuttgart user network and are likely to have strong influence across multiple communities.

| Rank | User ID                          | Number of Connections |
|------|----------------------------------|------------------------|
| 1    | L4LYCnLj2DySKILVvAbmxQ           | 73                     |
| 2    | TcsaJM56vsQuE8wzq_jFqw           | 73                     |
| 3    | rXvsM4vsvrhmLKXul0HRQg           | 72                     |
| 4    | CIi0UKyKWjjcyuWfchFNWg           | 58                     |
| 5    | BFLXmLtblVt8JaSFk_kb1A           | 56                     |
| 6    | OGXaxh9Qg9HQS1Npk1h2t9           | 55                     |
| 7    | 0pgXpWmpy6vAOqvs_TMhaA           | 54                     |
| 8    | izoKblvdpxcDkoEPAKJ7GQ           | 52                     |
| 9    | izoKblvdpxcDkoEPAKJ7GQ           | 52                     |
| 10   | u2M3pJLXJwSmHBabnRSFyw           | 50                     |


### 5.2 Louvain Community Detection

We applied the Louvain algorithm to detect community structures in the graph.

#### 5.2.1 Detected 7 Communities
   - Indicates clear social clustering among Yelp users
   - Clusters may form based on:
     - Geographic proximity
     - Shared interests
     - Favorite types of businesses

#### 5.2.2 Balanced Community Size
   - Largest community: 62 users (~22.5%)
   - Smallest community: 30 users (~10.9%)
   - The network exhibits a **multi-centered structure** with no dominant group

#### 5.2.3 Modularity Score: 0.255
   - Indicates a **moderate level of community structure**
   - While not highly modular (> 0.4 is considered strong), it still reflects meaningful social divisions

**Insight:**  
Community detection results can help Yelp:
- Personalize content and review feeds
- Understand regional or niche-based user behavior
- Design localized promotions and campaigns

<img width="1254" height="830" alt="image" src="https://github.com/user-attachments/assets/0a552637-fc2c-45d9-b298-000c0281e9ec" />


## 6. Strategic Insights

Based on the sentiment analysis and social network modeling conducted in this study, we provide the following strategic recommendations for Yelp and local businesses:

### 6.1 For Yelp Platform Strategy

- **Enhance User Retention through Social Engagement**
  - The social network shows low graph density (0.0569), indicating most users are isolated and not connected with friends. This suggests Yelp is currently driven more by content consumption (e.g., reading/writing reviews) than social interaction.
  - **Recommendation**: Encourage social features like friend recommendations, user groups, and community events to increase user retention and app stickiness.

- **Empower Influencers and Active Users**
  - Users with high degree centrality (e.g., 70+ connections) represent key influencers. These users are socially active and likely to contribute high-quality content.
  - **Recommendation**: Launch targeted programs (e.g., Elite invitations, exclusive events, reward systems) to retain and activate these high-value users.

- **Leverage Community Detection for Personalization**
  - Louvain community detection revealed 7 relatively balanced user clusters. These can be treated as social/interest groups.
  - **Recommendation**: Tailor review recommendations and push notifications based on users' community affiliations and common preferences.

### 6.2 For Business Owners (e.g., Restaurants)

- **Optimize Based on Sentiment Feedback**
  - Both elite and regular users show a tendency toward positive sentiment, but elite users express opinions in a more centered, consistent tone.
  - **Recommendation**: Monitor elite user reviews closely, as they may set the tone for wider perception.

- **Prioritize Service Quality**
  - Word clouds and VADER+AFINN analysis show that *"service"*, *"staff"*, and *"server"* are key sentiment drivers—both positively (e.g., “friendly”, “excellent”) and negatively (e.g., “rude”, “slow”).
  - **Recommendation**: Invest in staff training and consistency of service experience.

- **Track Keyword & Bigram Trends**
  - Bigram networks (e.g., “korean bbq”, “great service”, “pork belly”) provide granular insight into what customers value or complain about.
  - **Recommendation**: Use bigram trends to monitor buzzwords and customer expectations in real time. Adjust marketing, menu items, or operations accordingly.

- **Use Influencer Nodes for Brand Promotion**
  - Influential users identified from network analysis can be invited for promotions, soft launches, or review campaigns.
  - **Recommendation**: Engage top Yelp users to create organic buzz and reach within their communities.


