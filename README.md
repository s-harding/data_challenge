# Predict active users based on first 5 days

For this project I will attempt to do two things with a user's first five days of activity data on the app:
1. predict if they will use a activation code (ie do more than just browse for free)
2. predict the number of activation codes a user will use over the lifetime of this data set

I intend to give a detailed walk through of my rationale and approach at the presentation.

### Outline of approach

The first steps I took were to build up the data set that I will use to train a neural network. The basis of this data set is the user_info table. The code activations table is used to determine who has used a code and how many codes each user has used (the two target variables for the two tasks).

I used the questions sheet to get statistics on the question feature activity of each user in the first 5 days of use. The statistics derived are:
- number of questions answered
- percent of questions answered correctly
- time spent on questions
- number of distinct days the user used the questions feature (i.e. did they just do the questions on 1 day of the 5 or did they use it for all 5 etc.)

These statistics are at the user level, so I could then join them back to the user_info table

A similar aproach was also taken for the chapters read data. The following statistics were compiled for each user on the user's first 5 days of use:
- number of chapters read
- number of distinct days the user read at least one chapter

Again these are at a user level and were joined to the user_info table.

The various other categorical fields in the user_info table (signup device, marketing source etc) are were encoded so that they could be used appropriately in the model.

The data is then split into training and testing sets.

Neural networks were then defined: one for categorising whether or not a user would use and activation code and one for predicting the number of codes that would be used (regression). The models are trained on the training sets and validated onthe validation sets. 

### Results

Unfortunately the results are inconclusive. 89% of the users are inactive, so guessing inactive on all the test set will mean that you are right 89% of the time to classify users. Likewise guessing 0 activation codes used will mean your right 89% too. So we want the model to improve on this accuracy. From the above we can see only a very minor improvement.

### Steps for improvement

The steps I would take to try improve the model:

- colloborate with key players (e.g. sales/marketing, subject matter experts, product partners etc.). This is vital for me, these are the people who know the product and how users interact with it best. A conversation with someone in the know makes for much faster progress and feedback cycles.

- Based on above feedback reconsider approach and rebuild/improve where necessary.

- Oversample data from the set of active users and use this to train the model. Currently we are working with a 10-90 split of active to inactive users. This combined with the fact that we are only considering 5 days of use data means that we have a relatively small set to work with. Oversampling will amplify characteristics of the active users and hopefully lead to better results.

- Utilise the time spent reading chapters data. This is useful information but needs to be used correctly. As it is only populated for mobile devices, nearly half the values are NULL. It is important to handle these NULLs appropriately. There are a few possibilities:
  1. set them to 0. Probably not a good option as it assumes no interaction
  2. fill them with the user's average time reading other chapters (if the information exists)
  3. fill them with the average time that chapter has been read (if the information exists)
  4. assign a random time from the distribution of observed times (probably the best option I think)
  
- One-hot-encode the chapters read column and use that as a predictor (I tried this but since there are over 2,000 chapters the additional columns significantly slowed my poor computer so I ended up not using it! :( )

- Define testing methodologies and implement a logging system to track performance of recommendations. Give constant feedback to stakeholders.

#### Notes on choice of tools

- I chose jupyter notebooks for this challenge because I think it is very good for doing quick analysis with unfamiliar data as it lets you see results immediatley and allows for things to be moved around and changed easily. It is also very useful and looks well for presenting and sharing code in an approachable way with with how you can embed well formatted comments.

- I chose Keras for a few reasons. As it is a wrapper around tensor flow it gives all the power of tf while being easy to use and read (which I think is important when sharing code).
