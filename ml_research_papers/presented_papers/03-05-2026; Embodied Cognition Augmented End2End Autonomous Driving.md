
- Tshingua University

---

- AVs have tons of sensors, lidar, radar, etc
- but think about your experience as a human driver
	- driving not just exercise in geometry
- what if AVs can learn to think / feel road like you do?
- what if onboard computer can feel that spike in human anxiety at a crowded intersection
- like when you just know a pedestrian is going to step onto traffic 
- transitions from visual geometrey based driving architectures, to cognitive empathetic driving models.
- industry has spent a decade focusing on eyes of the car, but what about the intuiton

---

- new framework is E^3 AD
- paradigm shift in ml applied to robotics
- bridged gap between visual data processing and human reasoning
- modeling what human nervous system does with real time data

---

- peeling back layers to reveal transformer architectures, the contrast of learning loss functions, and intricate cross attention mechanisms
- combines highly volatile multi modal physiological data with sophisticated state of the art neural network designs

--- 
- how to put brain waves into a computer?

- clearly define problem: current bottlekneck, E2E

- gold standard: end to end autonomous driving
- E2E AD
- imagine learn complex game, only by looking at snapshots of board
	- you never get rulebook, 
	- only millions of board states and what winning player did next.
- in e2e driving model, you feed model raw sensor data, and neural network acts as a black box that outputs driving command. Sensor data in -> driving command out.
- chess analogy: how machine represents board state internally?
- what is actively happening in black box before steering command?
- main stream E2E frameworks (UniAD VAD (vectorize scene representation for effecient autonomous driving, etc) 
	- they dont process raw pixels blindly
	- rely heavily on constructing sequential visual 3d representations, using birds eye view or BEV features
	- car takes flat 2d images from cameras, stitches them to hallucinate a top down 3d map of the world around it in real time.
	- visual feature extraction network takes in all different camera angles, projects them from 2d pixel space into 3d vaxel space. (3d pixel)
	- translates them into a dynamic top down grid. computationally heavy but gives car a unified spacial understanding
	- massive catch: the exact bottlekneck highlighted in paper
	- currently BEV feature extraction networks are primarily supervised using manually annotated data
	- humans had to sit at a computer and teach ai what it was seeing frame by frame
	- massive foundational models trained on data sets where human annotated have drawn 3d bounding boxes around other cars
	- manually labeled pedestirans, mapped out semantic segmentation (coloring the drivable road grey, sidewalks green, buildings blue) to teach computer what pixels represent
	- annotated motion predicitons, drawing little vectors where cars likely to go
	- neural networks learn to extract visual info by predicting and replicating these explicit manually created labels from raw labels.
	- car trapped in world of explicit labels it was trained on? 
	- knows what a box is but what a situation actually means? 
	- only knows explicit labels, fundamentally believes driving is a geometry problem.
	- limited supervision framework severly restricts generality of the driving models
	- model trains strictly to output labeled bounding boxes, fundamentally lacks context
	- learns to recognize vehicel as 3d rectangular posessing a certain velocity vector
	- but doesnt learn to anticipate cascading hazards based on subtle context
	- no mechanism to calculate vibe of a dangerous / escalating situation. 

- where we connect academic problem to everyday expereince behind the wheel
- like when u sense a driver will do something erratic
- brain registers high level threat before they do the turn signal = this is embodied reasoning

- you arent calcualting 3d bounding boxes and velocity vectors in your head
- you're using your life experience and instincts, to predict behavior
- AI just doing high speed calculus on bounding boxes
---

core thesis: looked at this bottlekneck, posed a question:
- if fundamental problem is that autonomous systems lack human cognition and intuition
- then solution is not to draw better bounding boxes, solution is to find a mathematical mechanism to inject actual biological human cognition directly into neyral network's training pipeline
- how do we get biology into machine? 
- paper introduces actual recorded human brain EEG data, to teach car's visual netowrk what is actually improtant on the road.
- not training ai based on what human labeler clicled on. training it on what made a human driver's brain light up with electreical activity in a real car in real traffic in real time
- trying to capture invisible physiological data
- to gather data of a high enough fidelity to train a neural network, researches had to execute a highly controlled real world data collection process

---
