Business Problem:
ACME Communications is having a problem with their information overload in group chats.  Conversations can get very lengthy, and users are reporting that important information and details get lost in those conversations.  This is especially difficult for users who have been away from the chats for an extended period.  This has led to less user engagement on the platform, more user churn, and a decrease in overall user satisfaction.


Project Notes:

Bottom Line Up Front:
The model is not perfect.  Especially with BERT2BERT which isn't trained for summarization.  It gets decent ROUGE scores after a few training epochs, but still human review is needed.  Even with the good ROUGE scores, the text summarization doens't always line up, and the context isn't always captured correctly.  I've noticed this especially on my model with the names being referenced in situations.  Incorrect names being used, amongst other inconsistencies.   

Important Notes on Jupyter Notebook:
I used Google Colab to run the project. Towards the end to speed up training I was using A100 GPU. 
At the end when we get to some metrics to see all rouge scores together, the models I ran earlier were saved, and then reloaded into Jupyter notebook to be used to create the final metric plot.  This will not work if the paths are not updated, or variables changed at the end.  

Process:
BERT2BERT
First I started with a BERT2BERT encoder decoder which was challenge.  Things I noticed that were challenging is the model took very long to run.  So there was a sweet spot to optimize performance with the resources you had.  Also it was hard to get the proper TrainingArguments set because if you used too small of a dataset, the model wouldn't really learn, and if it was too large, it took quite some time.  So you had to balance the two. 
Many different training argument setups were used, but you will only find the final ones in the notebook.  Most notable changes in TrainingArguments: learning_rate, warmup_steps, num_train_epochs, weight_decay, max_grad_norm, label_smoothing_factor.
It looks like at first BERT2BERT really struggles but learns pretty quickly about text summarization.  It levels out around 9 epochs or so, and then starts degrading (depending on the run).  Where as the others started out strong and still improved at a slower rate.
I did find it interesting that the model under all the same conditions would perform slightly differently every time even with the same parameters set.  Some slightly better, others slightly worse.  But I used the parameters I thought was best for the task.

BART/Pegasus/T5
I then moved to the other models for implementation which did not need as much configuration done for them to run successfully.  These models training went faster and their Rouge scores were a lot better off the bat.  They start out very strong compared to BERT2BERT and then slowly increase until they can't anymore.
One thing to note on these models - I used similar training arguments to the BERT2BERT model which was fine tuned with many different parameter settings.  I used this for consistancy and since the BERT2BERT was the focus of the project.

Outcome:
BART was the strongest performing model.  The training ran the fastest, and it had the best Rouge scores.  Also reviewing a few of the outputs from the models, its summaries made the most sense and were the most accurate compared to the others.
Note: TrainingArgument modifications could make a change to these models performance, but I did not go down that path (yet).

Metrics:
BERT2BERT
  Rouge1: 38.1765
  Rouge2: 12.4003
  RougeL: 29.5514
  RougeSum: 29.5317
BART
  Rouge1: 47.4899
  Rouge2: 24.0888
  RougeL: 40.0446
  RougeSum: 40.0453
T5
  Rouge1: 45.3258
  Rouge2: 20.899
  RougeL: 37.1246
  RougeSum: 37.1554
Pegasus
  Rouge1: 42.079
  Rouge2: 19.6021
  RougeL: 35.585
  RougeSum: 35.6228


Problems/Challenges:
Problem: BERT2GPT2 was too hard to get going and run to completion.  BERT capacity was 512 tokens, and GPT2 was 1024 tokens which caused for a lot of errors during training.  When I did finally get it working it was taking extremely long to run and the runtime kept crashing, so I scratched using that for an example model and proceeded with BART, T5, and Pegasus.
Challenge: BERT is limited to only 512 tokens which is good for this example.  only 125 dialouges were more than 512 tokens out of the ~15k.  That being said, if the issue is about users needing summarizations after being gone for a while, this would sure pose to be a problem for implementation.
Challenge: Overfitting was something to be cautious about.  One of my runs on BERT2BERT where I was using RougeL as the evaluation metric was overfitting.  Validation loss kept increasing as Training Loss kept decreasing.  But RougeL was slightly increasing so the training would not stop on the early callback.  This caused for Rouge Scores in teens and single digits on the Test Dataset.  


Conclusion:
The implementation of a chat sumamrization would be very useful for users to catch up on missed conversations.  In order to accomplish this with BERT2BERT, conversations would need to be segmented if longer than 512 tokens in length as BERT is capped at 512 tokens.  BART would be the best implementation for this as it is the most accurate so far.  However, there would still need to be human review on chat summarizations as not all summarizations are captured in the context of the conversations correctly.  It is missing some key pieces (i.e. referring to the incorrect person in chats at incorrect times, not summarizing who is doing what correctly).  The model would benefit from more training data that it could learn on and the model could be more fine tuned to chats and slang used in chats to help benefit the summarization task.  But overall the implementation would be a beneficial implementation to ACME communications.



