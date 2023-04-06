## Measuring the performance of the inference

- Build with `GGML_PERF`
- Uncomment `ggml_graph_print(&gf);` in `llama.cpp`

You will see output like this:

```java
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