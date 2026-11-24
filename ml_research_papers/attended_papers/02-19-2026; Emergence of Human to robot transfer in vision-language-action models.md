https://arxiv.org/abs/2512.22414

- Georgia Tech + Physical Intelligence

- paper explores data diversity
- huamn to robot trasndfer in vla models
- how to include human data in mix of large scale robot training data
- what new capabilites emerge when including human data?
- "increasing pretraining diversity"
- analogy: base llm, if training set is large enough u have high enough emergence
- as you increase pre training diversity success rate is flat, but beyond some level of adding new human data it becomes useful
- but you cant just blindly include human data


- human operator collects data with cameras
- human to robot mapping is tricky
- emergence tied to scale
- trainnign recipe: human root transfer emrges at a sufficient scale 
- dont want to use gloves or teleporators, they want to learn from actual video
- inspiration comes from language models where pre training has to be diverse enough for certain capabilities to be unlockedin psot training 
- as pre training diversity increases, performance 

![[Screenshot 2026-02-19 at 11.42.25 AM.png]]
- why scale? because the latent representations / embedding start being generalized across different modalities (diff robots, human arm, etc).so you get embodiemnt agnostic implementations, 

![[Screenshot 2026-02-19 at 11.47.01 AM.png]]
- robot data = it knows how to place eggs
- create new task - sort eggs
- no robot data for sorting eggs
- human data shows sorting eggs, so you have that data
- when you include both of these, robot learns on new task, the emergence here happens bc this task covered only in human data and not in robot data
- emergence = unlocking at scale, if pretrained data is not diverse enough, even if u add human data of sorting itsnot going to help, needf base traiing data to be diverse, should have data from diff robots, etc.
- scale in data diversity and applied to model diversity 
- performance improving at some scale = emergence

- in terms of llms, gp2 was not trained for translation, it was trained for next token prediction 
- emergent learned behavior that it learned translation, but the model had to be big enough
![[Screenshot 2026-02-19 at 11.55.00 AM.png]]
- built ontop of their own model