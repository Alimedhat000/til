Great! Here's a concise yet comprehensive set of notes on **HyperLogLog** suitable for revision, reports, or documentation:

## HyperLogLog (HLL) Notes

### Purpose

- Probabilistic algorithm to **estimate the cardinality** (number of distinct elements) in a multiset.

- Uses **sublinear space** (e.g., kilobytes for billions of elements).

### Core Idea

- Based on the observation that the **maximum number of leading zeros** in hashed values gives an estimate of cardinality.

- Hash elements and record how many leading zero bits appear in the hashed value.    

### Algorithm Overview

1. **Choose `n_bits`**: Number of bits used for register indexing.
    - Number of registers `m = 2^n_bits`.
        
2. **Hash each element**:
    - Hash value `h` → binary string.
    - First `n_bits` → index of register `j`.
    - Remaining bits → used to calculate `ρ(w)` = number of leading zeros + 1.        

3. **Update Registers**:
    - `registers[j] = max(registers[j], ρ(w))`

4. **Estimate cardinality**:
   $$E = \alpha_m \cdot m^2 \cdot \left( \sum_{j=1}^m 2^{-M[j]} \right)^{-1}$$
	- $\alpha_m$ is a bias correction constant depending on `m` generally `0.79402` .


---

###  Correction Steps

1. **Small Range Correction (Linear Counting)**: If `E <= 2.5 * m` and there are empty registers:
    $$
    E' = m \cdot \log \left(\frac{m}{V}\right)
    $$
    where `V` is the number of zero-valued registers.    
2. **Large Range Correction (optional)**: Apply corrections for very large cardinalities to avoid overflows.


### 🔢 Constants

|m|αₘ|
|---|---|
|16|0.673|
|32|0.697|
|64|0.709|
|>64|0.7213/(1+1.079/m)0.7213 / (1 + 1.079/m)|

### ✅ Advantages

- Very memory efficient.
- Fast insertions and union operations.
- Good accuracy with small space.
### ❌ Disadvantages
- Only estimates — not exact.
- Poor accuracy for small cardinalities unless corrections are used.

### 🧪 Use Cases
- Unique user counting.
- Network analytics.
- Distributed systems — HyperLogLog is **mergeable**!
