# A/B Testing (Payment type vs Fare amount)
For detail analysis: 

## I. Problem Context
You are a data professional in a data analytics firm, called Automatidata. The company's newest client, the New York City Taxi & Limousine Commission (New York City TLC) requested a detailed statistical analysis of payment type. That is, do the customers who use a credit card pay higher fare amounts than those who use cash?

That said, the TLC team is asking us to consider the following:

- The relationship between fare amount and payment type.
- Test the hypothesis that customers who use a credit card pay higher fare amounts.
- Should you conclude that there is a statistically significant relationship between credit card payment and fare amount, discuss what the next steps should be: what are your thoughts on strategies our team could implement to encourage customers to pay with credit card?


## II. Project Goal

The main purpose of this project is to Sample and analyze data to discover whether there is a relationship between payment type and total fare amount. For example: discover if customers who use credit cards pay higher total fare amounts than customers who use cash. Strategies can be made based on the result in order to find ways to generate more revenue for New York City taxi cab drivers.


## III. Data Source

https://data.cityofnewyork.us/Transportation/2017-Yellow-Taxi-Trip-Data/biws-g3hs

The TLC data comes from over 200,000 taxi and limousine licensees, making approximately one million combined trips per day.

Remark: For the purpose of this project, assume that the sample data comes from an experiment in which customers are randomly selected and divided into two groups:

- Customers who are required to pay with credit card
- customers who are required to pay with cash

Without this assumption, we cannot draw causal conclusions about how payment method affects fare amount.


## IV. PACE Process for completing project
- **Plan**: conceptualize the scope of the project and develop the steps that will guide you through the process of completing a project.
  + Determine the scope of project
  + Determine the project goal
  + Create a workflow for completing the project
  
- **Analyze**: collect, prepare, and analyze all of the data for your project.
  + Exploratory data analysis
  + Data cleaning
  + Identify relationship
  
- **Construct**: build models that will allow you access to hidden relationships locked within data.
  + Data sampling
  + A/B test performing
  + Validity checks
  
- **Execute**: present the finding of your analysis, receive feedback, and make revisions as necessary.
  + Insight conclusion
  + Recommendations
  + Analysis drawbacks
  
## A/B Test Result

After performing EDA and Data cleaning, I conduct A/B test in following process:

### 1. **Problem Statement** - *What is the goal of the experiment?*

Finding ways to generate more revenue for taxi cab drivers by discovering if customers spend higher when paying in credit card compared to cash. From the hypothesis testing result, the business can develope strategy to encourage customers to pay with credit cards, which will likely generate more revenue for taxi cab drivers.

The metric used for assessing if customers spend higher with credit card payment type compared to cash payemnt type is the `total_amount` (The time-and-distance fare calculated by the meter) per day per trip.

- Control group : the average total fare amount per day per trip from customers who paid in cash $(\mu_{c})$
- Treatment group : the average total fare amount per day per trip from customers who paid in credit card $(\mu_{t})$

### 2. **Define Hypothesis** - *What result do you hypothesize from the experiment?*

- $H_0 : \mu_{c} = \mu_{t}$

There is no difference in average total fare amount between customers paid with cash and ones paid with credit card.
- $H_0 : \mu_{c} \not= \mu_{t}$

There is significant difference in average total fare amount between customers paid with cash and ones paid with credit card.
    
### 3. **Design the Experiment** - *What are your experiment parameters?*
    
- Determine sample size by formula: $(n = \frac {16\sigma^2} {\delta^2})$. where $\sigma$ is the population standard deviation, and $\delta$ is the smallest meaningful difference between the control and treatment group. The formula has assumptions that the significant level is $0.05$ and the statistical power is $0.8$. 

- In the scope of this project, I assume that the difference of $\$2$ per trip between control and treatment group will be significant enough to launch future marketing projects for promoting credit card payment. The population standard deviation $\sigma = 10$ is assumed in order to calculate the sample size.

- Based on the above assumptions, the sample size needed for conducting A/B test is: $n = \frac {16*(10)^2} {(2)^2} = 400$ for each group.
    
```python
# extract credit and cash group from original data
credit = data[data['payment_type'] == 1]['total_amount']
cash = data[data['payment_type'] == 2]['total_amount']

# sampling based on the calculated sample size of 400
t_group = credit.sample(n = 400, replace = True, random_state = 1)
c_group = cash.sample(n = 400, replace = True, random_state = 1)
```
    
### 4. **Run the Experiment** - *What are the requirements for running an experiment?*
    
Instruments and data pipelines are set up to collect data. For the purpose of this project, assume that the sample data comes from an experiment in which customers are randomly selected and divided into two groups: 

  1.  customers who are required to pay with cash
  2.  customers who are required to pay with credit card

Without this assumption, we cannot draw causal conclusions about how payment method affects fare amount.

### 5. **Validity Checks** - *Did the experiment run soundly without errors or bias?*
    
This project requires an assumption that passengers were forced to pay one way or the other, and that once informed of this requirement, they always complied with it. The data was not collected this way; so, an assumption had to be made to randomly group data entries to perform an A/B test.

### 6. **Inteprete Results** - *In which direction is the metric significant statistically and practically?*

```python
# conduct the hypothesis
from scipy import stats
t, p = stats.ttest_ind(a = t_group, b = c_group, equal_var = False)

print('T-score:', t)
print('P-value:', p)
```
- T-score: $6.735824687046151$
- P-value: $3.120675999156806e-11$

=> P-value < 0.05, reject H_0, there's a significant difference in average total fare amount between customers who paid in credit card and customers who paid in cash

### 7. **Launch Decision** - *Based on the results and trade-offs, should the change launch?*

#### **Insight Interpretation**

The calculated p-value is less than 0.05, it means that the probability of observing a difference in the average total fare amount between the cash and credit card groups is significant and not by chance alone. Therefore, I reject the null hypothesis and conclude that there is a significant difference in the average total fare amount between customers who paid with cash and those who paid with credit card.

The A/B result provides insights into whether credit card payment can lead to higher revenue for taxi cab drivers. It's statistically significant is that customers who pay with credit card (treatment group) tend to spend more on taxi fares compared to those who pay with cash (control group). This may be due to the fact that paying with credit card is more convenient for customers, or that they feel more comfortable spending more when they don't have to physically hand over cash. Alternatively, it may be due to the fact that credit card users are more likely to be from higher income brackets and are therefore more willing and able to spend more on taxi fares. This can provide the basis for developing strategies to encourage more credit card payments and increase revenue.

**The key business insight is that encouraging customers to pay with credit cards will likely generate more revenue for taxi cab drivers.**

#### **Recommendations**

Based on this result, the business can develop strategies to encourage more customers to pay with credit cards. For example, they can offer discounts or incentives for credit card payments, advertise the benefits of paying with credit cards, and work on improving the payment system to make it more convenient for customers to pay with credit cards.

#### **Analysis Drawbacks**

**Note: Given the assumptions, follwing factors need to be ajusted for the result to be valid and practical:**

- The sample size of 400 was calculated based on assumptions about the population standard deviation and cusomters are randomly selected and divided into two groups. However, in reality, the population variance need to be based on previous research or A/B test, A/A test, further more, there may be factors that influence whether a customer pay with cash or credit card, such as age, income, or location. Failure to account for these factors could introduce bias into the results.


- The experiment uses data come from a single city (New York). In reality, geographic difference may affects ho customers pay for taxi rides and how much they spend.


- This dataset does not account for other likely explanations. For example, riders might not carry lots of cash, so it's easier to pay for longer/farther trips with a credit card. In other words, it's far more likely that total fare amount determines payment type, rather than vice versa.
