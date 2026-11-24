CMU, Stanford, Nvidia

 # prelims
 - open loop v closed loop evaluation
	 - open-loop: 
		 - AI is tested passively using offline, pre-recorded, data.
		 - models fail to account for how actions affect future 
	 - closed-loop:
		 - AI placed in interactive enviornment where it's decisions impact next sequence of events
		 - minor mistakes in closed loop systems acculuate over time, compounding errors
		 - CL systems require AI to optimize for long term cumulative rewards rather than just mimicking immediate actions

 - Partially Observed Markov Decision Processes (POMDP)
	 - authors mathematically formulate entire closed loop dirivng problem as a POMDP
	 - states and Observations
		 - true state of environment (like intentions of other drivers) is only partially observed through sensors
	 - actions and transitions
		 - AI takes an action (driving trajectory) which transitions the enviornment into a new state
	 - rewards: 
		 - AI recieves a reward based on its actions, goal is to maximize its cumulative reward over a timeframe (planning horizon)
	   
 - E2E Autonomous Driving
	 - AD relies on seperate modules (one for detecting objects, one for predicting movement, one for planning the route)
	 - E2E AD replaces this with a unfied neural network that directly takes in sensor data and outputs driving plans.
	 - this paper focuses on existing E2E models like UniAD and VAD to be safer.

 - 3D Gassuain Splatting (3DGS)
	 - to safely test AI in closed-loop setting without putting real car on the road, authors use a 3DGS based simulator
	 - 3DgS is cutting edge rendering technique creates 3d envrionments from 2d images
	 - 3DGS is used here as a world model to generate diverse, realistic what if scenarios for AI to practice on

 - Diffusion Models
	 - not diffusion model from AI image generators like DALL-E / Midjourney
	 - latent diffusion model to generate driving actions
	 - basic concept of a diffusion process
		 - adding noise to data (forward noising)
		 - training neural network (like 1D Unet) to denoise to generate new, varied data
	 - authors use this to predict residual actions. essentially, multi modal adjustment or corrections based on car's baseline trajectory

 - Q-Value Models (Reinforcement Learning)
	 - multi-step Q-value model.
	 - in RL, q value estimates total future reward an agent will get if it takes a specific action in a specific state.
	 - authors use q value model to evaluate the different driving paths proposed by the diffusion model, scoring them based on saftey principles like avoiding collisions, staying in the lane, following speed limits
	 - at infrerence time, model picks path with highest Q-value.

---

- why do AI systems stumble in chaos of real world compared to perf in the lab ?
- dynamic closed loop environment
- Model Based Policy Adaptation (MPA) new framework that addresses that shortfall

- summary
	- current E2E models fantastic at predicting next move from offline data, Open loop evaluation
	- suffer from compounding errors in closed loop
	- MPA acts a bridge between those two words:
		- 3d rendering technique 3DGS to generate what if scenarios
		- diffusinon model to propose alternate driving actions
		- multi step q value model to pick safest most efficient path out of those options

---

- massive gap between open loop and closed loop driving.
- analogy
	- imagine student learning to drive by watching tons of hours of others driving
		- open loop, predict next move based on perfect human driven history
	- same student behind steering wheel, where every tiny mistake changes state of car 
		- closed loop, tiny mistakes add up
- maps perfectly to reality of these systems

- we need to formalize that feeling of sitting in the driver seat using the language of the paper
- POMDP. Researchers formulate closed loop driving as a partially observed markov decision process
	- S = latent state space
		- perfect view of the word. true state includes everything, 
		- every vehicles exact IMU status (acceleration, velocity, orientation in 3d)
		- every vehicle's motion intents, etc
		- true state knows child will chase ball into street from behind parked van
		- driver doesn't know that.
	- O = observation space
		- partially observed part of acronym
		- car's AI doesn't get true state S.
		- only gets O, set of 2d camera images, some lidar points, etc.
		- forced to make decisions based on incomplete image of reality
	- A = action space
		- me deciding to turn steering wheel 10 degrees right or press brakes
	- P = transition dynamics
		- physics of world
		- when u take action A in specific state S, transition dynamics dictate how world changes to next state
		- ex: friction of tires on asphalt, etc
		- bc Markov process: every action you take fundamentally alters the reality you'll phase in next fraction of a second
	- R = reward function
		- score we're trying to maximize
	- gamma = discount factor
		- tells model how much it should care ab future rewards compared to immediate ones
	- t = planning horizon
		- how far into future car trying to simulate

