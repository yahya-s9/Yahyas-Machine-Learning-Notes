
link: https://arxiv.org/abs/2601.16163


anubhav's presentation:
- nvidia paper
- work built up on video foundation model called cosmos predict, given a text or frame or video they can generate a sequence of frames, 
- they want to leverage it, if u can fine tune video model, they can learn a control policy on top of it
- yesterday they released something called dream 0 which is similar to this
- ![[Screenshot 2026-02-05 at 11.35.02 AM.png]]
- for a robotic task, image from a couple cameras, and given task descriptions, this can become fed into video model which can generate the next frames
- if imagine taking a particualar action, what are next sequence of frames
- butthis is also a policy, so they also figure out future state and value which can be used for planning
- action based control?
- this policy can handle multimodal inputs from multi view angles


- out preforming benchmarks for manipulation tasks
- policy can also learn from experience

- initial post training / fine training is imitation / behavior cloning
- cosmos policy can learn from experience to refine it's world model and value function and leverage model based planning

---- 

- why video based planners coming into picutre?
- we use VLA models alot but use VLM backbone, good at semantic understanding but can';t understand physics or dynamics of a system
- so this physical understanding is missing from them
- learning implicit physics
- frames could be temporally incoherent
- vision langauge will still be there but on top can we learn motion priors / physics?

- how is this different than existing video based planners?
	- prior works needs multiple training stages
	- single stage post training only
	- and no inverse dynamics model

- basically main thing they're doing now is using pretrained video langauge model and claiming new recipe they're doing is much simpler than prior approaches and learn frames acitons and value finctions in one go

----

prelims
- based on cosmos predict video model
- encoding frames using wan2 spatio temporal VAE (kind of like duffison)
	- encoding frame into latent space: x_0, not in the data space
	- rescaling in the latent frame
- text embedding T5-XXL embeddings
- given video

- MDP ?
- they use sparse reward


![[Screenshot 2026-02-05 at 11.45.21 AM.png]]


- recipe 
	- initial video frame 
	- in latent space, encode 
	- propioception - what the robot sees
	- still using it as a video foundaitom model, but also predicting frames for action chunks, etc.
	- so not making architectural changes or training of the exisitng video model
	- but they inject extra info in the latnet frame
	- given state
		- what action taken?
		- what is future state?
		- value estimate for next state?

- action chunk?

- they care ab the terminal reward bc spare reward session

- how ab planning?
	- 11 latent frames 
		- 1 placeholder
		- n couple cameras
		- action chunk 
		- input modality?

- input modality
- encoding action chunk in latent frame
- no input history



- learning
	- the policy (along with different objectives as well)
	- predict aux things like next state and value
	- training world model as well.
- ablation 

- they can jointly predict all of these values so they can do a paralllel decoding, to get from latent to video
- cosmos policy can be deployed as a direct policy evaluation without planning (just need the actions, can discard value prediction and next state prediciton) or as a planning policy (need all the inputs) with an autoregressive way


- refine world model in value funtion?
	- train 3 models together: policy, world model and value function
	- deploy control policy, run roll outs, use 90% of that to train world model and value function and then 10% to train the policy

- 

- MDP = model based planning
	- using best of n sampling: level 1 depth, no tree search
	- given state, sample n actions, for each action call world model thrice, 3 different states given an action, for each state call value function 5 times, to get 15 value estimates
	- select an actual value using majority mean: if x of 15 above threshold, take mean of the values across the trheshold


----

performance on benchmarks?
- outpreformign everything compared to other imitation learning pipelines they're cooking them
- pre-trained video model showing help to perform these tasks

Questions: Ablation?
sample efficient planning and training?

problems: 5 secs to make 1 action chunk so not good at dynamic environments

----


notes from second reading:

