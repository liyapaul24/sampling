# ASSIGNMENT: Sampling and Reproducibility in Python

Read the blog post [Contact tracing can give a biased sample of COVID-19 cases](https://andrewwhitby.com/2020/11/24/contact-tracing-biased/) by Andrew Whitby to understand the context and motivation behind the simulation model we will be examining.

Examine the code in `whitby_covid_tracing.py`. Identify all stages at which sampling is occurring in the model. Describe in words the sampling procedure, referencing the functions used, sample size, sampling frame, any underlying distributions involved, and how these relate to the procedure outlined in the blog post.

Run the Python script file called whitby_covid_tracing.py as is and compare the results to the graphs in the original blog post. Does this code appear to reproduce the graphs from the original blog post?

Modify the number of repetitions in the simulation to 1000 (from the original 50000). Run the script multiple times and observe the outputted graphs. Comment on the reproducibility of the results.

Alter the code so that it is reproducible. Describe the changes you made to the code and how they affected the reproducibility of the script file. The output does not need to match Whitby’s original blogpost/graphs, it just needs to produce the same output when run multiple times

# Author: Liya Paul

```
> Identify all stages at which sampling is occurring in the model.

Stage 1: Sampling for people at events with initial infection and traced status
Stage 2: Primary Contact Tracing
Stage 3: Secondary Contact Tracing
Stage 4: Calculating Proportions and Final Sampling Outcome

>Describe in words the sampling procedure, referencing the functions used, sample size, sampling frame, any underlying distributions involved, and how these relate to the procedure outlined in the blog post.

Stage 1: Sampling for people at events with initial infection :
a) Sampling Procedure: The population of 1000 individuals is split between weddings (200 individuals) and brunches (800 individuals). The model then randomly selects a subset of individuals to infect based on the attack rate.
b) Functions Used: np.random.choice(ppl.index, size=int(len(ppl) * ATTACK_RATE), replace=False)
c) Sample Size: ATTACK_RATE = 0.10
d) Sampling Frame: entire population of 1000 individuals
e) Underlying Distribution: Infection is sampled using a uniform distribution
f) Relation to the Blog Post: In the blog post, the true infection proportion among weddings and brunches is simulated by a 10% infection rate for each event type.

Stage 2: Primary Contact Tracing :
a) Sampling Procedure: For each infected individual, a random number is generated, and if it’s less than the trace success rate (20%), that individual is marked as traced.
b) Functions Used: np.random.rand(sum(ppl['infected'])) < TRACE_SUCCESS
c) Sample Size: 20% of infected individuals will be traced
d) Sampling Frame: sampling frame is the subset of individuals who are infected
e) Underlying Distribution: This follows a Bernoulli distribution.
f) Relation to the Blog Post: In the blog post, the primary contact tracing success rate is assumed to be 20%. This imperfect contact tracing is modeled by randomly selecting infected individuals with a 20% probability of being traced. 

Stage 3: Secondary Contact Tracing :
a) Sampling Procedure: The code first counts how many traced individuals are associated with each event. If an event has two or more traced individuals, all infected people who attended that event are then traced.
b) Functions Used: ppl[ppl['traced'] == True]['event'].value_counts()
                   ppl.loc[ppl['event'].isin(events_traced) & ppl['infected'], 'traced'] = True
c) Sample Size: The sample size for secondary tracing depends on how many events have two or more traced individuals. All infected individuals at those events are then traced.
d) Sampling Frame: The sampling frame is the subset of events with at least 2 traced individuals.
e) Underlying Distribution: This is a deterministic process based on the primary tracing results.
f) Relation to the Blog Post: The blog post shows how secondary contact tracing might happen after identifying an event with multiple infections.

Stage 4: Calculating Proportions and Final Sampling Outcome :
a) Sampling Procedure: The code calculates the proportion of infections and the proportion of traced individuals that can be attributed to weddings.
b) Functions Used: sum(ppl['infected'] & (ppl['event_type'] == 'w')) - infections at weddings.
                   sum(ppl['infected'] & ppl['traced'] & (ppl['event_type'] == 'w')) - traced individuals at weddings.
c) Sample Size: The entire population of 1000 individuals.
d) Sampling Frame: The entire population of infected individuals is the sampling frame for both infections and traces.
e) Underlying Distribution: The calculation uses simple counts to compute the proportions. 
f) Relation to the Blog Post: In the blog post, the observed proportions of infections traced to weddings (and brunches) can be biased due to imperfect tracing and secondary tracing. 

>Run the Python script file called whitby_covid_tracing.py as is and compare the results to the graphs in the original blog post. Does this code appear to reproduce the graphs from the original blog post?

In the original blog post, 2 graphs are displaying. Graph1 shows the proportion of infection resulting from weddings and graph 2 shows Distribution of true vs observed proportion of cases from wedding. After running the Python script file called whitby_covid_tracing.py as is I got a graph which shows both infections from wedding(blue) and traced to weddings(red) in single graph, which is attached as "Figure1_AS IS code result". The code result is producing graph that is similar but it shows larger frequency count compared to blog graphs.

>Modify the number of repetitions in the simulation to 1000 (from the original 50000). Run the script multiple times and observe the outputted graphs. Comment on the reproducibility of the results.
When I run the modified script multiple times, I observed that the shape of histogram is getting changed. This is due to the the smaller number of trials. 
With fewer repetitions (1000 instead of 50,000), the variability in the results might increase. This could lead to more fluctuation in the histograms from run to run, as the sample size is smaller.

>Alter the code so that it is reproducible. Describe the changes you made to the code and how they affected the reproducibility of the script file. The output does not need to match Whitby’s original blogpost/graphs, it just needs to produce the same output when run multiple times
To make the same output when running multiple times, I will modify the code by setting a fixed random seed. I have added the code np.random.seed(42) at the beginning to ensures that the random processes are reproducible. When the seed is fixed, the sequence of random numbers generated will be the same each time the script is run. 
```


## Criteria

|Criteria|Complete|Incomplete|
|--------|----|----|
|Altercation of the code|The code changes made, made it reproducible.|The code is still not reproducible.|
|Description of changes|The author explained the reasonings for the changes made well.|The author did not explain the reasonings for the changes made well.|

## Submission Information

🚨 **Please review our [Assignment Submission Guide](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md)** 🚨 for detailed instructions on how to format, branch, and submit your work. Following these guidelines is crucial for your submissions to be evaluated correctly.

### Submission Parameters:
* Submission Due Date: `HH:MM AM/PM - DD/MM/YYYY`
* The branch name for your repo should be: `sampling-and-reproducibility`
* What to submit for this assignment:
    * This markdown file (sampling_and_reproducibility.md) should be populated.
    * The `whitby_covid_tracing.py` should be changed.
* What the pull request link should look like for this assignment: `https://github.com/<your_github_username>/sampling/pull/<pr_id>`
    * Open a private window in your browser. Copy and paste the link to your pull request into the address bar. Make sure you can see your pull request properly. This helps the technical facilitator and learning support staff review your submission easily.

Checklist:
- [ ] Create a branch called `sampling-and-reproducibility`.
- [ ] Ensure that the repository is public.
- [ ] Review [the PR description guidelines](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md#guidelines-for-pull-request-descriptions) and adhere to them.
- [ ] Verify that the link is accessible in a private browser window.

If you encounter any difficulties or have questions, please don't hesitate to reach out to our team via our Slack at `#cohort-3-help`. Our Technical Facilitators and Learning Support staff are here to help you navigate any challenges.
