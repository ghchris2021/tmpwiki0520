|                      | **CPU (AVX2)** | **CPU (ARM NEON)** | **Metal** | **cuBLAS** |    **rocBLAS**   | **SYCL** | **CLBlast** | **Vulkan** | **Kompute** |
|:--------------------:|:--------------:|:------------------:|:---------:|:----------:|:----------------:|:--------:|:-----------:|:----------:|:-----------:|
| **K-quants**         | ✅              | ✅                  | ✅         | ✅          | ✅                | ✅        | ✅           | ✅          | 🚫           |
| **I-quants**         | ✅ (SLOW)       | ✅ (SLOW)           | ✅ (SLOW)  | ✅          | ✅                | Partial¹        | 🚫           | 🚫          | 🚫           |
| **Multi-GPU**        | N/A            | N/A                | N/A       | ✅          | ❓                | 🚫        | ❓           | ✅          | ❓           |
|  **K cache quants**  | ✅              | ❓                  | ❓         | ✅          | Partial³ (SLOW) | ❓        | ✅           | 🚫          | 🚫           |
| **MoE architecture** | ✅              | ❓                  | ✅         | ✅          | ✅                | ❓        | Partial² | 🚫          | 🚫           |

* ¹: IQ3_S and IQ1_S, see #5886
* ²: Only with `-ngl 0`
* ³: Only `-ctk q8_0`