https://arxiv.org/abs/2506.15799


- TLDR: doing RL fine tuning on a diffusion policy by treatng diff policu as black bnox anf usinf rl to pick what noise to put into the model.
- context: in manipulations setting, current state of the art is diff policy. instead of sicretization actions or gaussian distribution, u train diff policy with input adn then u condiiton it on ur state
- current state of the art is doing BC with diff policy. challenge is what if u want to move beyond behavior cloning? downside of diffusion is its hard to do rl becasue for any sort of rl algo u need to back propagate the probs of diff actions, all the way back to model weights, with diff policy u need to back propagate throufh de noising process. 
- diff policy best for large scale
- how to best do rl with diffusion?
- context of paper: assume u have a diff policy that is pre trained on behaviore cloning and now we want to do rl finetuning on it.
- how relevant to driving: often with driving, action space is low dimensional enough that we can just discretize it and no need to diffusion. but benefits to diffusion: 
- or trajectory predicition model, that also is fine tunable beyond just imitation objective.
- how does the dimensionality of the action space affect diffusion? 
- diffusion or de noising type models in general: 
	- u have data set
	- add noise to it
	- model predicts noisy data back to original data
	- during inference u sample random noise then iteratively de noise that
	- if model converges, then u can get samples according to data distribution
	- several algos for de noising.
		- EDM
		- DDIM
		- CFM

- benefit of diffusion: non parametric distribution, so u can get arbitrarily complex distributions, isntead of mlp which outputs means and that params of gaussian, but sometimes true data is multi modal so it can learn arbitrary distributions
  
- with diffusion not imposing any constraints, it can learn arbitrary distrubutiosn adn 
- idea of paper: 

- Q: how to sample initial noise ![[Screenshot 2025-11-20 at 11.46.51 AM.png]]- pi is diffusion policy, black box takes in noise vector W, outputs action A
-  action space might be multi modal, so there are two big
- input noise space is standard gaussian so simple, as long as u sample with norm small enough it should be in the distribution
- if u want to do rl, we need a way to back propagate and train model to choose certain actions over others
	- computationally expensive
	- instead of sampling noise vectors randomly, train policy to take in current state and output parameters of 
	- select what Ws to pick
	- main benefit: diff policy becomes part of env, so we can create RL as part of the W space.
	- latent space policy to pick the noise vectors, mapping back from desired action a' to  
- W is a continuous action space


- goal: avoid having to back propagate through diffusion with some tricks
- want to avoid back propagation through diff policy bc expensive: 
	- iterative process
	- the base diff policy is large, need smaller rl policy, this way u avoid having to do activations for diff models
	- with this approach u don't have to worry about the activations of diff policy at all

- better to do Q learning in original action space