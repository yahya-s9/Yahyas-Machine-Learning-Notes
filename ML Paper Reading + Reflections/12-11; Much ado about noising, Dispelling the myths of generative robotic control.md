
- Speaker: Anubhav
- link: https://arxiv.org/pdf/2512.01809

- recently we have seen good amount of success in using diffusion based policys for robotic tasks/ AV driving. High frequency high control tasks. 
	- recent papers like pi zero are working on VLAs that internally use diffusion based policy's 
	- generative policies for learning robotic controls
	- one hypthesis for why: recent success attributed bc they can model compelx mulitmodal action distrubutions and that might help 
- this paper tries to analyze that hypothesis, are they actually learnign complex multimodal distributions or is it something else?
  
  
  - Comparing two policies: generative robotic control (also behavior cloning not RL) and regression based policies (old supervised learning)
  - GCPs better bc of manifold learning
  - iterative process and there is a stochastic nature 
  - trying to induce two steps of iteration and stochasticicity to find out if its the case 
  - "We find that GCPs do not owe their success to their ability to caputre multi-modality or to express more complex"
  - instead we find that iterative computation and intermediate steps during training are supervised
  - is there rlly a benefit to use GCP for behavior cloning or are their claimed successes



- claim: neither multi-modality nor policy expressivity account for GCP's success
	- performance gaps only arise on tasks requiring high precision
- exposing the design space of GCP
	- distributional learning: either 
		- learn deterministic output given observation
		- learn a distribution
	- stochasticity injection: inject noise during learning to improve it
	- supervised iterative computation: generate output with multiple steps, each of it receives supervision during training

- c4: manifold adherence 
- Takeaway:
![[Screenshot 2025-12-11 at 11.48.20 AM.png]]
- C2+C3 are the under explored sandbox for future algorithm design! 
- Questions: 
	- Manifold?
	- Generative control policy?
