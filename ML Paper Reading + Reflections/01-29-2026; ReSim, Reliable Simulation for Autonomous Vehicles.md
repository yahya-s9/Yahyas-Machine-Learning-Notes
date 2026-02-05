High Level Understanding


- intuition: ur driving, you see a ball and imagine a child is coming, if I don’t hit brakes I’ll hit the brakes
- World model = your brain's internal physics engine predicting future based on current trajectory and environment 
- Want to give this to ai 
- Most ai drivers suck at this kind of imagination 
- Ask Ai to imagine a car crash and it refuses 

- it’s a huge safety problem 
- solving good driver paradox: 
- What is a world model for AVs?
- Why do current models suffer from positive hallucinations

- good driver paradox:
- Ai refuses to dream of danger,
- U train Ai on millions of hours, but most videos are mostly expert data, they are safe drivers 
- When u train world model (basically video generator predicting next couple moments)
- It learns 
- Data is mostly safe 
- So if u ask it like hey simulate if I turn steering wheel 90° right into the tree, it can’t, it’s never seen someone do that, it hallucinates a safe path 

- lack of action controllability = you tell it to crash and it generates safe driving 
- But this sucks for planning. If ai can’t simulate consequences of a bad action, then it can’t plan to avoid it 
- Want to create a reliable world model. If I tell it to drive off a bridge it does that 

World model?

- video prediction engine, give it last couple seconds and an action, it generates next two seconds of video 
- Goal here is physically consistent, respect physics, it’s a simulator that learn from watching reality 
- Why can’t we use normal sim like gta? 
- Traditional simulators never look like real world 
- Want ai to understand visual complexity of real world, so we need to learn from video 
- Need physics of a game engine, onto visual reality of video 
- Prior models like vista or gen ad were great at photo realism but failed action controllability test, good driver bias 
- So with those If scenario is highway driving and u say turn right, 
  

How to break bias?

- do they hire stunt drivers for data set?
- Re sim: mix reality with sim 
- Use carLLA which is a sim engine for av research, looks like a game but perfect physics 
- Pile A: real world driving, OpenDV, Nfims, tons of videos of expert data: teaches how real city looks like
- Pile B: non expert data, diverse driving policies. Bunch of agents driving at different angles and speeds, colliding with each other, half safe half crazy. 
- 88k clips from carLla half were hazardous 
- If u feed ai 88k of cartoon crashing willv it not generate a cartoon? How does it generate a photo realistic crash?
- Challenge of sim to real 
- Want model to learn physics consequences from sim but apply them to visuals of real cities 
- Needs to learn the concept of a crash from the game , simulated data provides physics

  

  
----

- had to build architecture to force model to pay attention to right things. Now section 3 let’s look under the hood
- Using a diffusion transformer 
- CogVideoX 
- Don’t reinvent the wheel, cogvideoZ is open source video gen model, good at videos from text, understands what car is what road is 
- Pre trained model as foundation 
- Smart artist basically 

  

- artist doesn’t know how to drive 
- Default cogvideoX takes text prompts 
- Resin need to take specific driving instructions 
- New conditioning system 
- Giving it hints, rules, constraints 
- 3 things in to control video gen, the history, the last 9 frames of video to set the scene. 2nd text prompts using encoder called T5 (llm component from google), bc high level intent matters, command like turn left gives model semantic understanding of what should have which helps guide vide gen at high level, primes the brain, 3rd: the trajectory. The specific action the sequence of future x y coordinates where we want car to be in next few seconds. Ur next 5 positions will be here here here here here it’s a path. 
- Video mode deals In pixels, trajectories are numbers, the use a special trajectory encoder which takes coordinates and MAs then int same mathematical space the latent space as the video noise. Draws an invisible path inside the neural network that the video gen has to follow 
- Ai staring into static and clearing the fog, denousing from pure noise 
- Has static, inputs in ear, text says turn left, trajectory says go to x y, clear fog to make video to match all that. 
- Twist: inputs isn’t enough. Even with inputs, model would still ignore trajectory if it looked unsafe. 

They had to change math of how model learns. Dynamics consistency Loss ?

- it’s ab motion. Standard diffusion training checked the image quality, compares generated pixel to real one. Is car red, etc. but that static doesn’t check momentum, resim adds a loss function, a penalty that looks at latent motion
- Model isn’t just generating pixels, it’s working in compressed latent space, mathematical shorhtand for image
- Dynamics consistently loss looks at vector difference between two frames in that space
- Asks did representation of car move in a way that matches the trajectory input 
- not just asking is car in picture, It’s calculating the velocity vector in abstract space, did the car move ten feet to the left like I told it too 
- Forces model to respect the physics 
- If trajectory says move left, but video shows car going straight, loss function screams wrong and penalizes model heavily during Trianing, forces fog clearing to prioritize motion path above all else
- Enforced physics part of sim

Another trick:
- unbalanced noise sampling. 
- In diffusion models you add noise to image to train model to remove it. You can add a little or a lot of noise. You can a little noise, or a lot. Diff levels of difficulty. 
- Usually you sample noise uniformally. You train on all levels of noise equally
- researchers realized that different levels of noise control different things
- high levels of static: where model decides high level structure like is there a raod, is the car turning, big picture. structure.
- low levels of noise: high frequency details, texture of asphalt, leaves on tree, etc. texture. 
- Car crash or sharp turn: is that texture or structure? it's a structural one.
- ReSim unbalanced sampling spends more training time on high noise strucutal steps. like studying for test and focusing on hard parts of video generation rather than getting distracted by perfecting texture of a cloud
- so when crash happens, it structually renders impact

