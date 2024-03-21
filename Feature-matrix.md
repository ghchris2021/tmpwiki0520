|                      | **CPU (AVX2)** | **CPU (ARM NEON)** | **Metal** | **cuBLAS** |    **rocBLAS**   | **SYCL** | **CLBlast** | **Vulkan** | **Kompute** |
|:--------------------:|:--------------:|:------------------:|:---------:|:----------:|:----------------:|:--------:|:-----------:|:----------:|:-----------:|
| **K-quants**         | ✅              | ✅                  | ✅         | ✅          | ✅                | ✅        | ✅ 🐢⁵          | ✅ 🐢⁵         | 🚫           |
| **I-quants**         | ✅ 🐢⁴       | ✅ 🐢⁴           | ✅ 🐢⁴ | ✅          | ✅                | Partial¹        | 🚫           | 🚫          | 🚫           |
| **Multi-GPU**        | N/A            | N/A                | N/A       | ✅          | ❓                | 🚫        | ❓           | ✅          | ❓           |
|  **K cache quants**  | ✅              | ❓                  | ✅         | ✅ 🐢³          | Partial⁶ 🐢³ | ❓        | ✅           | 🚫          | 🚫           |
| **MoE architecture** | ✅              | ❓                  | ✅         | ✅          | ✅                | ❓        | Partial² | 🚫          | 🚫           |

* ✅: feature works
* 🚫: feature does not work
* ❓: unknown, please contribute if you can test it youself
* 🐢: feature is slow
* ¹: IQ3_S and IQ1_S, see #5886
* ²: Only with `-ngl 0`
* ³: Inference is 50% slower
* ⁴: Slower than K-quants of comparable size
* ⁵: Slower than cuBLAS/rocBLAS on similar cards
* ⁶: Only q8_0 and iq4_nl