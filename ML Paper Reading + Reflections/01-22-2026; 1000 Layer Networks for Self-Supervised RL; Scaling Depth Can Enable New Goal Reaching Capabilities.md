

- paper: https://arxiv.org/abs/2503.14858
- NeurIPS best paper!
- performance gains are 2x to 50x depending task and scaling depth
- deeper networks, networks learns more complex behaviors
- scaling model depth s generally 2-5 layers
- language models and stable diffusion have hundreds of layers
- lot of learned behavior unlocked after u scale network to specific depth
- question: how to scale and increase rl performance?
- main result: 
![[Screenshot 2026-01-22 at 11.38.31 AM.png]]
 -  lighter colors shallower networks
 - darker color is deeper network
 - in many cases there are scales where performance increases quite a bit

 - RL feedback signal is very sparse. ratio of feedback to parameters is very small. RL is just to fine tune.
 - computer vision, nlp, self supervised learning is the main driving force to drive
 - RL and self supervised learning are not orthogonal to each other, they can combine to make CRL.
 - increasing available data 
 - 3rd step is increasing network depth, including residual connections 

- cited two papers which study wider networks, noted limited gains from adding depth
- here main goal was depth, just scaling depth is enough
- CRL (contrastive rl) was ..

Training:

- Goal conditioned RL
	- P(s)
	- goal defined in goal space
	- reward is sparse, -1 for when state not at goal
	- discount factor gamme is applied
	- goal conditioned policy gives action probabilities given state and goal
	- objective function q function of policy, 
		- starting from p0, goal from goal distribution, 

- Contrastive Reinforcement Learning
	- built on infoNCE
	- actor critic method?
	- focus on critic 
	- critic predicts closeness or similarity with the goal
	- goal of policy is maximize the critic (which is conditioned on goal)
	- actor learns to reach the goal 

- Residual Connections

![[Screenshot 2026-01-22 at 11.50.28 AM.png]]
	- ResNet / skip connections paper first 
	- layer i + x add prev layers output to it
	- skipping connections
	- studied quite a bit in computer vision, it learns modification instead of entirely new transformations
	- gradient propagation is better bc of shortcut paths
	- every skip connection is called a block
	- you have N skip connections
	- swish is critical


Experiment:
- range of locomotion, navigating and robotic manipulation tasks
- sparse reward, measured over last 5 epochs

- increasing network depth, increases performance on CRL. 
- you can scale along critic and actor depth.
- There is a limit to scaling, diminishing returns at some point.

- Questions:
	- why did folks never try simply increasing depth for RL before?
		- tried before, there are limited gains from scaling, activation function, residual connections are the secret sauce here
		- most of the models that use rl are most of the times not RL from scratch, here they are training from scratch
		- if they can also gain some scaling benefits from using cross entropy loss (2 years back called stop regressing) that breaks down value function learning into categorical loss, they were using infoNCE here which is similar to cross entropy
		- attributed to scaling properties that cross entropy can bring into picture, similar to what nlp and vision papers have in common
		- assume they tried it, but now they figured out which axes together should be scaled together
		- produced combo of whatever worked for them
	- actor v critic?
	- techniques here applicable to transformer based models or not? or are transformer based model recipes already well established?
		- transformer networks are more expressive when it comes to scaling them 
	- significance of self supervision ?

