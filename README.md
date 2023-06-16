# LoL_Match_Result_Prediction_Project
by Adam Tran (ant010@ucsd.edu)

In this project, I developed a model to predict the outcome of matches based on match data at minute 15. 
All of my work is in the corresponding Jupyter notebook, so I recommend referring to that in tandem, as you read along.

Note: Our exploratory data analysis on this dataset can be found [Here](https://github.com/tranman03/LoL_LeeSin_Project.git)

## Framing The Problem
The prediction problem I will be looking at is predicting the outcome of the matches. To be more specific, I want to look at the game at 15 minutes and predict which team will win and which will lose. Because of this we will be able to use the data at 10 and 15 minutes in our model. Data concerning objectives such as dragons and towers may be harder to use since we don't have time stamps for these objectives, and thus don't know if we would have this information at the time of prediction. This would be a binary classification type problem as we are trying to classify each match as a win (1) or loss (0). For this problem, the response variable would be "result," because as I said we will be trying to predict wins or losses. For this problem, there are no clear negative consequences to having a high false positive or high false negative other than our model being not as accurate. Thus we will use accuracy as our metric to evaluate the model. Furthermore, since for every loss there is also a win, there is no class imbalance. This makes accuracy a suitable classifying metric.

## Baseline Model
For the baseline model, I will use a decision tree classifier model (I change the model to a Random Forest Classifier at the end but started with a decision tree classifier). In the base model I will look at the features golddiffat10, expdiffat10, golddiffat15, and expdiffat15 for just the teams. This way we can see the total difference based on the teams. Since these features are already quantative features, there is no need to modify or encode them in any way. Thus our entire pipeline would be using the decision tree classifier.

Looking at the training and testing accuracies, I think this model is not good. Using a max_depth of 13 gives a training accuracy of around 83% and testing accuracy of around 73%. This means for any unseen data, we can guess the accuracy of the predictions will be around 73%. It is hard to get both accuracies above 80% with the current model, by rough modification of the hyperparameters. This means we need to further develop the model by adding in more relevant and indicative features and tuning the hyperparameters further.

## Final Model
I added several features to improve my model: firstblood, firsttower, killsat15, deathsat15, csdiffat15, and side. For the first 5 of these features, each of them gives some information into how a team is performing in the first 15 minutes. Getting the first blood or first tower normally indicates a team is doing well. Having more kills, less deaths, and more cs at 15 minutes also indicates that the team is more favorable to win. I added side for a less straightforward reason. While it may seem that side might not have an effect on a teams performance, it has been said by some people that a certain side can be more favorable. Additionally it has been stated red side is normally associated with aggression, from the color red, making the team perform better. I wanted to add this feature in since it was interesting and cool to think about. I also modified the golddiff features to indicate if from minute 10 to minute 15 if they were continuing to gain more gold or if the other team was catching up. This new feature would show if one team was starting to catch up in gold to another team.

We see the validation accuracy starts to level off around depth = 7. However, the testing accuracy continues to increase as depth increases.There are slight decreases after increasing from depth 7,10,and 13. I choose 13 since it has the highest average accuracy of the three. 

Note that for this model I chose to use a Random Forest Classifier instead of the initial Decision Tree Classifier. I found that this model gave better overall accuracies. I believe this is because a lot of the features are heavily correlated (i.e. if you have a lot of cs you probably also have a lot of gold and xp). The Random Forest Classifier avoids overfitting that a single decision tree would do. I looked into the differences a little online and based my decision of that. Source: https://towardsdatascience.com/why-random-forests-outperform-decision-trees-1b0f175a0b5#:~:text=Random%20forests%20consist%20of%20multiple,as%20more%20trees%20are%20added.

The hyperparameter I chose to focus on was the max depth of the tree. To determine the best hyper parameter, I ran the model on the test set for each depth, similar to lab 9. I then looked at the accuracies for each of the depths. As mentioned above I chose a max depth of 13 as my final decision. This is because the higher the depth, the higher the training accuracy. Furthermore, we see the validation accuracy drop after depths 7,10,13. Because 13 has the highest training accuracy I chose 13, even though depth 7 had a slightly higher validation accuracy by .007.

Looking at the accuracies, the final model performs about 6% better for the training accuracy and 3% better for the testing accuracy. In total this is 89% and 76% for training accuracy and testing accuracy respectively. This makes sense as we added more relevant features to our model and improved the model that we chose to use.

## Fairness Analysis
The NA LCS league is notorious for being one of the worst leagues. The players are less consistent and predicatable as other leagues. This leads me to question whether there might be some problems predicting the outcomes of games in the NA LCS.
Question: Does my model perform better,worse, or the same for matches within the NA LCS league versus games outside NA?
Group X would be matches inside the NA LCS and Group Y would be matches not in the NA LCS
My evaluation metric again will be accuracy for the same reasons as stated prior
H0: The model performs equally well on the two groups
H1: The model performs not as well on games in the NA LCS league, and is not as accuarate
Significance Level: a = .05
Test statistic: Comparing accuracy of permutated data to actual data
pvalue = .1
It seems like the random permutation matches have worse accuracy than the NA LCS 10% of the time. We cannot conclude from this test that the model is less accurate on NA LCS matches. Therefore we do not reject the null, meaning it seems as if the model performs equally well on the NA LCS League as it does on the rest of the leagues
