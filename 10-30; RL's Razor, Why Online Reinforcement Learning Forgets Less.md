
https://arxiv.org/abs/2509.04259

- Speaker: Anubhav Paras
- comes from ocham's razor: usually ml algos prefer simpler solution, less in complexity.
- RL's razor: on policy rl prefers solutions that implicitly minimizing KL, more into fine tuning part, prefer finding soltuios that minimize kL divergence beyween final policy and base polocu that they were startign from, as compared to any other fine tuning method that is not doing that. On policy RL does that implicitly is the crux of
- paprer not proposing new algos, experimental study, valdiating hypothesis that this is the case
- diagram: 
	- base policy: llm fine tuned on specific math task, and u want to fine tune this base policy on another math task, we go via two paths: supervised fine tuning and rl fine tuning. 
	- this is gradient plot where its task preformance![[Screenshot 2025-10-30 at 11.38.04 AM.png]]
	- using SFT, we obserbe, model learned during pre training stage, prefromance degrades as new tasks increases.
	- compared to on policy rl, it forgets less. it deterioriates a little bit but not as fast as SFT.
	  
- motivation:
	- when dealing with large foundational models not specialized to a task, it;s nice to adapt to new data and new objectives, continual learning, keep learning and adapting to tasks and capabiltiies,but if u do that, can forget what it acquired in previous training runs. so this can be a blocker for contual improvements.
	- whatever fine tuning method we use, SFT, RL, etc, online / policy specific RL preserves skills better than any other approach
	- what is underlying mechanism that is allowing RL to do this? and minimally impact model's prior mileage?
- predictor to measure quantitave way how much it is forgetting?
	- emperically fouind that when fine tuning a model on a new task, degree of forgetting is predicted by KL divergence between base policy and fine tuned policy.
	- so this can be a reliable predicot rfor hjow much the model is forgetting.
- defining RL's razor
	- amongst other solutions during fine tuning stage
	- on policy RL is biased towards policies closer to the base policy
- theoretical evidence
	- during online RL traiing, we are sampling from model's own distribution, whatyever samples , we only nudge prob distrubtiion towards high reward, 
	- as opposed to SFT settings, base policuy distrubution gets pushed further away, as in it might not preform well on previouis tasks, but will still do well on new tasks its being trained on.
- one intersting experiment
	- not about algo, 
	- in online rl we have implicit bias to minimze KL. but what if we still have an SFT algo that inherently minimizes kl divergence. we choose a distrbution that explicitly minimizes divergence. trainning on this oracle distriubtion produces less gorhetting than RL itself.
	- so rl's advantage not coming from being inherently different. RL is not doing magic. its ab implicit KL minimization that's happening. so when training biased towards KL minimma l solutions, KL is reducted.

- main contributions:
	- We show that RL fine-tuning forgets less than SFT, even when both reach the same performance on new tasks
	- emperical forgetting law: forgetting can be predicted by kl divergence to base polciy measured on new task
	- on policy nature of polciy gradient methods leads to smaller KL skhifts, that is RL's advantage

- experiment, different kinds of tasks.
![[Screenshot 2025-10-30 at 11.47.27 AM.png]]
- as accursacy on new task was increasing, preformance on previous tasks using SFT was decreasing.


- smaller KL divergences lead to less forgetting.
	- how did They prove it?
	- toy example: parity MNSIT: given image of even, predict any even digit label. given any odd label, predict any odd label.
	- small model, 3 layers, pretrained model on subset of ParityMnist and fashionMnist, fine tunined on partityMNSIT while measuring forgetting on fashionMNIST.
	- it's not ab large scale tasnformers, this is a general property of fine tuning deep generative models. this is a theoretical algorithmic concept.
	- in parityMNIST they identiifdedi labeling that minimizeds KL divergence to the bnase amongst all distirbutions. 
- another expeirment
	- sft guided by output from RL distrubution.
		- trained sft, guided wth RLs outpiut
		- sft trained model with RL disturbution, achieved on par preformance with on policy RL.
- tried to theoretically verify these things
	- in sft trying to optimize over 
		- during traiing trying to pull base distrubutiin to ground truth distrubtuion
	- in rl's case, 
		- small shift in direction of 

- SIMPO algo (based off deep u)
	- simple onffline fine tuning algo for preference based learning, with pairs of good and bad examples, offline = no rewards. 

- other things mentioned
	- previous works shown can  measure catastrophic forgetting with other things



