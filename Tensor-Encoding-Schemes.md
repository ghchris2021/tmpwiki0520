
There isn't really a real writeup of all the mapping, but this should hopefully be a good central starting point if any maintainers needs some understanding of when each feature was added and the general specs of each. Updating it with more accurate information is greatly appreciated

## Tensor Naming Scheme

This is not definitive, but is helpful when reading sourcecode or console output to understand what each means typically.

- `<Encoding>_<Variants>`
  - `<Encoding>` : This defines the most common encoding of individual weights in the model
	- Floating Point Formats:
	  - `BF16`: [16-bit bfloat16](https://en.wikipedia.org/wiki/Bfloat16_floating-point_format) [Google Brain](https://en.wikipedia.org/wiki/Google_Brain) truncated form of 32-bit IEEE 754 (1 sign bit, 8 exponent bits, 7 fractional bits)
	  - `F64`: [64-bit IEEE 754](https://en.wikipedia.org/wiki/Double-precision_floating-point_format) floats per weight (1 sign bit, 11 exponent bits, 52 fractional bits)
	  - `F32`: [32-bit IEEE 754](https://en.wikipedia.org/wiki/Single-precision_floating-point_format) floats per weight (1 sign bit, 8 exponent bits, 23 fractional bits)
	  - `F16`: [16-bit IEEE 754](https://en.wikipedia.org/wiki/Half-precision_floating-point_format) floats per weight (1 sign bit, 5 exponent bits, 10 fractional bits)
	- Integer formats:
	  - `I<X>`: X bits per weight, where `X` could be `4` (for 4 bits) or `8` (for 8 bits) etc...
	- Quantized formats:
	  - `Q<X>`: X bits per weight, where `X` could be `4` (for 4 bits) or `8` (for 8 bits) etc...
	  - `KQ<X>` (or `Q<X>_K`) : k-quant based models. X bits per weight, where `X` could be `4` (for 4 bits) or `8` (for 8 bits) etc...
	  - `IQ<X>`: i-quant based models. X bits per weight, where `X` could be `4` (for 4 bits) or `8` (for 8 bits) etc...
  - `<Variants>`: This represents different strategies of packing quantized weights into a gguf file. This is because we may want a mix of different bit sizes for weights of varying importance, or we may be encoding a general offset to a block or super-block. This may be omitted if trivial or initial attempt, refer to encoding scheme name table for details.

## Tensor Scheme Mapping

| Scheme   | `ggml_ftype` C enumeration name | `ggml_type` C enum name | Bits/Weight | Data Type                     | Block Configuration                                                    | Quantized Weight Formula                        | Initial Commits Or Pull Request Sources (of `ggml_type`)                 |
| -------- | ------------------------------- | ----------------------- | ----------- | ----------------------------- | ---------------------------------------------------------------------- | ----------------------------------------------- | ------------------------------------------------------------------------ |
| BF16     | GGML_FTYPE_MOSTLY_BF16          | GGML_TYPE_BF16          | 16          | bfloat16 (trunc 32b IEEE754)  | Homogonous Array Of Floating Weights                                   | -                                               | [llama.cpp PR: Introduce bfloat16 support #6412](https://github.com/ggerganov/llama.cpp/pull/6412) |
| F16      | GGML_FTYPE_MOSTLY_F16           | GGML_TYPE_F16           | 16          | 16-bit IEEE 754               | Homogonous Array Of Floating Weights                                   | -                                               | [llama.cpp CM: Initial Release](https://github.com/ggerganov/llama.cpp/commit/26c084662903ddaca19bef982831bfb0856e8257) |
| F32      | GGML_FTYPE_ALL_F32              | GGML_TYPE_F32           | 32          | 32-bit IEEE 754               | Homogonous Array Of Floating Weights                                   | -                                               | [llama.cpp CM: Initial Release](https://github.com/ggerganov/llama.cpp/commit/26c084662903ddaca19bef982831bfb0856e8257) |
| F64      | -                               | GGML_TYPE_F64           | 64          | 64-bit IEEE 754               | Homogonous Array Of Floating Weights                                   | -                                               | [llama.cpp CM: Add support for I64 and F64 arrays #6062](https://github.com/ggerganov/llama.cpp/pull/6062) |
| I8       | -                               | GGML_TYPE_I8            | 8           | (signed?) integer             | -                                                                      | -                                               | [llama.cpp PR: Designate enum vals for integer types #6050](https://github.com/ggerganov/llama.cpp/pull/6050) |
| I16      | -                               | GGML_TYPE_I16           | 16          | (signed?) integer             | -                                                                      | -                                               | [llama.cpp PR: Designate enum vals for integer types #6050](https://github.com/ggerganov/llama.cpp/pull/6050) |
| I32      | -                               | GGML_TYPE_I32           | 32          | (signed?) integer             | -                                                                      | -                                               | [llama.cpp PR: Designate enum vals for integer types #6050](https://github.com/ggerganov/llama.cpp/pull/6050) |
| I64      | -                               | GGML_TYPE_I64           | 64          | (signed?) integer             | -                                                                      | -                                               | [llama.cpp PR: Add support for I64 and F64 arrays #6062](https://github.com/ggerganov/llama.cpp/pull/6062) |
| Q4_0     | GGML_FTYPE_MOSTLY_Q4_0          | GGML_TYPE_Q4_0          | 4           | round to nearest quantization | Each block has 32 weights                                              | w = q * block_scale                             | [llama.cpp CM: Initial Release](https://github.com/ggerganov/llama.cpp/commit/26c084662903ddaca19bef982831bfb0856e8257) |
| Q4_1     | GGML_FTYPE_MOSTLY_Q4_1          | GGML_TYPE_Q4_1          | 4           | round to nearest quantization | Each block has 32 weights                                              | w = q * block_scale + block_minimum             | [llama.cpp CM: Initial Release](https://github.com/ggerganov/llama.cpp/commit/26c084662903ddaca19bef982831bfb0856e8257) |
| Q4_1_F16 | GGML_FTYPE_MOSTLY_Q4_1_SOME_F16 | -                       | 4           | round to nearest quantization | Each block has 32 weights (token embedding and output weights are F16) | w = q * block_scale + block_minimum             | [llama.cpp CM: add Q5 WASM SIMD + GGML_FTYPE](https://github.com/ggerganov/llama.cpp/commit/6bc4400e67e6bc4faad3ad3d5e9d8a6576a9752d) |
| Q8_0     | GGML_FTYPE_MOSTLY_Q8_0          | GGML_TYPE_Q8_0          | 8           | round to nearest quantization | Each block has 32 weights                                              | w = q * block_scale                             | [llama.cpp PR: Add Q8_0 quantization format (rename the old one to Q8_1) (ARM NEON) #1179](https://github.com/ggerganov/llama.cpp/pull/1179) |
| Q8_1     | -                               | GGML_TYPE_Q8_1          | 8           | round to nearest quantization | Each block has 32 weights                                              | w = q * block_scale + block_minimum             | [llama.cpp PR: Add Q8_0 quantization for intermediate results #951 (Note: Renamed to Q8_1 in later commit)](https://github.com/ggerganov/llama.cpp/pull/951) |
| Q5_0     | GGML_FTYPE_MOSTLY_Q5_0          | GGML_TYPE_Q5_0          | 5           | round to nearest quantization | Each block has 32 weights                                              | w = q * block_scale                             | [llama.cpp PR: Add Q5_0 and Q5_1 quantization #1187](https://github.com/ggerganov/llama.cpp/pull/1187) |
| Q5_1     | GGML_FTYPE_MOSTLY_Q5_1          | GGML_TYPE_Q5_1          | 5           | round to nearest quantization | Each block has 32 weights                                              | w = q * block_scale + block_minimum             | [llama.cpp PR: Add Q5_0 and Q5_1 quantization #1187](https://github.com/ggerganov/llama.cpp/pull/1187) |
| KQ2      | GGML_FTYPE_MOSTLY_Q2_K          | GGML_TYPE_Q2_K          | 2.5625      | k-quantization                | Superblocks has 16 blocks ( 16 weights per block)                      | w = q * block_scale (4-bit) + block_min (4-bit) | [llama.cpp PR: k-quants #1684](https://github.com/ggerganov/llama.cpp/pull/1684) |
| KQ3      | GGML_FTYPE_MOSTLY_Q3_K          | GGML_TYPE_Q3_K          | 3.4375      | k-quantization                | Superblocks has 16 blocks ( 16 weights per block)                      | w = q * block_scale (6-bit)                     | [llama.cpp PR: k-quants #1684](https://github.com/ggerganov/llama.cpp/pull/1684) |
| KQ4      | GGML_FTYPE_MOSTLY_Q4_K          | GGML_TYPE_Q4_K          | 4.5         | k-quantization                | Superblocks has  8 blocks ( 32 weights per block)                      | w = q * block_scale (6-bit) + block_min (6-bit) | [llama.cpp PR: k-quants #1684](https://github.com/ggerganov/llama.cpp/pull/1684) |
| KQ5      | GGML_FTYPE_MOSTLY_Q5_K          | GGML_TYPE_Q5_K          | 5.5         | k-quantization                | Superblocks has  8 blocks ( 32 weights per block)                      | w = q * block_scale (6-bit) + block_min (6-bit) | [llama.cpp PR: k-quants #1684](https://github.com/ggerganov/llama.cpp/pull/1684) |
| KQ6      | GGML_FTYPE_MOSTLY_Q6_K          | GGML_TYPE_Q6_K          | 6.5625      | k-quantization                | Superblocks has 16 blocks ( 16 weights per block)                      | w = q * block_scale (8-bit)                     | [llama.cpp PR: k-quants #1684](https://github.com/ggerganov/llama.cpp/pull/1684) |
| KQ8      | -                               | GGML_TYPE_Q8_K          | 8.0         | k-quantization                | Superblocks has  1 blocks (256 weights per block) (Only used for intermediate quants) | w = q * block_scale (8-bit)      | [llama.cpp PR: k-quants #1684](https://github.com/ggerganov/llama.cpp/pull/1684) |
| IQ1_S    | GGML_FTYPE_MOSTLY_IQ1_S         | GGML_TYPE_IQ1_S         | 1.5         | i-quantization                | Superblocks has  8 blocks ( 32 weights per block)                      | w = func(superblock_scale, importance_matrix)   | [llama.cpp PR: 1.5 bit quantization #5453](https://github.com/ggerganov/llama.cpp/pull/5453) |
| IQ1_M    | GGML_FTYPE_MOSTLY_IQ1_M         | GGML_TYPE_IQ1_M         | 1.75        | i-quantization                | Superblocks has 16 blocks ( 16 weights per block)                      | w = func(superblock_scale, importance_matrix)   | [llama.cpp PR: IQ1_M: 1.75 bpw quantization #6302](https://github.com/ggerganov/llama.cpp/pull/6302) |
| IQ2_XXS  | GGML_FTYPE_MOSTLY_IQ2_XXS       | GGML_TYPE_IQ2_XXS       | 2.0625      | i-quantization                | Superblocks has  8 blocks ( 32 weights per block)                      | w = func(superblock_scale, importance_matrix)   | [llama.cpp PR: SOTA 2-bit quants #4773](https://github.com/ggerganov/llama.cpp/pull/4773) |
| IQ2_XS   | GGML_FTYPE_MOSTLY_IQ2_XS        | GGML_TYPE_IQ2_XS        | 2.31        | i-quantization                | Superblocks has 16 blocks ( 16 weights per block)                      | w = func(superblock_scale, importance_matrix)   | [llama.cpp PR: SOTA 2-bit quants - part 2 #4856](https://github.com/ggerganov/llama.cpp/pull/4856) |
| IQ2_S    | GGML_FTYPE_MOSTLY_IQ2_S         | GGML_TYPE_IQ2_S         | 2.5         | i-quantization                | ?                                                                      | w = func(superblock_scale, importance_matrix)   | [llama.cpp PR: Adding IQ2_S and IQ2_M to complete coverage of the 2-3 bit quantization range #5721](https://github.com/ggerganov/llama.cpp/pull/5721) |
| IQ3_S    | GGML_FTYPE_MOSTLY_IQ3_S         | GGML_TYPE_IQ3_S         | 3.4375      | i-quantization                | ?                                                                      | w = func(superblock_scale, importance_matrix)   | [llama.cpp PR: IQ3_S: a much better alternative to Q3_K #5676](https://github.com/ggerganov/llama.cpp/pull/5676) |
| IQ3_XXS  | GGML_FTYPE_MOSTLY_IQ3_XXS       | GGML_TYPE_IQ3_XXS       | 3.0625      | i-quantization                | Superblocks has  8 blocks ( 32 weights per block)                      | w = func(superblock_scale, importance_matrix)   | [llama.cpp PR: SOTA 3-bit quants #5196](https://github.com/ggerganov/llama.cpp/pull/5196) |
| IQ4_NL   | GGML_FTYPE_MOSTLY_IQ4_NL        | GGML_TYPE_IQ4_NL        | 4.5         | i-quantization                | Superblocks has 16 blocks ( 16 weights per block)                      | w = [non linear mapping of quants to weights]   | [llama.cpp PR: IQ4_NL: 4-bit non-linear quants with blocks of 32 #5590](https://github.com/ggerganov/llama.cpp/pull/5590) |
| IQ4_XS   | GGML_FTYPE_MOSTLY_IQ4_XS        | GGML_TYPE_IQ4_XS        | 4.25        | i-quantization                | Superblocks has  8 blocks ( 32 weights per block)                      | w = func(superblock_scale, importance_matrix)   | [llama.cpp PR: IQ4_XS: a 4.25 bpw quantization #5747](https://github.com/ggerganov/llama.cpp/pull/5747) |

* All superblocks have fp16 scaling factor and contains up to 256 weights. Number of weights in a block must be divisible by 256. (To be confirmed)

## Where to find the structure of these tensors in the code?

You would find it all usually in `ggml-common.h` where it typically be of this form

### Blocks

```c
#define QK4_0 32
typedef struct {
    ggml_half d;           // delta
    uint8_t qs[QK4_0 / 2]; // nibbles / quants
} block_q4_0;
static_assert(sizeof(block_q4_0) == sizeof(ggml_half) + QK4_0 / 2, "wrong q4_0 block size/padding");
```

### Superblocks

```c
//
// Super-block quantization structures
//

// 2-bit quantization
// weight is represented as x = a * q + b
// 16 blocks of 16 elements each
// Effectively 2.625 bits per weight
typedef struct {
    uint8_t scales[QK_K/16]; // scales and mins, quantized with 4 bits
    uint8_t qs[QK_K/4];      // quants
    union {
        struct {
            ggml_half d;    // super-block scale for quantized scales
            ggml_half dmin; // super-block scale for quantized mins
        } GGML_COMMON_AGGR;
        ggml_half2 dm;
    };
} block_q2_K;
static_assert(sizeof(block_q2_K) == 2*sizeof(ggml_half) + QK_K/16 + QK_K/4, "wrong q2_K block size/padding");
```