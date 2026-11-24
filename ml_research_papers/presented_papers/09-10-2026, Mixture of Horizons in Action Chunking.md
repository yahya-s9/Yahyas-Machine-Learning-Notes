

Problem

- **The Horizon Trade-Off**: Vision-Language-Action (VLA) models use **action chunking** to predict a sequence of future actions. However, using a single fixed prediction length ("horizon") forces a trade-off:
    - **Long horizons** enhance long-term planning and global foresight on complex tasks, but sacrifice low-level motor precision1more_horiz.
    - **Short horizons** yield precise motor control for local spatial tasks, but lack global foresight1more_horiz.
- **Generalization Bottleneck**: Choosing any fixed single horizon limits policy flexibility and creates an inherent bottleneck across diverse task suites36.

---

Solution

- **Mixture of Horizons (MoH)**: A universal, plug-and-play strategy that predicts and fuses multiple temporal horizons simultaneously within a single model17.
- **Shared Action Transformer & Gated Fusion**:
    - The vision-language model (VLM) prefix processes visual observations and instructions once89.
    - A lightweight **shared action transformer** processes multiple horizon segments in parallel79.
    - A lightweight **linear gating head** (~2,000 parameters) evaluates hidden states at each step to compute step-wise, horizon-normalized weights for a final fused action prediction [16, 30–32].
- **Gating Balance Loss (**$L_{bal}$**)**: A regularization penalty based on the coefficient of variation across gating weights, preventing gate collapse to preferred horizons and enforcing balanced utilization across timescales1011.
- **Dynamic Inference via Cross-Horizon Consensus**: Treats predictions across horizons as voters and measures their $\ell_1$ disagreement1213. The robot executes long action sequences continuously when horizons agree (low-risk/straightforward motion) and truncates execution early to replan when disagreement arises (fine manipulation or decision points)12more_horiz.

---

Results

- **Simulation Benchmarks**:
    - **LIBERO**: MoH provided consistent gains across both flow-matching ($\pi_0$, $\pi_{0.5}$, StarVLA) and one-step regression ($\pi_{reg}$) models, achieving a state-of-the-art **99.0% average success rate** with $\pi_{0.5}$16more_horiz.
    - **RoboTwin 2.0 & RoboCasa**: Improved success rates and generalization across 50 bimanual tasks under heavy domain randomization, as well as complex household manipulation tasks in RoboCasa19more_horiz.
- **High Efficiency & Throughput**: Dynamic inference achieved **2.5× higher execution throughput** on LIBERO-Long while maintaining superior performance and adding negligible compute or latency overhead1more_horiz.
- **Real-World Validation**: Demonstrated on a physical 7-DoF robot arm across four manipulation tasks (e.g., pouring milk, folding towels), reducing action hesitation and improving fine-grained control2324.

----


intuition:
- LLM generate text
- vlm look at image and describe it
- vla: vision language action model

VLA:
- to make a VLA, take a pre-trained VLM, attach an action transformer
- converts language tokens into physical rotation of a joint.
- maps HD mathematical representations of objects to LD continuous motor space
- action transformer takes output embeddings from VLM (mathematically represented understanding of the scene) & projects them into physical coordinates (x, y, z, pitch, yaw, roll, claw state).

- action transformers predict numerical values for joints to achieve the task

- How often are action transformers making these predictions?
	- ex: camera @ 60 fps. is action transfromer predicitng new motor command every single frame and stuttering?
	- predict the next frame every frame. stuttering. 
	- move 1mm, stop, calc, move...
	- this is solved by action chunking.


Action Chunking:
- instead of predicting 1 frame, predict chunk of frames
- instead of outputting 1 action vector for current millisecond, action transformer observes once, predicts a sequential matrix for actions it will take 
- outputs chunk of movements at once
- action chunking allows for smooth motor control because you pre-planned

- what is the problem with action chunking? 
- the horizon: how long into the future is this chunk.
- historically horizon is a fixed parameter on the VLA.

problem: locking model to one horizon, bad at mixed horizon tasks

- ex: pyzero model tested on libero benchmark. two test suites:
	- spatial suite: short horizon. threading a needle.
	- long trajectory: long horizon complex goals.

- trained 3 pyzero models on 3 horizons: 10, 20 and 30 steps.