- - 

- we can see why E2E models fail in closed loop settings
	- 1st) observation mismatch:
		- ai model trained offline on human data
		- human rlly good driver, images sees are perfectly centered in lane, camera angles smooth
		- but in real world, ai driving the car, if it drifts off course, camera view shifts, now it sees lane markers from a perspective it never saw during training
		- suddenly operating out of distribution
		- they note this is the minor issue
	- 2nd) objective mismatch
		- open loop models trained to copy behaviors of some other experts, but thats not hte objective. the objective is to drive safely, reach the goal. open loop models use a proxy objective. problem with end to end models, you need to find a way to simulate the input
		- open loop minimizes imitation error compared to an expert's trajectory
		- ai minimizing risk or error between steering angle and human steering angle in training data
		- ai playing giant matching game. it sees picture of a road, in sees human turned steering wheel 5 degrees to here i will turn it 5 degrees
		- no concept of why human turned wheel, no understanding of the physics, etc
		- just knows it
		- core of emperical risk minimization: perfectly mimic past without understanding of future
		- driving is not a matching game. its a game of maximizing accumulated long horizon reward over time
		- if model only knows how to mimic perfect human driving, doesn't know how to recover from mistakes
		- human training data never swerved off road, so ai doesnt know how to recover
		- once it makes tiny error and deviates off path, enters a state it has never seen, errors compound until it crashes
		- increasing planning frequency, more compute, or shortening planning horizon doesnt address objective mismatch. bc if ur optimizing for the wrong thing, doing it faster does not help
		- need a system that can understand long term consequences
		- need AI to know 1 degree deviation means hitting concrete barrier 10 sceonds freom now

	- so to fix this, ai needs to experience mistakes and recover from them
	- but can't do this in real life
	- need a simulation.

---

- phase 1: dreaming up what ifs with counter factual data.

- new data curation
	- expose model to consequences of poor actions in safe enviroment
	- used 3dgs to build environment
	- hyper realsitic 3d video game engine from 2d photo
	- 3dgs compared to older rendering techniques: not building 3 models out of polygons, nor are we using neural radiance fields (NERFs) which can take hours
	- 3dgs displays pixels using millions of mathematically defined 3d blobs (gaussians) (each has a position color and opacity, and a covariance matrix that defines its shape and orientation) so a GPU can display them on a screen incredibly fast
	- so 3dgs acts as our observation model (p obs) in POMDP framework
	- it's the virtual world the ai is going to drive through
	- researchers used a specific simulator called hug sim, cl simualtor desinged ofr this testing
	- runs at frequency of 4 hz, for every update it generates 6 camera angles that are stitched together to give the agent a 360 degree view of the world 
	- how do we generate the counter factuals (the what ifs?)

- algorithm 1 from the paper: 
	- takes a base pre-trained E2E model (like UniAD, VAD & LTF)
	- these are models already trained on massive data sets using empirical risk minimization
	- these models are very good at open loop prediction
	- straight A students who learned everything from watching youtube
	- instead of letting models drive reference path in simulator, researches intentionally break perfection

	- under a teaching forcing setup, they initialize the scene in hug sim
	- then they actively mess with steering and acceleration to force car into bad situations
	- it is not random button mashing:
	- they inject specific types of noise into trajectory.
	- 1st, apply random gaussian noise with standard deviaiton of 0.05 to spatial coordinates
	- 2nd, apply rotation to entire trajectory, varying from -10 to +10 degrees
	- 3rd, warp scale of speed, multiply velocity by factor ranging from 0.1x to 2.0x velocity.
	- grabbing virtual steering wheel and yanking left/right + slamming on gas / brakes.
	- deliverately forcing car off the path and into dangerous unobserved paths.
	- from this combo of noise, they genereate 21 distinct counterfactual what if behaviors for a single observation state
	- for every frame of video, exploring 21 different alternate realities, forcing model to experience what it looks like to be slightly out of bounds drifting to curb or tailgating, etc.

	- but in those 21 generated scenarios, some are disasters
	- if virtual car drives straight through building, sim glitches out, it only knows what cameras saw
	- if you train AI on glitchty stretched textures, it cant percieve reality correctly
	- so they implemented a rigorous filtering step
	- how to filter out garbage without losing valubale mistakes?
	- every one of 21 trajectories, they discard any trajectory that strays too far from human reference path with a spatial distance constraint, keeping it somewhat grounded
	- also evaluate paths using rule based heuristics,
		- ex: did bounding box of vehicle intersect with another vehicles bounding box?
		- ex: did car leave drivable surface
		- throw away trajectories that result in terrible instant rewards
	- they also use beam search algorithm
		- instead of exploring every branch of tree, it only keeps top few promising branch at each step and discards the rest
		- as simulator rolls these counterfactual trajectories out into the future step by step, beam search maintains only rewarding candidates, prunes catastrophic failures and keeps recoverable mistakes
		- result is a library of reasonable alternate realities
		- they have the photorealistic 3dgs images of what those mistakes look like
		- alongside mathematical data on how to pull car back onto safe path
	- so now they have the data set that attacks the observation mismatch. 
	- ai gets to see what a mistake looks like!

