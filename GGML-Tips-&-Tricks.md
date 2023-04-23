## Measuring the performance of the inference

- Build with `LLAMA_PERF`:
  ```bash
  make clean
  LLAMA_PERF=1 make
  ```

  This adds `-DGGML_PERF` to the compile flags which enables the internal `ggml` performance timers

You will see output like this:

```java
n_nodes = 1188
 -   0: [  4096,     1,     1]         GET_ROWS   (  1) cpu =   0.019 /   0.019 ms, wall =   0.006 /   0.006 ms
 -   1: [  4096,     1,     1]         RMS_NORM   (  1) cpu =   0.008 /   0.008 ms, wall =   0.008 /   0.008 ms
 -   2: [  4096,     1,     1]              MUL   (  1) cpu =   0.001 /   0.001 ms, wall =   0.001 /   0.001 ms
 -   3: [  4096,     1,     1]          MUL_MAT   (  1) cpu =   0.814 /   0.814 ms, wall =   0.817 /   0.817 ms
 -   4: [   128,    32,     1]          RESHAPE   (  1) cpu =   0.000 /   0.000 ms, wall =   0.001 /   0.001 ms
 -   5: [   128,    32,     1]             ROPE   (  1) cpu =   0.011 /   0.011 ms, wall =   0.011 /   0.011 ms
 -   6: [  4096,     1,     1]             VIEW   (  1) cpu =   0.000 /   0.000 ms, wall =   0.000 /   0.000 ms
 -   7: [  4096,     1,     1]              CPY   (  1) cpu =   0.004 /   0.004 ms, wall =   0.004 /   0.004 ms
 -   8: [  4096,     1,     1]          MUL_MAT   (  1) cpu =   3.273 /   3.273 ms, wall =   0.356 /   0.356 ms
 -   9: [  4096,     1,     1]          RESHAPE   (  1) cpu =   0.000 /   0.000 ms, wall =   0.001 /   0.001 ms
 -  10: [     1,  4096,     1]        TRANSPOSE   (  1) cpu =   0.000 /   0.000 ms, wall =   0.001 /   0.001 ms
 -  11: [     1,  4096,     1]             VIEW   (  1) cpu =   0.000 /   0.000 ms, wall =   0.001 /   0.001 ms
 -  12: [     1,  4096,     1]              CPY   (  1) cpu =   0.022 /   0.022 ms, wall =   0.023 /   0.023 ms
 -  13: [    17,   128,    32]             VIEW   (  1) cpu =   0.001 /   0.001 ms, wall =   0.001 /   0.001 ms
 -  14: [ 69632,     1,     1]             VIEW   (  1) cpu =   0.000 /   0.000 ms, wall =   0.000 /   0.000 ms
 -  15: [   128,    32,    17]          RESHAPE   (  1) cpu =   0.002 /   0.002 ms, wall =   0.000 /   0.000 ms
 -  16: [   128,    17,    32]          PERMUTE   (  1) cpu =   0.000 /   0.000 ms, wall =   0.000 /   0.000 ms
 -  17: [  4096,     1,     1]          MUL_MAT   (  1) cpu =   0.744 /   0.744 ms, wall =   0.246 /   0.246 ms
 -  18: [   128,    32,     1]          RESHAPE   (  1) cpu =   0.001 /   0.001 ms, wall =   0.000 /   0.000 ms
...
perf_total_per_op_us[            NONE] =   0.000 ms
perf_total_per_op_us[             DUP] =   0.000 ms
perf_total_per_op_us[             ADD] =   0.339 ms
perf_total_per_op_us[             SUB] =   0.000 ms
perf_total_per_op_us[             MUL] =   0.271 ms
perf_total_per_op_us[             DIV] =   0.000 ms
perf_total_per_op_us[             SQR] =   0.000 ms
perf_total_per_op_us[            SQRT] =   0.000 ms
perf_total_per_op_us[             SUM] =   0.000 ms
perf_total_per_op_us[            MEAN] =   0.000 ms
perf_total_per_op_us[          REPEAT] =   0.000 ms
perf_total_per_op_us[             ABS] =   0.000 ms
perf_total_per_op_us[             SGN] =   0.000 ms
perf_total_per_op_us[             NEG] =   0.000 ms
perf_total_per_op_us[            STEP] =   0.000 ms
perf_total_per_op_us[            RELU] =   0.000 ms
perf_total_per_op_us[            GELU] =   0.000 ms
perf_total_per_op_us[            SILU] =   0.574 ms
perf_total_per_op_us[            NORM] =   0.000 ms
perf_total_per_op_us[        RMS_NORM] =   0.721 ms
perf_total_per_op_us[         MUL_MAT] =  95.358 ms
perf_total_per_op_us[           SCALE] =   0.166 ms
perf_total_per_op_us[             CPY] =   2.062 ms
perf_total_per_op_us[         RESHAPE] =   0.067 ms
perf_total_per_op_us[            VIEW] =   0.067 ms
perf_total_per_op_us[         PERMUTE] =   0.067 ms
perf_total_per_op_us[       TRANSPOSE] =   0.020 ms
perf_total_per_op_us[        GET_ROWS] =   0.007 ms
perf_total_per_op_us[   DIAG_MASK_INF] =   0.025 ms
perf_total_per_op_us[        SOFT_MAX] =   0.185 ms
perf_total_per_op_us[            ROPE] =   2.667 ms
perf_total_per_op_us[      CONV_1D_1S] =   0.000 ms
perf_total_per_op_us[      CONV_1D_2S] =   0.000 ms
perf_total_per_op_us[      FLASH_ATTN] =   0.000 ms
perf_total_per_op_us[        FLASH_FF] =   0.000 ms
```