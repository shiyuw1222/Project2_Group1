## EART60702 Project2 Group 1  Reflections

#### Shiyu Wang

In this project, my main responsibilities included EDA, model building with Random Forest, XGB, LightGBM, and SVR, as well as background research for these technologies. This group project was significantly more challenging than previous ones, and I encountered more difficulties in collaborating to complete it. Although these were only relatively basic aspects, they still presented a challenge for a beginner like me.  

I'm very grateful to my group member who participated in the machine learning portion. We cooperated effectively and shared many challenges. Although much of the work was learned on the job, it was precisely this process that allowed me to accumulate more practical experience. The current project completion rate represents the best we could achieve within our current capabilities. This group collaboration has also been very inspiring, allowing me to learn a lot about data science, machine learning, and teamwork. 

---

#### Hanjin Yue 

In Project 2, I was mainly responsible for making the slides and draft an outline for the speech. I needed to understand the structure of the whole project and turn a large amount of technical content into a clear presentation narrative. I worked on linking the research background, CESM-LENS data, preprocessing steps, feature engineering, model comparison, extreme heat analysis and final conclusions into a logical flow. To make the presentation more convincing, I also paid attention to how key results were shown visually, such as the warming trend of Manchester’s urban maximum temperature, the increase in extreme hot days, and the performance differences between tree-based models and deep learning models. 

Through this process, I developed a better understanding of how to interpret and communicate climate and machine learning outputs. I learned how to simplify complex information, such as differences between tree-based models and deep learning models, into clear visual comparisons that highlight performance and limitations. It also strengthened my ability to present quantitative results with clarity, ensuring that key patterns, such as the intensification of extreme heat and model behaviour under different conditions, could be communicated in a precise and accessible way. Whenever I came across something I didn’t understand, my teammates were always willing to explain it to me patiently. That really encouraged me, and this group project helped me learn a lot. 

---
#### Xinhao Zhou
 
**My Role in the Project 2 Group1**

In this project, I was mainly responsible for the deep learning part — building, training, and evaluating the LSTM, 1D-CNN, and CNN-LSTM models — as well as constructing the weighted ensemble that combined all six models together. On top of that, I also prepared and delivered the second half of the group presentation, covering the all-model comparison, ensemble results, extreme heat prediction skill scores, the 2050–2080 projection, limitations, and conclusions.

Choosing the deep learning section was something I volunteered because I wanted to actually understand how these architectures work in practice, not just read about them theoretically. In hindsight, that decision put me in front of some of the harder problems in this project.

**What I Learned**

Before this project, through lecture I had a basic idea of what LSTM and CNN were, but I had never really built them from scratch on a real dataset. The most valuable thing I learned was probably the difference between *regression accuracy* and *event detection skill*. In the beginning, I was quite happy when our CNN-LSTM reached R² = 0.849 — that seemed like a strong result. But then when I looked at the POD/FAR/TS metrics for extreme days, CNN-LSTM had POD = 0. Zero. It never predicted a single day above 28°C on the test set.

That was a bit of a shock honestly. I spent some time trying to understand why this happened before I realised it was a class imbalance problem. Extreme days are only around 5% of the data, and the standard MSE loss function treats every sample equally, so the model learns that predicting the seasonal average gives low enough loss — it never really "bothers" learning to predict the tail. This is sometimes called regression-to-the-mean, and it is actually a known issue in climate ML, but I only understood that after encountering it myself.

The fix I applied was a weighted MSE loss: extreme-day samples were given ten times the normal penalty during training. This did raise the DL detection rate significantly — LSTM went from POD = 0 to 0.52, CNN-LSTM from 0 to 0.66. But the false alarm rate also went up a lot (CNN-LSTM FAR = 0.58), which shows that improving detection without hurting precision is not simple. So I ended up learning not just how to build these models, but also how to think critically about *what* a model is actually optimising for, and whether that aligns with what you actually care about.

I also got more comfortable with the whole pipeline: handling the cftime no-leap calendar issue, building proper temporal splits without shuffling, making sure the scaler only fits on training data. These sound like small things but they are the kind of mistakes that can silently ruin your results if you are not careful.

**Problems I Encountered**

Honestly, the biggest problem I had was during the Q&A after the presentation. One of the questions was roughly: "Why did you choose to use deep learning models? What's the point?" At the time I gave a fairly vague answer about how deep learning can capture temporal dependencies, but I did not explain it very clearly and I could tell the examiner was not fully satisfied with that.

Looking back, here is what I should have said more clearly:
We chose to include deep learning not because we expected it to beat tree models on regression accuracy, but because we wanted to test whether sequence-based models could learn to detect extreme heat patterns *from raw temporal data*, without relying on the heavily hand-engineered features that give tree models their structural advantage. Tree models perform well partly because they receive 30+ constructed engineered features including rolling 14-day maximum temperature — which is essentially a proxy for heat persistence. The question we were asking is: can a model learn that persistence signal from raw sequences alone?

The result was that, without any intervention, the answer was no — DL models had POD = 0. But with weighted loss, the answer became partially yes — detection improved substantially, though precision remained a problem. This is actually a useful and honest scientific result: it tells us that the architecture is not incapable, but that the standard training objective is misaligned with the rare-event detection task. If we had instead used a focal loss or trained with oversampled extreme-day sequences, the gap might close further.

So the reason for including DL was not to win a performance comparison, but to understand *what kind of information structure* is needed to detect climate extremes — which is a different and arguably more important question.

I wish I had explained this during the Q&A. I think I knew parts of this reasoning but had not put it together clearly in my head before the presentation, so when the question came I kind of froze and gave a surface-level answer. That is something I want to improve — not just understanding things technically, but being able to explain the *why* quickly and clearly under pressure.

**Thoughts on Group Collaboration**

Working in this group was quite a good experience overall. Everyone took on different parts and we coordinated through shared documents and a group chat. There were moments where things got a bit stressful near the deadline — especially around integrating the code and making sure the figures were generated correctly — but we managed to sort it out.

One thing I noticed is that when I was stuck on the POD = 0 problem, I spent quite a long time trying to debug it alone before asking for help. In the future I think I should be quicker to discuss problems with teammates.

**Future Plans and Improvements**

If I were to continue this project, there are several things I would want to try.

The first is to give the DL models the same engineered features that tree models receive — rolling max, lags, seasonal signals — as additional input channels. The current comparison is not entirely fair because trees start with a structural advantage in features. Adding these inputs to the DL sequence would allow a more honest comparison of the architectures themselves.

The second is to explore better loss functions for rare-event detection. Weighted MSE was a simple fix that worked partially, but I am sure there are other ways might reduce the false alarm problem without hurting detection.

Third, I am curious about what happens if we extend the lookback window. We used 14 days, which captures one to two heatwave cycles, but in principle longer memory (e.g., 30 days or even seasonal context) might help the model understand whether conditions are building toward an extreme event.

And more broadly, I would like to apply some of these methods to other projects or other variables.The pipeline we built is reasonably general and could be adapted fairly quickly.

**Final Thoughts**

Project 2 let me to engage with machine learning in a way that felt genuinely applied rather than just textbook. The part I am most glad about is encountering the POD = 0 failure and having to understand it properly — because that kind of unexpected result, where your model looks fine by one metric and completely fails by another, is very common in real applied work, and through fixing this question how to respond to it is a skill I will actually use.

The Q&A experience also taught me something about preparation: it is not enough to understand the technical content —  also need to talk about the *motivation* and *design choices* to others. I will try to prepare for that kind of challenge next time.

