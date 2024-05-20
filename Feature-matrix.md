|                      | **CPU (AVX2)** | **CPU (ARM NEON)** | **Metal** | **cuBLAS** |    **rocBLAS**   | **SYCL** | **CLBlast** | **Vulkan** | **Kompute** |
|:--------------------:|:--------------:|:------------------:|:---------:|:----------:|:----------------:|:--------:|:-----------:|:----------:|:-----------:|
| **K-quants**         | ✅              | ✅                  | ✅         | ✅          | ✅                | ✅        | ✅ 🐢⁵          | ✅ 🐢⁵         | 🚫           |
| **I-quants**         | ✅ 🐢⁴       | ✅ 🐢⁴           | ✅ 🐢⁴ | ✅          | ✅                | Partial¹        | 🚫           | 🚫          | 🚫           |
| **Multi-GPU**        | N/A            | N/A                | N/A       | ✅          | ❓                | ✅⁷        | ❓           | ✅          | ❓           |
|  **K cache quants**  | ✅              | ❓                  | ✅         | ✅ 🐢³          | Partial⁶ 🐢³ | ❓        | ✅           | 🚫          | 🚫           |
| **MoE architecture** | ✅              | ❓                  | ✅         | ✅          | ✅                | ❓        | Partial² | 🚫          | 🚫           |

* ✅: feature works
* 🚫: feature does not work
* ❓: unknown, please contribute if you can test it youself
* 🐢: feature is slow
* ¹: SYCL: OK: README-sycl.md: ``2024.4: IQ4_NL, IQ4_XS, IQ3_XXS, IQ3_S, IQ2_XXS, IQ2_XS, IQ2_S, IQ1_S, IQ1_M''
* ¹: SYCL: OK?: iq4_XXS maybe ok (per. #5674 anecdote comment)
* ¹: SYCL: ERROR: iq1_s reportedly remains broken per. #6014
* ¹: SYCL: OK?: To be confirmed: Q_K_ quantizations, F16, F32 are all ok?
* ²: Only with `-ngl 0`
* ³: Inference is 50% slower
* ⁴: Slower than K-quants of comparable size
* ⁵: Slower than cuBLAS/rocBLAS on similar cards
* ⁶: Only q8_0 and iq4_nl
* ⁷: SYCL: PARTIAL OK: README-sycl.md: ``2024.3 Support multiple cards: --split-mode: [none|layer]; not support [row], it's on developing.''
