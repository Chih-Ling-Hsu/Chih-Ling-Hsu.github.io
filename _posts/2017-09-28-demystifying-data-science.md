---
title: '線上會議筆記與分享：「Demystifying Data Science」'
layout: post
tags:
  - Data-Science
  - Conference
category: Notes
mathjax: false
---

「Demystifying Data Science」是美國一場非常精彩的 12 小時免費直播講座，邀請 28 位來自 Facebook、Airbnb、Quora、Etsy、Fast.ai 等知名企業的資深資料科學家分享「如何轉職進入成一位數據分析師」。

由於直播時間是美國時間的早上十點到晚上十點，即，台灣時間的晚上十點到格式的早上十點，因此我只看了晚上十點到半夜十二點半共五場演講，並筆記一些講者分享的內容。由於一些來不及紀錄的缺漏內容是事後再根據記憶補上的，因此有些地方可能用詞或說法會不太精準，就請多多體諒啦。


<!--more-->

## Getting a Data Science Job is Not About Stacking up Prerequisites and Hoping Somebody Picks You


Introdiction: _"... Great news - you can stop waiting and hoping a data science manager will pick you. You can become a Data Scientist without being overwhelmed, perplexed, or unmotivated. This talk will make you a chooser by showing you three key ways you can turn the "Getting A Data Science Job" process on its head so that you have a 100% highly customized plan for you. You'll learn a) how to take control of the process from the beginning, b) how to build your portfolio so that you beat out all the other candidates who are hoping to be chosen, and c) how to revamp your resume so that your resume always goes to the top of the "must-hire" pile."_


Time: 10:00 AM in EST
Presenter: _Sebastian Gutierrez_

---

### Find the job you are interested on indeed.com

indeed.com is job site in US.   You shoud do pairwise comparisons to find the the job you are interested and know about what you should learn to obtain this job.

This job should be the job that would make you say this upon everyday morning:

> “I am excited to learn this!”

### Show you know the right things:
1. project portfolio
    - language
    - technique
    - dataset
    - data structure
    - deployment
    - specific-targeted project
2. resume
    - "What is the intersection between what I want and what employers want?"
    - Resume driven by intersection of Portfolio and Job Advert
3. target companies
    - "Each specific job would be hiring persons with specific techniques."

