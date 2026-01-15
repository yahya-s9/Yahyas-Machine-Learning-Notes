https://arxiv.org/abs/2512.08108

UC Berkeley paper

- Speaker: Nour

- Levine lab has work on leveraging action chunking in robotic applications, in past we talked ab action chunking for RL, this is exploring where can action chunking help with robotics and RL
- offline RL setting, data set has demonstrations from diff policies, can we learn from data a better policy than what exists
- prior work: rise and reductions make rl scalable, in offline RL setting, main things that limits scalability is use of temporal difference learning, the bias accumulates alot
- so here bootstrapping decreases, can improve performance
- here action chunking gives you a single shot multi step prediction
- they will also leverage ideas from model based RL to get better perf out fo this. here they will combine action chunking with model based learning to learn a policy better than what the data set gives u 


- main approach: model based value expansion
	- model based rl method that learns a dynamics function
	- uses it to improve value estimate
	- model based value expansion approach leverages dynamics function only to improve value estimate
	- idea: if ur using some dynamics function that introduces error, you want to have a dynamics function that has a horizon reduction effect and bootstrapping
	- tradeoff: increase horizon -> , reduce horizon = more error from bootstrapping
	- how to get benefit of both?
	- action chunking.
	- idea from model based value expansion, value is updated 
	- Model Based RL with action chunks = MAC
	- use dynamics function 
	- more u roll out the more error from dynamics function but less from bootstrapping, less u roll out, more bootstrapping error
	- learned action chunking policy
	- dont want to query dynamics function with queries 

- offline setting
	- not learning policy with a simulator, but from data itself
	- use flow matching

- main takeaways
	- when doing offline RL, be aware of bootstrapping errors
		- use N step returns instead of single step
	- if u want to improve value estimate you can icnorproate ideas from model based RL
	- action chunkning can be effective to reduce computational cost of doign roll outs to get better value estimates, and reduce accumulated error from dynamics function
		- but these are from environments that don't look like driving (where there is partial observability on environment state)

--------------------------------------------------------

questions:
- dynamics function?
- bootstrapping?
- horizon?
- action chunking?
- model rollouts?
- rejection sampling?
- behavior cloning?
- state action distribution?
- flow matching? learning a velocity vector and at inference time you integrate through an ODE with velocity vector that you learned 

--------------------------------------------------------

Me chatting with notebooklm:
- start with basics of how machines learn to make decisions. 
- Reinforcement learning (RL) = agent learns to make a sequence of decisions to reach a goal, gets rewards for doing things correctly
- Offline RL 
	- usually agents learn by trial and error in real world.
	- Offline RL, agent learns only from pre-recorded dataset.
	- learning from history book rather than doing it live
- Problem: curse of horizon
	- horizon = how many steps a task takes to finish
	- long horizon tasks (like solving a multi step puzzle) are difficult because small mistakes early can lead to total failure much later
- Model Based RL: 
	- to plane ahead the agent creates a world model or similar to imagine what will happen if it takes certain actions??
