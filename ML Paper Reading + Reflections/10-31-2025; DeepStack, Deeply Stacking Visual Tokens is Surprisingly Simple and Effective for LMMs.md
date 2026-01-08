https://arxiv.org/abs/2406.04334

Speaker: Jim Robinson-Bohnslav


- solving you'd like to make ur images highier res going into vlm / llm decoder, but that increases sequence length, and llms have famously rhe big O proportional to square of sequence lenght, so its a bad tradeoff. u want to minimize sequence length by passing in as much visual info as possible
- so they come up with 
- lava family of models (they call them LMMs instead of VLMs)
- ..
- constrast this with lava 1 vision
	- how to deal with igh res images rn?
	- ![[Screenshot 2025-10-31 at 12.23.08 PM.png]]
	- lava 1 says: resize entire image into one small tile, feed that into llm abd blinearlerily interoplate to multiple of tilew size
	- so gets both global and high res representaiton of the iamge
	- take high res image, make it a giant long sequence and feed to llm

	- what deepstack does:
	- ![[Screenshot 2025-10-31 at 12.23.53 PM.png]]
		- downsample to global image
		- also a 4x larger variant, think as 4 larger images but no tiles overlap
		- take high res image and divide into 4 again, so u have 5 copies of the image
			- one global
			- one high res split up into 4
		- take global view, and put into input layer of the LLM
		- take 4 copies, feed into layers 2,3,4,5 with a simple residual connection with hidden states coming out of llm block


- ![[Screenshot 2025-10-31 at 12.27.57 PM.png]]
- lava 1.5 takes 576 global visual tokens in
- deepstack-L also takes in 576 seqeuence lemgth for visual tokens, byt its 5x that 2880 into first 5 layers. so 79.5 VQA 


- where do u inject visual tokens into llm? closer to decoder? 
	- no earlier u inject the better

- where to look in code if a model is using deepstack?
	- look in the image processor. 
		- u expect u take image and resize into two variants, and each of those look into image encoder and then embeddings come out
	- look for code that divides image into fourths

- deepstack 
- qwen3: one copy that comes out of any resolution native vit, take intermediate native states from VIT and add them to the first n layers of llm.
