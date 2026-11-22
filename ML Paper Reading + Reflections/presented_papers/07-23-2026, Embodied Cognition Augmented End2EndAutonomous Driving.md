
- Researchers from Tshingua University
# In my own words


### Problem: 
- Traditional E2E AD frameworks 

- First work to integrate human driving cognition for improving E2E autonomous driving planning






--- 
# Abstract

- vision based E2E autonomous driving is a new thing
- problem: 
	- E2E approaches rely on visual feature extraction networks trained under label supervision
		- visual backbone (like transformer / CNN) extracts features from camera to construct spatial representations like a Bird's eye view.
		- to train this, engineers rely on explcit human annotated labels
		- trained to predict 
			- 3D bounding boxes (perception)
			- future trajectory (prediction)
	- restricts the generality and applicability of driving models
		- extracting visual features to complete specific tasks = restricted
		- meaning you can't capture abstract, unlabelled environment queues because nobody drew a bounding box around them
	- that's not how humans drive
		- no bounding boxes in our heads
		- we use embodied reasoning: initiative, holistic way of processing the scene to anticipate hazards, read driver behavior and adapt to new situations
	- so relying on rigid annotated labels, standard models fail to extract richer semantic and cognitive info hidden in raw video


- This paper: E3AD
	- comparative learning between 
		- visual feature extraction networks
		- EEG large model
	- in order to learn latent human driving cognition to enhance E2E planning
	- In this work
		- They collected a cognitive data set for the contrastive learning process
		- They investigated the methods and potential mechanisms for enhancing E2E planning with human driving cognition 
		- Tested using popular driving models as baselines
		- both open and closed loop tests
	- They found 
		- E3AD significantly enhances E2E planning perf 

# I. Introduction


# II.I E2E Planning


# II.II Cognitive-Enhanced AI Algorithms


# III. Method


