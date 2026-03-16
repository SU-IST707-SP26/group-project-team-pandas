- Does your proposal include all of the above mentioned sections? 0/1

Please read the directions.  The report was supposed to be a jupyter notebook, not a markdown file.

- Are your objectives concrete and do you have a clear stakeholder need? 2/2

Nice.  I like the stakeholders as restaurant owners to prepare them for likely inspections instead of predicting likely violations.  This suggests to me that you want to predict where inspections are going to be run, rather than the outcome of inspections.  So, I'm hoping that's what I'll see here.

Moving further through your report, it looks like you are predicting score outcome.  But in light of my suggestions, you might instead provide a probability of an inspection given past performance.  Make sense?


- Do you have a good data source and have you done a thorough job investigating its provenance and credibility? 1/1

- Did you do a thorough job exploring your data 2/2

Note that your inspection scores look like a log normal distribution, so if you log the data, you should be able to transform it to a pure normal distribution. 

I think it would also be good to being bringing geographic and possibly socio-economic data.

- Have you done some initial modeling of your problem and do you have some early baseline results? 3/3

- Do you have a clear path forward 1/1


I think this is a good start, but as I've discussed above, I'm going to recommend that you change your stakeholder problem to be predicting the likelihood of an inspection based on a number of different things, including:

- geographical information
- recent trend in inspections
- past performance, including nature of violations, overall score, etc.

The goal here would be to model the decision logic of the inspections, which in turn would provide restaurant owners with some ability to work on making sure their establishments are compliant.  Predicting violations is going to swing the other way, empowering inspectors with more targeted tools.  This is good in one regard, but I think it ultimately reinforces a potentially biased power dynamic.  Going the other way will actually improve incentives for restaurants to bring their facilities into compliance.

Score: 9/10

