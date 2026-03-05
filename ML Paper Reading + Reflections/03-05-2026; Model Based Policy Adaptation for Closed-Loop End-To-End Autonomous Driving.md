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

- we can see why E2E models fail in closed loop settings
	- 1st) observation mismatch:
		- ai model trained offline on human data
		- human rlly good driver, images sees are perfectly centered in lane, camera angles smooth
		- but in real world, ai driving the car, if it drifts off course, camera view shifts, now it sees lane markers from a perspective it never saw during training
		- suddenly operating out of distribution
		- they note this is the minor issue
	- 2nd) objective mismatch
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

- phase 1: dreaming up what ifs with counter factual data.
