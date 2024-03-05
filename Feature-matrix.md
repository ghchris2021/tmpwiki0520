# llama.cpp feature matrix

|                      | **CPU (AVX2)** | **CPU (ARM NEON)** | **Metal** | **cuBLAS** |    **rocBLAS**   | **SYCL** | **CLBlast** | **Vulkan** | **Kompute** |
|:--------------------:|:--------------:|:------------------:|:---------:|:----------:|:----------------:|:--------:|:-----------:|:----------:|:-----------:|
| **K-quants**         | ✅              | ✅                  | ✅         | ✅          | ✅                | ✅        | ✅           | ✅          | 🚫           |
| **I-quants**         | ✅ (SLOW)       | ✅ (SLOW)           | ✅ (SLOW)  | ✅          | ✅                | Partial¹        | 🚫           | 🚫          | 🚫           |
| **Multi-GPU**        | N/A            | N/A                | N/A       | ✅          | ❓                | 🚫        | ❓           | ✅          | ❓           |
|  **K cache quants**  | ✅              | ❓                  | ❓         | ✅          | Only q8_0 (SLOW) | ❓        | ✅           | 🚫          | 🚫           |
| **MoE architecture** | ✅              | ❓                  | ✅         | ✅          | ✅                | ❓        | Only -ngl 0 | 🚫          | 🚫           |

* ¹: IQ3_S and IQ1_S, see #5886