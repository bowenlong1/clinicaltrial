AI Project Design Doc: Tax Hub Recommended Next Step (RNS) [WIP]
Date (last updated): 10/30/2024
DACE
Driver
Peter Ouyang
Approver - Commitments & Execution
Qingbo Hu
Approver - Technical Solution & Architecture
Shankar Sankaraman
Contributors
Solomon Ubani
Tin Nguyen
Kara Liu
Manisha Panta
Escalation
Joanne Fan

Executive Summary

Elevator Pitch
Users get stuck in today’s linear TTO flow with tax topics that they aren’t ready to address. If we can use ML to instead present users with topics they are likely to complete (the “recommended next step”), they will make more progress in their tax prep, leading them to convert at a higher rate.
Model Description
The model has two main sub-model components: 1) a Relevance Model aimed at estimating the probability that certain tax topics are applicable to a given user, and 2) a Ranking Model which can reorder the topics so that easier or less complex tax topics can be presented first to the user and harder topics later.

In the initial version, the Relevance component is a multi-output XGBoost model using PRISM and the Ranking component is fixed logic driven by offline data analysis.
Launch Date
 Mar 12, 2024 
Status
🟢Model is currently live in Prod as part of Tax Hub v2. Tax Hub was at 45% of traffic in 3rd peak and 100% of Tax Hub traffic went to the model.
Test Readout and Metrics
RNS endpoint is not currently being tested, as the model-driven experience was baselined.

Metrics: S2C, acceptance rate of recommended topics
Contacts and dependencies
PM: Ukeme Owoh
PD Contact: Patrick Tsui, Jose Bernal, Grace Faigen
Analytics Contact: Sohaila Shaukat
Tax Contact: Kassandra Koh
Content for Explanations: Nakia Richardson



Business Problem
Tax Hub is a novel paradigm for the structure of the flow of Turbotax Online. Traditionally, TTO organizes the tax prep experience along an approximately linear flow, beginning with Wages & Income, then Deductions & Credits, and then Other Tax Situations. This flow leads to several customer problems, which the new experience hopes to address:

Customers are not always ready for the next step in the flow.
Customers do not know what they need to complete a topic before starting.
Users enter TTO with a different intent than full-on filing.
Returning users don’t always recall their tax topics from the previous year.

Tax Hub intends to restructure the tax prep flow in a non-linear fashion to “optimize for progress.” The hypothesis is that by presenting users with topics they are likely to complete soon (showing easier topics before hard topics), users will get further in the tax preparation process in their current session and be more likely to convert. The key components of Tax Hub, from a UI point of view, are a centralized “hub” from which users will explore tax topics, and a highlighted element which presents the user with a single recommended next step in their tax prep, illustrated below.

The goal of this AI project is to optimize this content using data science and machine learning.

The overall estimated potential business impact of Tax Hub is in the $18-36M range for incremental revenue.

Reductive Nav Experiences: Tax Hub Phase 1&2
Reductive Nav Experiences: Tax Hub Phase 3
Landing Table Data Analyses contains data justifying the general approach of Tax Hub
Tax Hub Phase 2 a slide deck with an overview of Tax Hub post-3rd peak TY22; business impact is covered on the last slide. 


Illustration of the Tax Hub experience
AI Solution
To summarize from the previous section, the main goal of the AI solution is to recommend topics that the user will want to complete soon. We’ve divided this problem conceptually into two distinct components: 

Relevance: estimate the probability that a tax topic actually applies to the customer, and applying a threshold to eliminate irrelevant topics, followed by
Ranking: re-rank the predicted topics along with topics required by business logic, so that topics that the user is likely to complete soon appear first, and thereby avoid the situation where they get stuck on a topic they aren’t ready to address and wind up dropping out of the product. 

A sketch of the internal model design is below. This is fundamentally a real-time ML model based on supervised learning, to be executed while the user is in TTO.

This type of architecture is common in large-scale recommendation systems, but the intent behind it is different. For example, in something like YouTube, the relevance component is meant to be a very lightweight model that scans over many millions of potential videos to recommend so that a more powerful ranking model can act on fewer items. In our use case, the universe of tax topics is much smaller, so that the relevance model can be heavier. Another difference is that in most recommenders, the second re-ranking model is still a relevance model (for example, estimating the probability that a user clicks on a video), whereas in Tax Hub RNS the intent of the ranking model is to use an alternative ranking, distinct from relevance, to optimize for the user making more progress on their tax prep and eventually to be more likely to convert.

This division of concerns into relevance and ranking is important for another reason. The problem of tax relevance is tied to tax accuracy and ultimately compliance, which places constraints on the modeling approaches we can take and what business logic needs to be superimposed on the model. The ranking problem does not impact compliance in the same way, so by separating it out, we give ourselves more freedom in our choice of methodology.
Product Integration
In general, the user will have a stack of topics to go through, where some of these topics are based on business logic (for example, EIC or Child Tax Credit which are calc-driven), some are based on user selections (from GTKM tiles or from hunting through dropdown menus.) The RNS model will add more topics to this stack using an ML model to determine relevance and then rank the full stack of topics to optimize for progress.