----

- having data was 1st half of the battle. model now knows how to recover from errors, but need architectural mechanism to utilize that knowledge during closed loop driving.
- so now we have this massive library of simulated mistakes. we need to teach the AI how to use it in real time.
- phase 2: diffusion based policy adapter.


- going to surgically attach an adapter to the brain of our base model
- our UnIAD or VAD E2E autonomous driving models are frozen
- its weights aren't updated during this phase
- it still produces its standard open loop prediction
- adapter's job is to take naive open loop prediction and refine it, dynamically adjusting velocity and steering based on rich multi modal counterfactual data we just generated

- diffusion models:
	- we have them for art generation
	- model chips away at noise, until a hyper realistic image is formed.
	- but here instead of image generation, model is generating a driving trajectory
	- chiseling out perfect sequence of steering and acceleration commands from pure noise
	- iterative process
- neural network architecture they built for this policy adapter
	- core denoising netowrk (theta) constructed as a 1D u net.
	- 1d bc we are not dealing with a spacial net of pixels, we are dealing with a temporal sequence. 1d sequence of actions over time, it's a time series of spatial coordinates representing future trajectory of the car
	- u net architecture: with encoder / decoder and skip connections, is great at understanding both local fine grain details of sequence and global long term context
	- to make an informed decision, this 1d u-net needs context
	- input to the adapter is a massive 960 dimensional vector every fraction of a second
	- combines 3 pieces of contextual information:
		- 1) feature map encoded by res net 18 backbone
			- birds eye view of the scene
			- res net processes 6 camera images, projects them into a top down 2d grid
			- gives model spatial understanding of the layout (lanes, intersections, obstacles)
		- 2) 128 dimensional encoding of the ego vehicles history
			- specifically the last 5 time steps
			- matematically encodes velocity, steering angle, acceleartion
			- gives model sense of own physical momentum
		- 3) 128 dimensional encoding of base action
			- the trajectory that the frozen e2e model (good at open loop) wants to take
	- u net sees the map, feels momentum of car, and knows the naive prediction from the base model
	- adapter refines action it doesn't just replace it.
	- it does not predict whole driving path from scratch
	- predicts residual action, known as delta 
	- takes base models prediciton, figures out difference to reach optimal safe counterfactual trajectory, *
	- formula is delta a = star - base
	- base model is still doing heavy lifting
	- adapter is like an expert driving instructor sitting in the passenger seat
	- instructor watches student, and road, nudges wheel, predicts a correction
	- formulating it as residual learing allows adapter to leverage massive driving knowledge of E2E base model
	- adapter only dedicates parameter to learn closed loop stability and recovery
	- drastically simplifies learning objective

---

- how do they train a 1d u net to chip away clay and find that perfect residual correction?
- they use a standard latent diffusion process.
- during training they execute a forward noising process over k = 25 steps.
- they take perfect residual correction from generated counter factual data set
- then step by step they gradually add gaussian noise to that perfect correction until it is static
- then u net trained to reverse this process
- given noisy version of trajectory, the current timestep and the 960 dimension context vector, network predicts noise that was added, recovering clean residual action


- training over 25 steps makes sense in lab but not real world dynamic environment
- inference has to be blindingly fast

- so during inference, they transition to d-dim. denoising diffusion implicit models.
- d-dim = technique that allows them to accelerate sampling process, so from 25 steps to 5 
- allows adapter to sample multiple residual trajectories in real time
- bc diffusion model starting from random noise, u don't get same answer every time.
- stochastic nature of diffusion means running this process over 5 steps multiple times = diff answers
- different valid driving paths given the same situation.

---

