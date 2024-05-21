|                      | **CPU (AVX2)** | **CPU (ARM NEON)** | **Metal** | **cuBLAS** |    **rocBLAS**   | **SYCL** | **CLBlast** | **Vulkan** | **Kompute** |
|:--------------------:|:--------------:|:------------------:|:---------:|:----------:|:----------------:|:--------:|:-----------:|:----------:|:-----------:|
| **K-quants**         | ✅              | ✅                  | ✅         | ✅          | ✅                | ✅        | ✅ 🐢⁵          | ✅ 🐢⁵         | 🚫           |
| **I-quants**         | ✅ 🐢⁴       | ✅ 🐢⁴           | ✅ 🐢⁴ | ✅          | ✅                | ✅¹        | 🚫           | 🚫          | 🚫           |
| **Multi-GPU**        | N/A            | N/A                | N/A       | ✅          | ❓                | ✅⁷        | ❓           | ✅          | ❓           |
|  **K cache quants**  | ✅              | ❓                  | ✅         | ✅ 🐢³          | Partial⁶ 🐢³ | ✅        | ✅           | 🚫          | 🚫           |
| **MoE architecture** | ✅              | ❓                  | ✅         | ✅          | ✅                | ⚠️⁸       | Partial² | 🚫          | 🚫           |

* ✅: feature works
* 🚫: feature does not work
* ⚠️: feature runs but is presently impacted by a known bug.
* ❓: unknown, please contribute if you can test it youself
* 🐢: feature is slow
* 1: SYCL: ✅: IQ4_XXS.
* 1: SYCL: 🚫⚠️: IQ1_S format use executes but presently invalid data results (q.v. #6014).
* 1: SYCL: README-sycl.md: ``✅ 2024.4: IQ4_NL, IQ4_XS, IQ3_XXS, IQ3_S, IQ2_XXS, IQ2_XS, IQ2_S, IQ1_M''; except IQ1_S 🚫 [as above]''
* 2: Only with `-ngl 0`
* 3: Inference is 50% slower
* 4: Slower than K-quants of comparable size
* 5: Slower than cuBLAS/rocBLAS on similar cards
* 6: Only Q8_0 and IQ4_NL
* 7: SYCL: Multi-GPU: README-sycl.md: ✅ ``2024.3 Support multiple cards: --split-mode: [none|layer]'';
* 7: SYCL: Multi-GPU: [row]🐢✅ support was said to function, but is slower (2024-05-21).
* 8: SYCL: 🚫⚠️: MoE architecture reportedly executes, but has a blocking bug being pursued (as of 2024-05-21).
