# TP4-Softwar-Evolution

## Section 1.2.4

### Question 1 : 
The probability is virtually zero. SHA-256 is designed to be collision-resistant; even with the massive computing power of the entire Bitcoin network, it would take roughly 18 billion years to have a 50% chance of finding a single collision


### Question 3 : 
No, this is not recommended for strict reproducibility. TAR archives are not inherently reproducible because they record build-specific metadata such as timestamps, file ownership, and permissions, which vary across different systems and build environments. To make a tarball reproducible, one must explicitly normalize or strip this metadata using specific flags

## Section 2.2.1

### Question 1 :
Metadata can be found using ls -l (size, permissions) and file hello-world (architecture, e.g., x86_64). Standard compilation generates intermediate object files (.o), though they are often deleted automatically by the compiler unless specified otherwise

### Question 2 :
No, the binary hash changes because the __DATE__ and __TIME__ macros embed the exact moment of compilation into the executable

### Questoin 3 : 
Yes, because the timestamp strings are fixed within the compiled machine code

### challenge : 
By defining the SOURCE_DATE_EPOCH environment variable (e.g., export SOURCE_DATE_EPOCH=1700000000), we provide a fixed reference point for the compiler. Modern versions of GCC recognize this variable and use it to replace the non-deterministic values of the __DATE__ and __TIME__ macros. This ensures that the resulting binary remains bit-for-bit identical across multiple compilations, effectively achieving build-time reproducibility despite the use of timestamps.
