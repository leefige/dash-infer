## llama.cpp version

```
commit e190f1fca6f60d80944f9e8709d343a025c4d245 (HEAD, tag: b2534, master)
Author: Joseph Stahl <1269177+josephst@users.noreply.github.com>
Date:   Mon Mar 25 20:51:46 2024 -0400

    nix: make `xcrun` visible in Nix sandbox for precompiling Metal shaders (#6118)
    
    * Symlink to /usr/bin/xcrun so that `xcrun` binary
    is usable during build (used for compiling Metal shaders)
    
    Fixes https://github.com/ggerganov/llama.cpp/issues/6117
    
    * cmake - copy default.metallib to install directory
    
    When metal files are compiled to default.metallib, Cmake needs to add this to the install directory so that it's visible to llama-cpp
    
    Also, update package.nix to use absolute path for default.metallib (it's not finding the bundle)
    
    * add `precompileMetalShaders` flag (defaults to false) to disable precompilation of metal shader
    
    Precompilation requires Xcode to be installed and requires disable sandbox on nix-darwin
```

## x86 EMR

- Physical core per NUMA: 48
- 192 vCPU @ 3.2GHz, 16GBx64 DDR
- Aliyun instance: [ecs.g8i.48xlarge](https://www.alibabacloud.com/help/en/ecs/user-guide/overview-of-instance-families#g8i), [ecs.g8i.48xlarge 中文页面](https://help.aliyun.com/zh/ecs/user-guide/general-purpose-instance-families#g8i)

### Llama-2-7b-chat

#### llama.cpp

| Physical core * NUMA | Precision | Batch size | Input length | Output length | First-token latency (s) | Throughput per batch (Tokens/s) | Throughput (Tokens/s) |
| :------------------: | :-------: | :--------: | :----------: | :-----------: | :---------------------: | :-----------------------------: | :-------------------: |
| 48*1 | FP16 | 1 | 128 | 128 | 1.25  | 14.39  | 14.39 |
|  |  |  | 1200 |  | 12.63  | 13.47  | 13.47 |
| 48*1 | INT8 | 1 | 128 | 128 | 0.97  | 17.61  | 17.61 |
|  |  |  | 1200 |  | 9.82  | 15.63  | 15.63 |

#### DashInfer

| Physical core * NUMA | Precision | Batch size | Input length | Output length | First-token latency (s) | Throughput per batch (Tokens/s) | Throughput (Tokens/s) |
| :------------------: | :-------: | :--------: | :----------: | :-----------: | :---------------------: | :-----------------------------: | :-------------------: |
| 48*1 | BF16 | 1 | 128 | 128 | 0.24  | 13.21  | 13.21 |
|  |  | 2 | 128 |  | 0.38  | 13.95  | 27.91 |
|  |  | 4 | 128 |  | 0.68  | 14.00  | 56.00 |
|  |  | 8 | 128 |  | 1.36  | 11.39  | 91.12 |
|  |  | 1 | 1200 |  | 1.78  | 12.60  | 12.60 |
|  |  | 2 | 1200 |  | 2.72  | 12.35  | 24.69 |
|  |  | 4 | 1200 |  | 4.30  | 9.32  | 37.26 |
|  |  | 8 | 1200 |  | 7.70  | 6.28  | 50.22 |
| 48*2 | BF16 | 1 | 128 | 128 | 0.17  | 22.71  | 22.71 |
|  |  | 2 | 128 |  | 0.27  | 21.05  | 42.10 |
|  |  | 4 | 128 |  | 0.52  | 18.08  | 72.32 |
|  |  | 8 | 128 |  | 0.88  | 13.76  | 110.10 |
|  |  | 1 | 1200 |  | 1.45  | 20.93  | 20.93 |
|  |  | 2 | 1200 |  | 2.24  | 17.28  | 34.57 |
|  |  | 4 | 1200 |  | 3.92  | 11.06  | 44.26 |
|  |  | 8 | 1200 |  | 6.44  | 7.82  | 62.58 |

### Qwen1.5-4B-Chat

#### DashInfer

| Physical core * NUMA | Precision | Batch size | Input length | Output length | First-token latency (s) | Throughput per batch (Tokens/s) | Throughput (Tokens/s) |
| :------------------: | :-------: | :--------: | :----------: | :-----------: | :---------------------: | :-----------------------------: | :-------------------: |
| 48*1 | BF16 | 1 | 128 | 128 | 0.24  | 20.77  | 20.77 |
|  |  | 2 | 128 |  | 0.34  | 21.02  | 42.03 |
|  |  | 4 | 128 |  | 0.57  | 17.96  | 71.82 |
|  |  | 8 | 128 |  | 1.15  | 13.88  | 111.03 |
|  |  | 1 | 1200 |  | 1.09  | 18.66  | 18.66 |
|  |  | 2 | 1200 |  | 1.68  | 17.59  | 35.17 |
|  |  | 4 | 1200 |  | 2.71  | 12.38  | 49.50 |
|  |  | 8 | 1200 |  | 4.90  | 7.69  | 61.51 |
| 48*2 | BF16 | 1 | 128 | 128 | 0.34  | 33.03  | 33.03 |
|  |  | 2 | 128 |  | 0.50  | 29.67  | 59.34 |
|  |  | 4 | 128 |  | 0.86  | 19.71  | 78.84 |
|  |  | 8 | 128 |  | 1.62  | 12.86  | 102.90 |
|  |  | 1 | 1200 |  | 0.94  | 28.87  | 28.87 |
|  |  | 2 | 1200 |  | 1.40  | 19.97  | 39.94 |
|  |  | 4 | 1200 |  | 2.38  | 12.47  | 49.90 |
|  |  | 8 | 1200 |  | 4.40  | 7.68  | 61.44 |

## ARM Yitian710

- Physical core per NUMA: 64
- 128 vCPU @ 3.0GHz, 16GBx32 DDR
- Aliyun instance: [ecs.g8y.32xlarge](https://www.alibabacloud.com/help/en/ecs/user-guide/overview-of-instance-families#g8y), [ecs.g8y.32xlarge 中文页面](https://help.aliyun.com/zh/ecs/user-guide/general-purpose-instance-families#g8y)

### Llama-2-7b-chat

#### llama.cpp

| Physical core * NUMA | Precision | Batch size | Input length | Output length | First-token latency (s) | Throughput per batch (Tokens/s) | Throughput (Tokens/s) |
| :------------------: | :-------: | :--------: | :----------: | :-----------: | :---------------------: | :-----------------------------: | :-------------------: |
| 64*1 | FP16 | 1 | 128 | 128 | 4.68  | 5.91  | 5.91 |
|  |  |  | 1200 |  | 45.00  | 5.57  | 5.57 |
| 64*1 | INT8 | 1 | 128 | 128 | 1.40  | 8.37  | 8.37 |
|  |  |  | 1200 |  | 14.28  | 6.79  | 6.79 |

#### DashInfer

| Physical core * NUMA | Precision | Batch size | Input length | Output length | First-token latency (s) | Throughput per batch (Tokens/s) | Throughput (Tokens/s) |
| :------------------: | :-------: | :--------: | :----------: | :-----------: | :---------------------: | :-----------------------------: | :-------------------: |
| 64*1 | BF16 | 1 | 128 | 128 | 0.50  | 8.82  | 8.82 |
|  |  | 2 | 128 |  | 0.75  | 8.39  | 16.78 |
|  |  | 4 | 128 |  | 1.13  | 7.23  | 28.94 |
|  |  | 8 | 128 |  | 2.15  | 6.10  | 48.76 |
|  |  | 1 | 1200 |  | 3.36  | 8.12  | 8.12 |
|  |  | 2 | 1200 |  | 4.96  | 6.32  | 12.64 |
|  |  | 4 | 1200 |  | 8.47  | 4.63  | 18.51 |
|  |  | 8 | 1200 |  | 15.92  | 2.99  | 23.92 |
| 64*2 | BF16 | 1 | 128 | 128 | 0.46  | 13.79  | 13.79 |
|  |  | 2 | 128 |  | 0.79  | 13.22  | 26.44 |
|  |  | 4 | 128 |  | 1.20  | 10.98  | 43.94 |
|  |  | 8 | 128 |  | 2.22  | 8.33  | 66.67 |
|  |  | 1 | 1200 |  | 3.92  | 12.90  | 12.90 |
|  |  | 2 | 1200 |  | 4.27  | 9.96  | 19.92 |
|  |  | 4 | 1200 |  | 7.15  | 6.88  | 27.51 |
|  |  | 8 | 1200 |  | 12.89  | 4.24  | 33.91 |
| 64*1 | A16W8 | 1 | 128 | 128 | 0.48  | 15.89  | 15.89 |
|  |  | 2 | 128 |  | 0.71  | 14.33  | 28.65 |
|  |  | 4 | 128 |  | 1.19  | 11.56  | 46.25 |
|  |  | 8 | 128 |  | 2.05  | 8.45  | 67.57 |
|  |  | 1 | 1200 |  | 3.28  | 13.78  | 13.78 |
|  |  | 2 | 1200 |  | 5.19  | 10.07  | 20.13 |
|  |  | 4 | 1200 |  | 8.54  | 5.98  | 23.91 |
|  |  | 8 | 1200 |  | 15.60  | 3.43  | 27.40 |
| 64*2 | A16W8 | 1 | 128 | 128 | 0.50  | 25.19  | 25.19 |
|  |  | 2 | 128 |  | 0.66  | 20.48  | 40.95 |
|  |  | 4 | 128 |  | 1.15  | 17.70  | 70.80 |
|  |  | 8 | 128 |  | 1.88  | 11.26  | 90.11 |
|  |  | 1 | 1200 |  | 3.54  | 20.68  | 20.68 |
|  |  | 2 | 1200 |  | 4.58  | 13.88  | 27.76 |
|  |  | 4 | 1200 |  | 7.88  | 8.67  | 34.69 |
|  |  | 8 | 1200 |  | 13.58  | 4.65  | 37.19 |

### Qwen1.5-4B-Chat

#### DashInfer

| Physical core * NUMA | Precision | Batch size | Input length | Output length | First-token latency (s) | Throughput per batch (Tokens/s) | Throughput (Tokens/s) |
| :------------------: | :-------: | :--------: | :----------: | :-----------: | :---------------------: | :-----------------------------: | :-------------------: |
| 64*1 | BF16 | 1 | 128 | 128 | 0.36  | 15.59  | 15.59 |
|  |  | 2 | 128 |  | 0.54  | 14.26  | 28.52 |
|  |  | 4 | 128 |  | 0.80  | 12.64  | 50.55 |
|  |  | 8 | 128 |  | 1.34  | 9.12  | 72.93 |
|  |  | 1 | 1200 |  | 2.35  | 14.03  | 14.03 |
|  |  | 2 | 1200 |  | 3.32  | 10.79  | 21.58 |
|  |  | 4 | 1200 |  | 5.61  | 6.98  | 27.93 |
|  |  | 8 | 1200 |  | 10.04  | 4.25  | 34.00 |
| 64*2 | BF16 | 1 | 128 | 128 | 0.37  | 23.61  | 23.61 |
|  |  | 2 | 128 |  | 0.56  | 19.81  | 39.63 |
|  |  | 4 | 128 |  | 1.01  | 16.14  | 64.54 |
|  |  | 8 | 128 |  | 1.75  | 11.06  | 88.44 |
|  |  | 1 | 1200 |  | 2.44  | 20.17  | 20.17 |
|  |  | 2 | 1200 |  | 3.83  | 13.40  | 26.81 |
|  |  | 4 | 1200 |  | 5.72  | 9.16  | 36.66 |
|  |  | 8 | 1200 |  | 11.43  | 5.24  | 41.92 |
| 64*1 | A16W8 | 1 | 128 | 128 | 0.45  | 22.17  | 22.17 |
|  |  | 2 | 128 |  | 0.56  | 19.46  | 38.92 |
|  |  | 4 | 128 |  | 0.67  | 15.80  | 63.18 |
|  |  | 8 | 128 |  | 1.44  | 11.11  | 88.90 |
|  |  | 1 | 1200 |  | 2.39  | 18.16  | 18.16 |
|  |  | 2 | 1200 |  | 3.35  | 13.33  | 26.66 |
|  |  | 4 | 1200 |  | 5.66  | 7.69  | 30.75 |
|  |  | 8 | 1200 |  | 10.12  | 4.54  | 36.31 |
| 64*2 | A16W8 | 1 | 128 | 128 | 0.38  | 30.79  | 30.79 |
|  |  | 2 | 128 |  | 0.59  | 23.09  | 46.18 |
|  |  | 4 | 128 |  | 0.80  | 17.94  | 71.77 |
|  |  | 8 | 128 |  | 1.51  | 11.80  | 94.40 |
|  |  | 1 | 1200 |  | 2.81  | 24.86  | 24.86 |
|  |  | 2 | 1200 |  | 3.48  | 16.00  | 32.01 |
|  |  | 4 | 1200 |  | 5.74  | 10.11  | 40.44 |
|  |  | 8 | 1200 |  | 10.67  | 5.63  | 45.02 |