- build so far:
- model trained on mix of real video and drunk driver sim data
- generic video brain (cog video x) condition by trajectory math
- training it with penalty that obsesses over motion vectors and structural changes.
- result is a video generator that can show us a crash, but.. we aren't done.



---- 

- AI can generate a crash but does it know crashing is bad?
- if we want to use this for planning to help cars, we need to score these videos.
- video A is safe, video B is a firey wreck,
- section 4: the judge.

- video2reward. separate AI model entirely.
- architecture: use dynov2 as backbone, self distillation with no labels.
- computer vision model from meta, it does exceptionally well at object permanence and understanding geometry of scene without being told what things are
- learns features not just labels.
- has good eyes.

- they take dyno model and freeze it, they don't change brain, they just add prediction head on top of it
- freeze it bc dino already knows what a car is it's a foundational model
- dont need to retrain whole thing on sim data, u can break it, u just want to teach it a new trick, scoring safety
- how does learn rules of game? go back to simulator CarLA
- in simulator we have ground truth for safety, it has infraction scores
- train video to reward on sim videos paired with these scores
- when u see this chaotic visual it's a zero, smooth lane keeping is a 10.
- skepticism check: training judge on cartoons.
- can a judge trained on cartoons accurately score a photorealistic video from ReSim
- this is why they used dyno2, a foundational model that looks at features of image, relations between objects, not just texture
- crash in cartoon is two objects intersecting rapidly, same with real life
- dino learns what a crash is, abstracts away the texture is

- creates a universal metric for danger.
- 1) planner proposes action: turn left
- 2) ReSim imagines video of turning left
- 3) video2reward watches video and scores it
- 4) if score high, car takes action

---------------


testing?
- section 5.
- testing on new scenes, famous data set ReSim never saw during training

- gold standard, if u train on openDV and test on new scenes, usually perf drops bc cameras different and cities different
- ReSim beat competitors, some even trained on new scenes.
- metrics? scoring realism: FiD and FeD
- FiD measures image quality: does this frame look like an image
- FvD measures temporal consistency: does video flow like real video
- lower is better.
- ReSim scored significantly lower than baselines on both

- What about action controllability? it makes good videos but does it listen to bad actions when asked to imagine them?
- set up a test scenario: given dangerous command that a human would never follow
- competitor vista saw, received dangerous command and hallucinated safe driver
- ReSim generated video of car driving off the road, respected command even tho resulted in failure state
- measured this using "TrajectoryDifference"
- ReSim action following by over 50% compared to Vista
- massive jump in reliability.

if testing saftey system u need to know if planner would hit the barrier, u dont want a simulator that lies to u and says dont worry we missed it


- showed humans videos and asked which more realistic and which followed command better, ReSim won in both

---- 

- So have reliable simulator and reliable judge.
- how does this sit in a car? applications in section 6.
- application 1: planner
	- use inverse dynamics model (IDM)
	- usually car thinks i see road i turn wheel (thats forward dunamics)
	- 1) resim imagines video of car reaching desitnation with a perfecr safe trajectory
	- 2) IDM sees video and asks what steering and gas produces this?
	- 3) reverse engineers driving from dream
	- tested this on navisim benchmark and beat state of the art planners like UNiad
	- beating Uniad with a generative video model
	- shows dreaming of future first is a powerful way to plan

- application 2: reward guided policy selection
	- ur driving on highway with 2 junior AI drivers.
	- ur approaching a merge
	- junior driver A (policy A) is aggresive says speed up and cut ppl off
	- policy B is cautious says brake and merge behind
	- which one do u listen to?
	- in traditional system you'd listen to one with a higher confidence score
	- with reSim you simulate both, show me videos of both, video2reward watches both, gives crash video a zero and safe video a 10. system chooses safe policy.
	- it's like Dr Strange looking at 14 million futures and picking one where we don't die.
	- in experiments using the selection process boosted perf of weak policy by 55.3%
	- turned mediocre drivers into great ones by checking their homework.
	- its like a saftey net for other AIs

- application 3: closed loop sim
	- imagine u want to test a new self driving software, usually u put it on a real car or run it against recorded data set.
	- with ReSim you can build a new future, the car acts, resim generates new frame based on that action, car sees new frame acts again, resim generates a new frame, etc etc.
	- so can run minutes all in AIs imagination
	- figure 8: policy driving through virtual city generated by ReSim


if this is so good why not in tesla? limitations

- speed. generating 4 second video takes 2 minutes.
- on a massive NVIDIA M100 GPU
- so cant use this driving down highway 60 mph 
- no where near real time
- diffusion models are slow by nature
- fog clearing process takes many steps
- u can't use in car to make split second decisions


- main use is offline training or selecting policies or running closed loop saftey validations
- training tool not a co pilot yet.
- there are some models that can do diffusion in 1-2 steps.
- not impossible that in 5 years this can be real time

- What about visual glitches? 
- still a diffusion model that can hallucinate.
- but reliable enough to be useful.
- for saftey testing physics is what matters most.
- we have unpacked the good driver bias, idea that data too safe, 
- looked at reliable world simulation
- mixing cartoons with reality to fix that bias
- seen the judge video2reward that keeps the whole thing honest.

- moves us from AIs that just copy behavior to AI that understand mechanics of driving




- ReSim teaching it to anticipate failure to imagine negative consequences.
- that's what fear is no?? fear is simulation of bad future so you avoid it.
- do we need to teach AVs to be afriad?