- long horizon (30 step) model did well on long trajectory test suite, but bad at short horizon.
- short horizon (10 step) model did well on spatial test suite, but bad at long horizon.
- medium horizon (20 step) bad at both.


- If you lock into one horizon, you're choosing your weakness.
	- long horizon = clumsy up close
	- short horizon = lose big picture
	- medium horizon = bad at both

Solution: Predict both at the same time


Architecture of MOHA:

- VLM processes visuals once, action transformer outputs action chunk for one horizon.
- MOH action transformer outputs different horizons' action chunks, at the same time.

- does MOH add a large compute overhead? 
	- no bc tensor parallelism. 
	- most of the compute is processing the image. thats shared overhead, the VLM does it once.
	- the motor predictions from the shared action transformer is lightweight in comparison 
	- tensor parallelism lets VLM output feed the shared action transformer. 
	- shared transformer completes 3 predicitons distributing across the cores of the GPU
	- so system shares everything until the end, then motor predicition is cheap.

Now that you have multiple action chunks for different horizons.. which to pick?
- we need to benefit from the mixture of horizons
- mechanism that resolves this is "gated mixture"
- lightweight (2k parameter) layer after the action transformer outputs.
- for every step, evaluate each horizon's prediction based on visual context
- output a confidence rating, a % to each prediction 
- if robot needs extremee precision, then assign 90% weight to short horizon's action chunk.
- then final motor command is a weighted sum of all three predicitons

what about gate collapse? "lets do the X step plan every single time no matter what"
- problem in mixture of expert architectures
- how to fix a lazy gate?
- regularization term during training called the "balance loss"
- regularization = mathematical penalty during backprop in training
- when you add a regularization term: want to minimize loss that comes from regularization
- how does it force to use all horizons?
- calculate the coefficient of variations across the gating weights
- monitor how often you choose each horizon, if gate favors one, std of weights increases
- regularizatoon = mathematical penalty during backpropagation 
- if you favor a plan too much, penalized
- only way model can mininmize the penalty is by ensuring diverse distribution of policies chosen during training
- forcing cognitive diversity

Result is dynamic inference from cross horizon consensus:
- usually if just 1 horizon, you're pre comitting to the action chunk, you-re locked in
- MOH is running multiple horizons, so you have internal metrics of confidence
- cross horizon consensus: 
	- @ each time step: measure variance bw horizons' plans
	- ex: 
		- clear road, both 10s micromanager and 30s visionary say go forward
		- variance is low, they all agree
	- when disagreement bw plans low enough, dynamic inference allows you to keep processing plans after another without pausing to input new visual data

	- if they disagree: you've reached state of uncertainty. 
		- truncate: cut action chunk short @ point of disagreement.
		- stop, get a new image, feed into vlm, get a new set of action chunks per horizon.

- dynamic inference = slow down when need to, speed up when safe.
- dynamic inference achieved 2.5x throughput with no degradation to precision and success rate. 

- but what about extra compute?
	- now we're checking for consensus always, calc disagreement thresholds, dynamically altering execution length on the fly.  
	- ablation study show'd math is light
	- MOH adds 1.4% compute overhead during training, and negligible latency.



Results

- tested MOH on both
	- standard regression model
	- flow matching policies

- standard regression model:
	- draw straight line from input to output
	- see image, output motor coordiante
	- struggles with multimodal possibilities like multiple ways to grab a cup

- flow matching:
	- generative approach
	- kind of like diffusion models for image generation
	- formulated around "continuous normalizing flows"
	- dont predict directly. learn vector field that smoothly wraps random distribution of noise into distribution of robot actions.
	- sculpt trajectory out of mathematical noise

- MOH on flow matching on libero benchmark, 99% avg success rate across complex spatial (short horizon) & long trajectory (long horizon) tasks


Ablation studies: break MOH, remove pieces and observe degradation

- MOH just brute force? diversity vs ensemble?
	- fed it 3 identical horizons
	- if ensemble theory correct: avg(3 identical policies) still improves it
	- but no, barely improved. model still bad at short term tasks
	- perf leap comes from temporal diversity.
- What if you take gating out?
	- keep diverse horizons, but just divide them by 3
	- uniform average across all time steps
	- dealing with continious motor spaces
	- simple average was still an improvement
	- proves core concept: fusing multiple timescales creates more robust action seqeuence
	- dynamic gate just imrpoved on average
- max horizon limit?
	- why stop at 30 steps? why not 50