phase 3: multi step Q value model.

- diffusion adapter was a great brainstorming partner, generating all these options
- how car knows which of 20 paths knows which of these paths result in a crash 20 seconds later
- diffusion model just proposed them it didn't judge them
- we have a policy generator (the diffusion model), but you need an evaluator.

- Q values come from reinforcement learning. 
	- sophisticated scoring system that looks into future
	- Q value model, looks into expected cumulative reward across long horizon
	- brings long term consequence reasoning to E2E model.
	- this directly solves objective mismatch.
	- so no longer mimicking human, now understanding future consequences.
- architecture for q value network
	- needs to process visual info to asses danger
	- uses a  shair frozen res net 18 backbone to process 6 raw camera views from observation
	- pulls those visual features together. concats with ego vehicles history, and concates specific proposed action that is being evaluated.
	- massive feature vector is passed through final multi layer perceptron (MLP decoder)
	- mlp decoder spits out a number
	- q value model breaks down proposed action based on weighted sum of defined principles which are supervised by rollouts of conterfactual dataset
	- 4 principles each with specific weight
		- 1) route completion, w = 1000, finish route is most important thing
		- 2) lateral distance penalty, w = 50, keep car centerd in lane, penalizing for wandering or weaving
		- 3) collision penalty, 100
		- 4) speed limit, 50
	- why route completion weighted 10x more?
		- if collision penalty too high compared to route completion, ai discovers loop hole, safest action is slam on brakes and never move, parked car never crashes, incentivize agent to leave safe zone and try to drive.
	- how does q value model get used in loop when car is driving?
		- inference time scaling. llms use reward models and test time compute to scale their reasoning. generate multiple chains of thought and evaluate them before giving an answer
		- MPA brings same philosphy to autonomous driving
		- at inference time, diffusion adapter proposes batch of residual actions
		- q value model, takes every one, projects them into future, scores them based on 4 principles
		- system then does max value sampling, pick the winner with highest long term utility
	- so before car moves, simulate 20 diff futures, score them, pick winner

--- 

- built the machine.
	- counterfactual data generating the mistakes
	- diffusion adapter propsing multiple ideas to recover
	- q value model picking best idea

--- 

phase 4: hitting the virtual road.

- tested MPA framework on NuScenes data set.
- utilizing hug sim closed loop simualtor
- trained mpa models on split of 290 scenes from boston and singapore, 
	- 1) in domain evaluation: (novel routes from singapore that it saw in training
	- 2) unseen nominal scenes dropped into boston with new roads that it never saw in training
	- 3) safety critical evaluation. 10 hand crafted scenes where adversarial agents trying to cause trouble. scenarios forcing a collision, scenarios a base end to end model would never see in training data
- metric for success = HD score = comprehensive driving score
	- measure of safe effective process, heavily on route completion but penalized by saftey infractions
- trimuphs
	- mpa framework added onto UniAD, VAD and LTF.
	- in domain evaluation: singapore streets it saw in training 
		- UniAd base = 19.4, 39.4% route completion in singapore
		- UniAd + MAP = 66.4, 93.6% route completion
	- out of domain evaluation: boston
		- this is where objective mismatch theory proves its worth
		- base model = HD of 3.3
		- UniAd + MAP = HD of 60.9
		- taught model rules of long term rewards, instead of just memorizing visual patterns of singapore, can drive safely anywhere
	- saftey critical scenarios:
		- base UniAd = 4.5 HD, 11% route completion
		- UniAd + MPA = 70.4 HD, 95% route completion

----

- ablation study = take car engine apart, take one piece out a time, to see which components doing heavy lifting

- ablations

- what parts of MPA framework are responsible for massive leap?

- 1st ablation:
	- rollout steps during counter factual data generation phase, when they sim mistakes and see how they play out
	- what happens if u only step 1 step into future instead of 5 steps into future when generating counter factuals?
	- genreating more steps into future, makes a much better q value model, capturing long term consequences better.
- 2nd ablation:
	- what if we asked diffusion model for just 1 path, does diversity of paths matter?
	- jumping from 1 route to many dramatically increases benefits
- 3rd ablation:
	- reward principles
	- what happens if u take away route completion 1000 weight principle
	- car freezes to avoid collisions perfectly but refuses to drive route

- ablation studies show every component is needed
	- counterfactual imagination provides experience
	- multi modal diffusion proposal provide reactive options
	- q value evaluator ensures those options safe and progressive.


