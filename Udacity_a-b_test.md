# Free Trial Screener A/B Test
* [Experiment Background](#Experiment_Background)
* [Experiment Design](#Experiment_Design)
* [Experiment Analysis](#Experiment_Analysis)
* [Follow Ups](#Follow_Ups)

## Experiment Background
This is an analysis done for Udacity's Data Analyst Nanodegree capstone project. Further information on the project can be found [here](https://docs.google.com/document/u/1/d/1aCquhIqsUApgsxQ8-SQBAigFDcfWVVohLEXcV6jWbdI/pub?embedded=True).
Data analyzed can be found [here](https://docs.google.com/spreadsheets/d/1Mu5u9GrybDdska-ljPXyBjTpdZIUev_6i7t4LRDfXM8/edit#gid=0)

__Quoted from Udacity's page:__
At the time of this experiment, Udacity courses currently have two options on the home page: "start free trial", and "access course materials". If the student clicks "start free trial", they will be asked to enter their credit card information, and then they will be enrolled in a free trial for the paid version of the course. After 14 days, they will automatically be charged unless they cancel first. If the student clicks "access course materials", they will be able to view the videos and take the quizzes for free, but they will not receive coaching support or a verified certificate, and they will not submit their final project for feedback.

In the experiment, Udacity tested a change where if the student clicked "start free trial", they were asked how much time they had available to devote to the course. If the student indicated 5 or more hours per week, they would be taken through the checkout process as usual. If they indicated fewer than 5 hours per week, a message would appear indicating that Udacity courses usually require a greater time commitment for successful completion, and suggesting that the student might like to access the course materials for free. At this point, the student would have the option to continue enrolling in the free trial, or access the course materials for free instead. [This screenshot](https://drive.google.com/file/d/0ByAfiG8HpNUMakVrS0s4cGN2TjQ/view) shows what the experiment looks like.

The hypothesis was that this might set clearer expectations for students upfront, thus reducing the number of frustrated students who left the free trial because they didn't have enough time—without significantly reducing the number of students to continue past the free trial and eventually complete the course. If this hypothesis held true, Udacity could improve the overall student experience and improve coaches' capacity to support students who are likely to complete the course.

The unit of diversion is a cookie, although if the student enrolls in the free trial, they are tracked by user-id from that point forward. The same user-id cannot enroll in the free trial twice. For users that do not enroll, their user-id is not tracked in the experiment, even if they were signed in when they visited the course overview page.

## Experiment Design
### Metrics Chosen
List the standard deviation of each of your evaluation metrics. (These should be the answers from the "Calculating standard deviation" quiz.)
__Invariant Metrics__:
* _Number of Cookies_: The number of unique cookies viewing the overview page should be roughly the same between the experiment and control group since this is where the groups are split into their respective groups.
* _Number of Clicks_: The number of clicks on the "Start Free Trial" should also be roughly the same between the experiment and control group since users haven't seen the pop-up yet.
* _Click Through Probability_: Since this is mostly just a combination of the previous two metrics, it should also be invariant

__Evaluation Metrics__:
* _Gross Conversion_: Since we recommend a certain number of students not to enroll if they are not committed to spending a certain number of hours/week on learning, we expect the conversion from click to enroll to decrease and want to measure this.
* _Retention_: One of the most important metrics to track: do students stay enrolled and pay for a subscription after their free trial ends. We expect this to increase, since ideally only committed students will continue past the screen and enroll, and once the free trial ends, those are the students that are more likely to pay for a subscription.
* _Net Conversion_: This is the metric that will help sell the feature for buy in from leadership and engineering team: does the change result in overall significantly increased conversions? If not, it's hard to make a compelling case that turning away some  students with the screener is actually good for revenue.

### Standard Deviation
Using this table of [baseline values](https://docs.google.com/spreadsheets/d/1MYNUtC47Pg8hdoCjOXaHqF-thheGpUshrFA21BAJnNc/edit#gid=0)
* **Gross Conversion**  σ = 0.0202
```
Using the baseline values, for our sample of 5000 pageviews, we 
expect to see 
400 clicks
82.5 enrollments 
Based on the probability of free trial given click (0.20625)
SE = √(0.20625*(1-0.20625)/400) = 0.0202
```
* **Net Conversion** σ = 0.0549
```
Baseline probability of paid conversion (0.53)
SE = √(0.53*(1-0.53)/82.5) = 0.0549
```
* **Retention** σ = 0.0156
```
Baseline probability of retention (0.1093)
SE = √(0.1093*(1-0.1093)/400) = 0.0156
```
Since our unit of diversion of cookies, we expect analytical retention and gross conversion standard error to align with the experimental standard error. Net Conversion, however, is a proportion of users who enroll in free trial, so the unit of analysis is different from our unit of diversion and would result in a different Standard error most likely.

### Sizing
Don't really plan on using the Bonferroni correction here since these metrics are closely correlated, making the Bonferroni correction likely too strict.
For our experiment, I'm looking for an α = 0.5 and a β = 0.2
Using the sample size calculator [here](http://www.evanmiller.org/ab-testing/sample-size.html). We realize that the largest sample size needed will be to evaluate Retention.
```
Retention
Baseline Probability: 0.53
Dmin = 0.01
Enrolls needed: 39,115
Projected number of Pageviews Needed: 4741212
```
This is a bit much, even if we feed 100% of our traffic into the experiment (not advised) it would take over 100 days, which would be too long. Maybe this isn't the best metric to evaluate for this experiment. Our next largest group is Net Conversion
```
Net Conversion
Baseline Probability: 0.1093125
Dmin = 0.01
Clicks needed: 27,413
Projected number of Pageviews Needed/Group: 34,2662.5
Experimental Pageviews Needed: 34,2662.5*2 = 685325
```
This is a much more reasonable number for our experiment

**Duration vs. Exposure**
```
Considering Udacity gets 40,000 unique visitors/day. If we divert 75% of our traffic to the experiment we'll end up with 
22.8=685325/(40,000*.75)
23 days for our experiment
```
I chose 75% since we shouldn't be concerned about too many students being exposed to such a minor change. That being said we don't want to divert 100% just in case something goes wrong.
## Experiment Analysis
### Sanity Checks
__Number of Cookies__

| Control | Experiment |
|---------|------------|
| 345543  | 344660     |

```
Expected proportion = 0.5
Observed proportion = 0.5006
Pooled SE = √(0.5*(1-0.5)*(1/345543+1/344660)) = 0.0012
95% Confidence interval Z = 1.96
Upper Bound = 0.5 + 1.96*0.0012 = 0.5024
Lower Bound = 0.5 - 1.96*0.0012 = 0.4976
Observed Proportion within bounds? Yes
```
__Number of Clicks__

| Control | Experiment |
|---------|------------|
| 28378 |28325|

```
Expected proportion = 0.5
Observed difference = 0.5005
Pooled SE = √(0.05*(1-0.05)*(1/28378+1/28325) = 0.0054
95% Confidence interval Z = 1.96
Upper Bound = 0.5 + 1.96*0.0054 = 0.5105
Lower Bound = 0.5 - 1.96*0.00052 = 0.4895
Observed Proportion within bounds? Yes
```
__Click Through Probability__

| Control | Experiment |
|---------|------------|
| 0.050046 | 0.050078|

```
Expected difference = 0
Observed difference = -0.00003
Pooled SE = √(0.050046*(1-0.050046)/345543 + 0.050078*(1-0.050078)/344660) = 0.00052
95% Confidence interval Z = 1.96
Upper Bound = 1.96*0.00052 = 0.00103
Lower Bound = -1.96*0.00052 = -0.00103
Observed Proportion within bounds? Yes
```

### Results Analysis

|             | Control | Experiment |
|-------------|---------|------------|
| Clicks      | 17293   | 17260      |
| Enrollments | 3785    | 3423       |
| Payments    | 2033    | 1945       |

__Gross Conversion__

| Control | Experiment |
|---------|------------|
| 0.2188 | 0.1983|

```
Null Hypothesis - Difference = 0
Observed difference = -0.0206
Pooled Probability = 0.2086
Pooled SE = √(0.2086*(1-0.2086)*(1/17260 + 1/17293)) = 0.0044
95% Confidence interval Z = 1.96
Upper Bound = -0.0206 + 1.96*0.0044 = -0.0120
Lower Bound = -0.0206 -1.96*0.0044 = -0.0291
Statistically Significant?(CI doesn't include 0) YES
Practicallly Significant?(CI doesn't include dmin ± .01) YES 
```
__Retention__

| Control | Experiment |
|---------|------------|
| 0.5371 | 0.5682|

```
Null Hypothesis - Difference = 0
Observed difference = 0.0311
Pooled Probability = 0.5519
Pooled SE = √(0.5519(1-0.5519)*(1/3423 + 1/3785)) = 0.0117
95% Confidence interval Z = 1.96
Upper Bound = -0.0206 + 1.96*0.0044 = 0.0541
Lower Bound = -0.0206 -1.96*0.0044 = 0.0081
Statistically Significant? (CI doesn't include 0) YES
Practicallly Significant? (CI doesn't include dmin ± .01) NO
```
__Net Conversion__

| Control | Experiment |
|---------|------------|
| 0.1176 | 0.1127|

```
Null Hypothesis - Difference = 0
Observed difference = -0.0049
Pooled Probability = 0.1151
Pooled SE = √(0.1151(1-0.1151)*(1/17260 + 1/17293)) = 0.0034
95% Confidence interval Z = 1.96
Upper Bound = -0.0049 + 1.96*0.0034 = 0.0019
Lower Bound = -0.0049 -1.96*0.0034 = -0.0116
Statistically Significant? (CI doesn't include 0) NO
Practicallly Significant? (CI doesn't include dmin ± .01) NO
```
### Sign Tests
__Gross Conversion__

| Successes/Total Trials|
|------------|
| 4/23|

```
Assumed Probability = 0.5
Two-Tail P Value = .0026
Statistically/Practically Significant? (P Value < α/2(.025)) YES
```
__Retention__

| Successes/Total Trials|
|------------|
| 13/23|

```
Assumed Probability = 0.5
Two-Tail P Value = 0.6776 
Statistically/Practically Significant? (P Value < α/2(.025)) NO
```
__Net Conversion__

| Successes/Total Trials|
|------------|
| 10/23|

```
Assumed Probability = 0.5
Two-Tail P Value = 0.6776 
Statistically/Practically Significant? (P Value < α/2(.025)) NO
```
I did not decide to use the Bonferroni correction since there is a strong correlation between the metrics, which would lead the Bonferroni correction to be too strict here.
The only discrepancy we see here between sign/effect size is in the retention rate, where the effect size did show a significant increase, but the sign test did not. From skimming through the data, it looks like when there is a positive difference, those effects are larger than the negative ones, yet these differences all end up being lost when we just pull the sign out. 
### Recommendation
__Change not recommended__
The only metric we saw a practically significant difference in was in Gross Conversion, which was negatively affected by the experiment. This is as was expected, but we were expecting also for that loss to be made up for in retention and net conversion. 
There was a statistically significant increase in retention, which is a good sign. It fell short of our practical minimum desired difference. Looking at our last metric, net conversion, we saw no significant difference in the experimental group, and even saw a strong possibility that the experimental group had a lower net conversion rate.
Any possible gain in retention metric is pretty much negated by the lack of a net conversion increase. We are trying to benefit students by helping them better understand the requirements of the course, but if it possibly comes at the expense of turning students away who would otherwise have converted to paying students then I would not make the recommendation to enact this change.
## Follow Ups
There is significant evidence that writing down one's goals helps an individual to commit and eventually achieve those goals. How could required goal-setting help students to commit to their courses and increase probability of completing them? The student would be presented with a goal setting project at the end of their initial introduction to the course which would be evaluated by a grader who would only be checking if the student was taking the goal setting seriously? The project would just consist of a few prompts asking what the student wants to accomplish during their time in the course (what do you want to learn, what do you want to create, etc.)

My hypothesis is that it would increase completion rate of the course by at least .01.
My unit of diversion would be user-ids that register for a free trial. Half the students would be diverted to see the normal course, and the other half would be presented with this project after their introductory lesson. This way we can track the student's impact of their goals throughout their time with Udacity
The metrics I would measure would be:
* \# Minutes spent in classroom/week - Do students who set goals spend more time in the classroom?
* Completion rate of goal-setting project (students can skip around in the course so it's not guaranteed that they will do the project)
* Length of subscription for cancelled students? (do goal setting students stay enrolled longer?)
* __Completion rate of the course__ (do goal setting students end up graduating at a higher rate?)
* Time to completion (Do goal setting students complete the course faster?)

----
### References
[Experiment Background](https://docs.google.com/document/u/1/d/1aCquhIqsUApgsxQ8-SQBAigFDcfWVVohLEXcV6jWbdI/pub?embedded=True)

[Udacity's Course](https://www.udacity.com/course/ab-testing--ud257)

[Sample Size Calculator](http://www.evanmiller.org/ab-testing/sample-size.html)

[Baseline Udacity Values](https://docs.google.com/spreadsheets/d/1MYNUtC47Pg8hdoCjOXaHqF-thheGpUshrFA21BAJnNc/edit#gid=0)

[Data](https://docs.google.com/spreadsheets/d/1Mu5u9GrybDdska-ljPXyBjTpdZIUev_6i7t4LRDfXM8/edit#gid=0)
