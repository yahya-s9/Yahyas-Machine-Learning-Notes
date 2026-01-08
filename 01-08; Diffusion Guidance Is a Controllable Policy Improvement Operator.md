
https://arxiv.org/pdf/2505.23458

- BAIR: Frans, Park, Abbeel, Levine
- introudcing a way of some kind of supervised learning but stil performance thats better than data set / reference policy
- in RL we want performance beyond the data, but could be limited if ur doing behsbior cloning, you end up at par with the expert but not beter tban expert
- scaling RL is tricky
- trying to combine geenrateive modeling (diffusion / flow matchuing) along with policy improvement 
- CFGRL is result: trained with supverised learning but ends up better than the data

- not claiming that it's a state of the art RL policy
- but offline data, schema to extract policy from, this scheme could be better than reward weighted regression and advantage weighted regressed

- trying to have some kind of polocy extraction mechanism, if we have the policy as a product of reference policy and optmiality distrubution then its a policy improvmeent oeprator, if you apply it on an exisiting policy then it is better.
- diffusion comes in because they are tryting to formulate eveyrhting as a distrubution and we can sample from disturbution using diffusion modeling technqiues
- classifier free guidance
- trying to propose a policy improvement operator, thats better than the exisiting one
- offline rl not online


--- 

- they are also proposing a simple way to use RL on top of any diffusion based behavor cloning policies as well

-----

- in RL we try to maximize expected return given byu the formula
- policy prompvement oeprator: we can assume value function should be more than previous policy, like it made it better
	- update from reference policy to new policy such that RL objective does not decrease
- if advantage is more than zero, its a policy improvement
- IN RL we're trying to maximize returns 
- KL regularized policy 
	- new policy regularized by reference policy weighted by advantage function

--- 

proposal: diffusion guidance is a controllable policy improvement operator

- parametrize policy as product of reference policy and optimality function
	- optimality function is a function of advantage
	- how do they define the f?
		- remember its monotonic increasing or non increasing function of advantage


- we are trying to rescale distribution based on which actions are better based on advantage that is there, rescaling distribution 

- unconditional policy -> optimality conditioned policy -> optimality guided policy

- improvement of product policies
	- if f is a monotonic increasing function of advantage then product is an improvement of reference policy
- further improvement via attenuation
-

--- 

Classifier free gudiance 
- i dont need to have a seperate classifier being trained
- given image tell me if its a cat or dog


--- 

classifier free guidance rl:
- ...


--- 


Questions:
- what is advantage? objective? advantage weighted regression? flow matching? classifier guidance ? classifier guidance free (training framework for diffusion models)?
- behavior cloning? attentuation?
- diffusion or flow matching denoises actions ?
- - conditional and unconditional distributions?
- diffusion model?
- 