### Appendix
- [Data Scientist Interview Series (32 interviews)](https://www.datascienceweekly.org/data-scientist-interviews)
- [Data Scientist: How to Get a Data Science Job - Article Series (67 articles)](https://www.datascienceweekly.org/articles)
- [Data Science Weekly Newsletter: Archive (195 as of 17Aug17)](https://www.datascienceweekly.org/newsletters)

## The 5 Most Importtant Things in Data Science

Introdiction: _"I will highlight the 5 most important things in data science, providing a short illustrative (hopefully enlightening and informative) example from my own experience for each of these: The Data, The Science, Data Storytelling, Data Ethics, and Data Literacy. Since the primary focus of data science is discovery (new insights, better decisions, and value-added innovations), I will include an overview of the different flavors of machine learning for discovery in big data, plus a summary of the different levels of analytics maturity and what they mean for real world data science applications. I will finish with a review of the top characteristics of leading candidates for data scientist positions within my organization."_

Time: 10:30 AM in EST
Presenter: _Kirk Borne_

---

### 1. The Data

- 3+1 V's of big data
- combinatorial explosion in big data use case
- Bio-, Geo-, Health informatics, and more...

### 2. The Science

Data science follows a scientific cycle

1. **Data Collection**
    - observation
    - characterization
2. **Formulation of a hypothesis**
    - diagnosis
    - classification
3. **Deduction**
    - formulation of a predictive test
4. **Experimental design and testing**
5. **Evaluation**
    - error characterization & minimization
6. **Review results**
    - validate if you need to **revise hypothesis**

<u>Book recommendation</u>: "Ten Signs of Data Science Maturity"

### 3. Data Storytelling

> “people will forget what you said, people will forget what you did, but people will never forget how you made them feel.”
> ― Maya Angelou

> "No one ever made a decision because of a number. They need a story."


Knowing the knowable through data science is important.   The question can be seperated into 3 phases: $What?$ - $So~What?$ - $Now~What?$

1. Class Discovery
    - Find population segments.
    - Learn rules that constrain the class boundaries (that uniquely distinguish them).
2. Correlation (Predictive and Prescriptive Power) Discovery
    - trends, patterns, and dependencies, ...
3. Novelty (Surprise!) Discovery
    - Find something new, rare, one-in-a-million
4. Association (or Link) Discovery
    - Find unusual/interesting co-occuring associations.


The speaker also talks about the **5 levels of analytics maturity**:

1. Descriptive
    - Hindsight
    - "What happened?"
2. Diagnostic
    - Oversight
    - "Why did that happened?"
3. Predictive
    - Foresight
    - "What will happen?"
4. Prescriptive
    - Insight
    - "How can we optimize what happens?"
5. Cognitive
    - Right Sight
    - "What is the right question to ask for this set of data in this context?"


### 4. Data Ethics

> Statistical thinking will one day be as necessary for efficient citizenship as the ability to read and write.

The speaker states that he consider anyone should learn the following 2 kinds of thinking:

- Computational thinking
- Statistical thinking

He also quotes that

> "If you torture you data long enough, it will confess anything."


### 5. Data Literacy

> “I am shocked that half the students in this country score below average on their standardized test scores”

Using data sounds simple, but these 2 questions actually need you to think carefully:

1. How to use data?
2. How to use data correctly?

Jusk like the question that should always be asked when you need to make a decistion according to a data analysis: "What is the source of the data?"

In the end, when talking avout the attitudes a data scientist should have, the speaker said that we should go sail the 7 Seas (C's) of Data Scientists - [View Details](https://twitter.com/kirkdborne/status/743272055634800640)

### Appendix

- [Article: A Growth Hacker’s Journey to a Data Science Career](https://mapr.com/blog/growth-hackers-journey-right-place-right-time/)
- [Article: The Definitive Q&A for Aspiring Data Scientists](https://mapr.com/blog/definitive-qa-aspiring-data-scientists/)
- [Book: Field Guide to Data Science](https://www.boozallen.com/s/insight/publication/field-guide-to-data-science.html)
- [Report: Ten Signs of Data Science Maturity](http://www.oreilly.com/data/free/ten-signs-of-data-science-maturity.csp)
- [TedX Talk: Big Data, Small World](https://www.youtube.com/watch?v=Zr02fMBfuRA)


## How I Became a Data Scientist Despite Having Been a Math Major

Introdiction: _"Ten years ago, while I was studying math in my undergraduate, Business Week published an article declaring that "There has never been a better time to be a mathematician." As the field of data science has developed since, so has the hype around mathematics. While mathematical modeling is an important component of data science, my math education did little to prepare me for the types of challenges I face in my day-to-day as a data scientist. Many of my most valuable skills and tools are not taught in any traditional educational settings. In this talk, I will discuss this disconnect between my mathematics education and my data science career, the importance of self-instruction for data scientists, and advice on how students can prepare for a career in data science."_

Time: 11:00 AM in EST
Presenter: _Tim Hopper_

---

Here are some quotes from the presentation:

> Mathematics provides much of the theoretical underpinnings of data science (and is valuable).

> "... the flexibility to learn on my own."

## 5 Steps to Launch Your Data Science Career (with Python)

Introdiction: _"Data science can be an overwhelming field. Many people will tell you that you can't become a data scientist until you master the following: statistics, linear algebra, calculus, programming, databases, distributed computing, machine learning, visualization, experimental design, clustering, deep learning, natural language processing, and more. That's simply not true."_

In this talk, you'll learn how you can get started with your data science career today by learning how to master a small set of tools in a single programming language."

Time: 11:30 AM in EST
Presenter: _Kevin Markham_

---

What is exactly data science?

- Asking interesting question and answer the question using data.

And it takes the following 5 steps:

1. ask a question
2. gather data the might help
3. clean the data
4. explore, analyze, and visualize the data
5. answer the question


So, what is the 5 steps to launch a data science career with Python?

### Step 1. Get comfortable with Python

Tips:
- Don't try to learn both Python and R.
- Install the Anaconda distribution of Python.
- Don't try to become a Python expert.
- Focus on: data types, data structures, imports, functions, conditional statements & comparisons.
- Find the right Python course for you!

### Step 2. Learn data analysis, manipulation, and visualization with `pandas`

What can `pandas` do?

1. Reading and writing data
2. Handling missing values
3. Filtering data
4. Cleaning data
5. Merging datasets
6. Visualizing data


Tips:
- Conect the what to the how.
- Learn pandas rather than Numpy
    - Numpy has limitations on the data types of its data because it's designed for computation.
- Learn pandas rather than Matplotlib
    - May not be pretty, but enough for doing exploratory data analysis.
    - Learn Matplotlib if the plot would be seen more than onece.
- Focus on key pandas functions
    - [Tutorials on Youtube](https://www.youtube.com/user/dataschool)


### Step 3. Learn machine learning with`scikit-learn`

Why `scikit-learn`?

1. Clean and consistent interface to tons of different models.
2. Many tuning parameters for each model, but also chooses sensible defaults.
3. Exceptional documentation.

Tips:
- Read scikit-learn's documentation
- Learn the entire machine learning workflow
    - Do not skip or misunderstand any step.
    - [Tutorials on Youtube](https://www.youtube.com/user/dataschool)

### Step 4. Understand machine learning in more depth

Qustions are...

* How do I know which machine learning model will work "best" with my dataset?
* How do I interpret the results of my model?
* How do I evaluate whether my model will generalize to future data?
* How do I select which features should be included in my model?
* And so on...

<u>Book recommandation</u> - "An Introduction to Statistical Learning"
<u>Book recommendation</u> - "OpenIntro Statistics"


### Step 5. Keep learning and practicing

- Kaggle competitions
    - Focus on learning something new in every competitions
- Create your own projects
    - Better be [reproducible data science](http://www.dataschool.io/reproducibility-is-not-just-for-researchers/)
- Articles: [DataTau](http://www.datatau.com/)
- Newsletters:
    - [Data Elixir](http://dataelixir.com/)
    - [Data Science Weekly](http://www.datascienceweekly.org/)
    - [Python Weekly](http://www.pythonweekly.com/)
- Conferences:
    - [PyCon US](https://us.pycon.org/) (There are also [smaller PyCon conferences](http://www.pycon.org/) elsewhere.)
    - [SciPy](https://conference.scipy.org/)
    - [PyData](http://pydata.org/)

### Appendix

- [Data School - 5 Steps to Launch Your Data Science Career (with Python)](http://www.dataschool.io/launch/)

## Fixing the Hot Mess: What You're Doing Wrong to Hire and Get Hired In Data Science

Introdiction: _"The worst part of data science is the hiring process. It’s broken on both ends. People trying to get into the field don’t really understand the field well enough to position themselves for success. Companies hiring their 1st data science team have much the same problem. This is the real data science skills gap.   I want to speak to both audiences: aspiring/novice data scientists and those tasked with building their 1st team. Business needs meet skill sets in a rapid-fire fashion. I want both sides to leave with a framework, high quality questions to ask, and an understanding of what they need to educate themselves about to be successful."_

Time: 12:00 AM in EST
Presenter: _Vin Vashishta_

---

In this talk, the speaker talks about importance of business strategy and communications skills in data science.

> "The gap between data scientists and the employers needs to be bridged."

### The Tech Stack of Data Science

1. Programming Languages
    - Standard
        - R
        - Python
    - Sacrilege (suggest to learn at least one of below, consider developing a product)
        - Java
        - C/C++
        - C#
2. Platforms
    - Hadoop Ecosystem
    - Cloud Options
        - Hybrid, Public or Private
        - AWS/Google/Azure
3. Libraries, APIs, ...
    - Import From...
        - TensorFlow
        - AzureML
        - MXNet
    - Building From Scratch...
        - Research
        - Prpototyping
        - ...

### Strategy & Productizarion

**Strategy**
- Experience, not education based
- Taking business problems
- Projecting costs, duration & ROI (Return On Investment)

**Productization**
- The data science engineering function
- Optimization, deployment & maintenance
- Support end users

Without a data science strategy, the best team in the world produces sporadically at best; Without productization, the best prototypes dob;t make it into users' hands.

Many people say that a data scientis does not need to know these 2 things, but the speaker highly argued about it.

Lastly, in the Q&A session, the speaker said that he thinks specialists is much better than versatilities for an employer. "Too much for learning," he said.


## Useful Links
- [Event Link to Demystifying Data Science](https://www.thisismetis.com/demystifying-data-science/)
- [Data School](http://www.dataschool.io/)
- [DataQuest](https://www.dataquest.io/)