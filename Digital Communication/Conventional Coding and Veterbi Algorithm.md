
## Conventional Coding

An Error correction technique used to add redundancy to the transmitted data. This redundancy helps detect and correct errors introduced by noisy channels.

##### Key Concepts

- A convolution encoder takes a stream of bits as input and produces output by combining the current and previous input bits using modulo-2 addition

- **Code Rate:** Ratio of input bits to output bits.

- **Constraint Length (K):** Number of bits in the encoder's memory. Determines how many past bits affect the output.

- **Shift Register:** Stores past input bits. Used to perform bitwise convolution with generator polynomials.

- **Generator Polynomial:** define which bits are used for the modulu-2 addition
	- **Example:**
		- For a rate 1/2 encoder
		
		- **Constraint Length (K):** 3 (This means the encoder looks at the current bit and the two previous bits — so it uses a 3-bit shift register.)
		
		- Generator polynomials:
			- $G_1$ = 111 -> This means we perform modulo-2 addition (XOR) of the current bit, the previous bit, and the bit before that — all three bits in the shift register.
			
			- $G_2$ = 101 -> This means we XOR the first and third bits in the shift register (current and two steps back), skipping the middle one.
		
		- **Explanation:**
			- Each input bit enters the shift register.
    
			- Two output bits are produced for each input bit:
			    - One from $G_1$​ and one from $G_2$​.
				
			- These output bits are the parity results of the generator patterns.


	- Number of Generator Polynomials is determined by the rate and the length is determined by the constrain length i.e. how much does the shift reg hold. 

>*You can think of convolutional coding as computing the **parity** of an input sequence using **two or more different bitwise patterns** (generator polynomials).*
>
>*Where 0 is for even parity and 1 for odd*.
>
>*Each pattern checks a different **"view" or "combination"** of the input history (held in the shift register), and outputs a corresponding bit.*"

![[Pasted image 20250517155314.png|center]]


## Trellis Diagram

A **trellis diagram** is a time-expanded representation of a convolutional encoder's finite state machine. It illustrates all possible state transitions over time, making it an essential tool for decoding algorithms like the Viterbi algorithm.

##### Key Concepts
- **States**
	- Each state represents the contents of the encoder's shift register.
    
	- For a constraint length $K$, there are $2^{K−1}$ possible states.
    
    - Example: If $K=3$, there are $2^{3−1}=4$  states: 00, 01, 10, and 11.

- **Transitions (Branches)**
	- Each transition represents a possible input bit and the resulting output bits.

	- It's a Binary state so each state has two transitions
	    
	- Transitions are labeled with the input/output pair, e.g., 0/00 or 1/11.
	    
	- Solid lines often denote input '0', while dashed lines denote input '1'.

- **Constructing a Trellis Diagram**

	1. **List All States**: Determine all possible states based on the constraint length.
	    
	2. **Determine Transitions**: For each state, identify possible next states for input bits '0' and '1'.
	    
	3. **Label Transitions**: For each transition, label with the input bit and corresponding output bits.
	    
	4. **Repeat for Each Time Step**: Extend the diagram over the desired number of time steps.
	   
	   
   ![[Pasted image 20250517164335.png]]
   
An actual encoded sequence can be represented as a path on this graph. One valid path is shown in red as an example.

This diagram gives us an idea about _decoding_: if a received sequence doesn't fit this graph, then it was received with errors, and we must choose the nearest _correct_ (fitting the graph) sequence. The real decoding algorithms exploit this idea.


### Veterbi Algorithm

Now to the big boss **THE VETERBI ALGORITHM**

The Viterbi algorithm is a dynamic programming method used to decode convolutionally encoded data. It finds the most likely sequence of input bits that produced a received sequence.

Simply what it does is as every timeslot it computes the metric (some value to choose the optimal path) i.e. , for each state. This optimal path is called the survivor path.

- For Example: 
	- Here at time slot 5 
	- What the Trellis diagram says is that the optimal way to get to 
		- 00 is through the state 00
		- 01 is through the state 10
		- and so on.
![[Pasted image 20250517165712.png|center]]

- After computing the best survivor path for each possible state through all the time states.
- After all bits are processed, trace back from the state with the smallest metric to find the most likely input sequence.


After all bits are processed, trace back from the state with the smallest metric to find the most likely input sequence.


### Path Metrics

- **Hamming Distance:** Used when the channel is binary symmetric (i.e., noise causes bit flips). => We are using this. 
    
- **Euclidean Distance:** Used for soft decision decoding (e.g., using signal levels).