Currently, the model is called once, after the user finishes W2. At this point the model predictions are cached in TTO and used to generate the sequence of topics presented to the user.

In the future, we plan to integrate additional model calls at later points in the journey, when there will be more data available and the model can make better predictions. There is a tradeoff between calling the model earlier, when we can save users from hunting for topics, versus later when there is more data. Possible integration points in the future could be:

After W2 (the current integration)
After the user has gone through the stack of recommendations and before they would otherwise go to Federal Review, serving as a “Final Check” to see if the user has left something out.
After Wages & Income but before Deductions & Credits (assumes topics are sequenced to respect the W&I -> D&C section structure)

A sketch of the calling pattern with two integration points
Relevance Model
The first important piece of this AI solution is to actually suggest tax topics that are relevant to the user. This portion of the model addresses the business problem of users not remembering the topics that are relevant to them, and of users not being able to find certain topics because they are hidden in menus or have names that users aren’t able to connect to their situation. The general capability of recommending relevant tax topics may have other uses as well, beyond this particular UI optimization.
Input Data
Features available for prediction depend on the place in the product where the model is called, and on whether users are new or returning.
New users: GTKM, PI, W2, tax topics completed in CY (only for model call #2)
Returning users: PI, W2, PY 1040, tax topics completed in CY (only for model call #2)

One highlight of the feature engineering pipeline is the use of an embedding model (in this case, TinyBERT) to use fields such as occupation_taxpayer to improve the prediction of tax topics such as Educator Expenses and Unreimbursed W2 Job Expenses.

Catalog of input features
The space of tax topics to predict is recorded in this sheet
The Problem of Nulls
One of the underlying problems of in-product tax topic prediction is that before the user files, if they haven’t entered a given tax topic, we don’t know if that is because they don’t have that topic at all (and its associated taxML field is zero) or if that is because they simply haven’t visited it yet (and its associated taxML field will be non-zero after they do visit it.) This is important because it determines which fields can be used as features for an ML model.

The traditional approach to solving this problem is to have clearly defined waypoints based on the linear Turbotax flow, where we have a strong guess for which nulls are actually zero. For example, PTT-deductions and TBM are called after Wages & Income, so we can use current year income fields as features. However, in Tax Hub we are potentially discarding this standard structure in favor of a fully non-linear flow, and might not be able to define such simple waypoints in the future.

The problem of the nulls is one of the motivations for implementing a later model call as a “Final Check” experience. An assumption we are making is that because the vast majority of users do not have that many tax topics, once they have gone through their To-do stack once, it’s likely that they only have a small number of truly relevant unvisited topics left (maybe 1-2.) In this situation, it makes sense to use current year data where the nulls are imputed as zero (except for the topic we want to predict.)

Building a model that is truly robust to being called at arbitrary points in the customer journey is an interesting and difficult problem which we plan to address in later iterations. Some ideas include large-scale neural network training with various patterns of null data, neural networks with various forms of dropout, using the initial relevance model probabilities as a guide for null imputation, or some kind of RNN structure using the sequence to encode the nulls.

Strictly speaking, the problem of having arbitrary patterns of null data is a recommender system problem rather than a classic supervised learning problem (think of movie recommendations, where most users haven’t seen most movies.) However, standard recommendation system approaches aren’t immediately suitable for our use case, because they are too slow at inference time. Fundamentally the way that recommender systems handle nulls is by not actually training a model separately. In matrix factorization, for example, the model fitting and predictions occur in the same step. This step in general is computationally intensive and is why this class of algorithms is usually run in batch mode.
Algorithm
The actual prediction model is multi-output XGBoost and uses the recommender library and PRISM infrastructure for training. Initial results for the model are promising. Generally the precision/recall/F1 scores are quite satisfactory  for returning users, but the model shows lower efficacy for new users, who have less data available. Results from model exploration.

The primary metrics for evaluating the model are the precision and recall for each of the tax topics. In RNS, precision is arguably the more important of the two for optimizing the user experience, as we are showing the user one recommendation at a time very prominently, so we want to get that prediction right. However, recall is also important, as it points to the size of impact of the model. Furthermore, for alternative use cases where we might present a list to the user, recall might be a more appropriate metric if the goal is to guarantee completeness of the tax return.
Open problems and future directions
Expand tax topics
Refine and expand feature engineering, especially for categorical data and the user’s navigation sequence
Improve treatment of null data, possibly with novel algorithms, so that model can be called at any point in the customer journey
Personalization of the “Add more income” and “Add more deductions” drop-downs to improve the user experience when they hunt for additional tax topics

Relation to Other Work and Long-term Plan
Tax topic prediction has been done before at Intuit, specifically in two major use cases: the legacy model Personalized Tax Topics (PTT), and the more recent Tax Break Maximizer (TBM). There are some differences between these use cases, justifying the existence of separate models, but also many similarities. The long-term vision is to deprecate PTT and consolidate tax topic recommendation into a single service, possibly with multiple models under the hood to satisfy the relevant applications. Further details are available in this strategy document.

Updated Status as of 10/30/24
Current work has expanded tax topic coverage of the relevance model to 70 topics, which now includes all the topics covered by PTT and TBM. There is still a gap, because RNS is called at a different point in the customer journey than either PTT or TBM (they are between the major sections of the flow, e.g. before W&I and before D&C.) Because of this, the input features available are different (at D&C, PTT and TBM can use all income fields as features.) Eventually we would like to have a version of RNS designed to be triggered at these points.

There is an incoming track of work to change the NBA recommendations when the SVI flag is set so that standard deduction filers don’t get the itemized deductions. This only makes sense if RNS is called after SVI (which is immediately before the D&C onramp.) The most natural recommendation is to make a second model call after SVI, both to accommodate this business rule for SVI, but also to be able to make D&C predictions using income data.
Ranking Model
The second part of the RNS model is the ranking component, which aims to reduce the frequency of users getting stuck in their tax prep journey on a topic they aren’t ready to start, allowing them to make more progress.

There is a relative lack of data on what constitutes a good ranking (historical data tends to follow the natural ordering of topics in TTO), so we have opted for a simple approach to establish some baselines and lay the groundwork for future iteration. The baseline will be to create a ranked list of all topics by some criterion and use that ranking to reorder the topics which have relevance above some threshold.
Candidate Rankings
Although we have an intuitive notion of tax topic complexity, we don’t really have an precise, unambiguous definition of it. Therefore we have to work with proxies for complexity, which lead to several possible candidate ranking models:
Pure relevance probability – no additional ranking model (order from high to low)
Median time spent (order from low to high)
Propensity to abandon if the user has the topic (order from low to high)
Number of screens or number of fields (order from low to high)
Tendency to procrastinate, measured by median timestamp of tax topic completion (order from early to later; topics that users complete earlier in season tend to be simpler)
Some weighted combination of the above

In addition these complexity-based rankings, we can also imagine rankings on other criteria which would be reasonable:
Impact on the user’s tax refund/liability (which topics have the largest contribution to income/deductions)
The pre-existing ordering of topics from drop-downs in the main landing tables
Any of the above rankings, but with the added condition of grouping them by Wages & Income first, then Deductions & Credits, then Other Tax Situations

Results from offline data analysis to generate some of these rankings are available in this sheet: Complexity per Topic

So far, the “procrastination” (5) ranking seems to be a compelling proxy for complexity. Low-complexity topics such as EIC, Child Tax Credit, and Presidential Election Campaign Fund do in fact appear high on the list (they are completed early in the season) while high-complexity topics such as Schedule E, Schedule Q, Schedule K-1 appear at the bottom of the list. A nice feature of this ranking is that it naturally incorporates time-in-the-season as an element, because topics associated with forms that arrive later tend to be completed later as well.
Experimentation
We don’t know what actually corresponds to a good ranking, and PD doesn’t have bandwidth to integrate an IXP test targeted at the ranking model, so we have opted to run a “backdoor test” where the model endpoint will randomly choose a ranking model and record the version in the output payload. We will need PD to supply a custom beacon to record this data in clickstream for offline analytics.

For v1.2 of the model (initial prod launch) we will test with three ranking model treatments:

Baseline model: topics ordered as they currently appear in the landing tables, with W&I first, then D&C, and OTS last, with individual topics ordered as in the drop-down menus.
Topics reranked within larger sections: This treatment maintains the ordering of W&I, D&C, OTS, but within these sections we re-rank the topics according to one of the ranking treatments above.
Topics fully reordered according to one of the treatments.

This suite of treatments will allow us to test whether there is impact from the reordering, with varying degrees of risk (where 1 is the most conservative ordering and 3 is the most radical.) Moreover, we can test how different user cohorts respond to different rankings.

A final point – if PD can build this custom beacon, it is a crucial component that will later allow us to build the ETL pipeline to use a bandit model for the ranking.
Future state
There are several directions to move in next (and their relative priority may shift based on the results of testing after the first launch.)
Using a bandit to choose between ranking models
Trying more/different ranking criteria in the test
Ranking models with personalization for the ordering rather than a flat list
Combining ranking criteria for multi-objective optimization
Updated Status 10/30/24
The result of the A/B test in 2nd peak was that one of the ranking model recipes (“ComplexitySectionRigid”) showed statistically significant improvement in NBA interaction rate compared to control (the Baseline model using the landing table order.), with 82.6% start rate vs 80.8%.

The ComplexitySectionAgnostic showed an 81.8% start rate, so also a modest improvement against control, but not as much as the Section Rigid ranking. It also generated negative verbatims from users who expected to do all their income before doing deductions. Because it appeared to be performing the best, and to simplify test complexity, the decision was made to baseline the ComplexitySectionRigid ranking.

Currently further refinement of the ranking model is not prioritized.
Success Metrics
The primary business metric is S2C. Secondary metrics to track are the take rate of the tax topics (are the recommended topics correct?), the hunt rate (are we missing topics?), and the number of topics completed in the first session (is the model pulling people forward in their tax prep?)

