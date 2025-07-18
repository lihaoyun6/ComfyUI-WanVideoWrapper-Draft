# ComfyUI-WanVideoWrapper
## 此项目仅支持NVIDIA RTX30/40及A100/H100架构的GPU!

本项目是修改版的`ComfyUI-WanVideoWrapper`.  
我添加了 **[draft-attention](https://github.com/shawnricecake/draft-attention)** 注意力方案和 **[Euler/d](https://github.com/eddyhhlure1Eddy/Euler-d)** 调度器支持以进一步提升推理速度.  
此注意力方案显著的提高了 NVIDIA Ampere / Ada / Hopper GPU 上的视频生成速度,  
在 Ampere GPU 上使用 draft-attn 进行视频推理, 相比于 sage-attn 平均提速可达 25~40%  
**[[WanVideoWrapper readme](./original_readme.md)]**  

## 效果演示

| 输入图像 | Sage-Attention | Draft-Attention (75%稀疏率) | Draft-Attention (90%稀疏率) |
| :----:  | :----: | :----: | :----: |
| <img width=500 src="https://github.com/user-attachments/assets/5c8699d4-c08d-4976-a7a4-7e35a2be4068"> | <video src="https://github.com/user-attachments/assets/d86e6008-37cb-4ae2-ab33-d5b28cc84802"> | <video src="https://github.com/user-attachments/assets/e34c3f54-c2c7-4ec1-bf6f-c91bbfca619a"> | <video src="https://github.com/user-attachments/assets/5ce30f31-1a93-486b-89e1-6f20ae2307b9"> |
| 采样耗时: | 70.38s | 48.31s | 40.91s |  

**提示词:** *"女孩举起手放在额头附近遮挡太阳, 脸上带着迷人的微笑, 镜头逐渐拉远"*  


## 使用方法
安装后端支持库及 ComfyUI 插件本体后, 使用方法与原版 WanVideoWrapper 几乎无异,  
但与其他注意力方案不同, draft-attention 目前暂未支持任意分辨率视频生成.  
所以在搭建工作流时请务必使用 **`Find Draft-Attention Bucket`** 节点来将你输入的分辨率转换为 draft-attention 能接受的格式.  

![image](https://github.com/user-attachments/assets/e1b1245c-e2b5-4d74-be07-abcddaf5b95c)

## 安装

***!如果你安装了原版的 Comfyui-WanVideoWrapper 请先卸载后再安装本插件!***  

### 预构建安装:

1. 克隆此项目并安装依赖项:  

	```bash
	cd [ComfyUI_Path]\custom_nodes
	git clone https://github.com/lihaoyun6/ComfyUI-WanVideoWrapper
	pip install -r ComfyUI-WanVideoWrapper\requirements.txt
	```

2. 安装 flash-attention 和 Block-Sparse-Attention:  
	> 此处仅提供 `Windows_x64` 平台预构建包, 建议更新显卡驱动并安装 `torch2.7.0+cu128`, 然后根据 Python 版本安装对应的后端库:

	```bash
	# Python 3.10
	pip install https://github.com/lihaoyun6/Block-Sparse-Attention/releases/download/v0.0.1/flash_attn-2.8.1+cu128torch2.7cxx11abiFALSE-cp310-cp310-win_amd64.whl
	pip install https://github.com/lihaoyun6/Block-Sparse-Attention/releases/download/v0.0.1/block_sparse_attn-0.0.1+cu128torch2.7cxx11abiFALSE-cp310-cp310-win_amd64.whl
	```
	
	```bash
	# Python 3.11
	pip install https://github.com/lihaoyun6/Block-Sparse-Attention/releases/download/v0.0.1/flash_attn-2.8.1+cu128torch2.7cxx11abiFALSE-cp311-cp311-win_amd64.whl
	pip install https://github.com/lihaoyun6/Block-Sparse-Attention/releases/download/v0.0.1/block_sparse_attn-0.0.1+cu128torch2.7cxx11abiFALSE-cp311-cp311-win_amd64.whl
	```

	```bash
	# Python 3.12
	pip install https://github.com/lihaoyun6/Block-Sparse-Attention/releases/download/v0.0.1/flash_attn-2.8.1+cu128torch2.7cxx11abiFALSE-cp312-cp312-win_amd64.whl
	pip install https://github.com/lihaoyun6/Block-Sparse-Attention/releases/download/v0.0.1/block_sparse_attn-0.0.1+cu128torch2.7cxx11abiFALSE-cp312-cp312-win_amd64.whl
	```

	```bash
	# Python 3.13
	pip install https://github.com/lihaoyun6/Block-Sparse-Attention/releases/download/v0.0.1/flash_attn-2.8.1+cu128torch2.7cxx11abiFALSE-cp313-cp313-win_amd64.whl
	pip install https://github.com/lihaoyun6/Block-Sparse-Attention/releases/download/v0.0.1/block_sparse_attn-0.0.1+cu128torch2.7cxx11abiFALSE-cp313-cp313-win_amd64.whl
	```


---

### 编译安装:
1. 克隆此项目并安装依赖项:  

	```bash
	cd [ComfyUI_Path]\custom_nodes
	git clone https://github.com/lihaoyun6/ComfyUI-WanVideoWrapper
	pip install -r ComfyUI-WanVideoWrapper\requirements.txt
	```

2. 编译并安装 Block-Sparse-Attention:
	> 在编译前请确保已经安装了 CUDA >= 11.6 以及 C++ 编译器和 PyTorch  

	```bash
	pip install ninja wheel packaging
	#每个 JOB 大约会占用 8G 内存, 请根据系统内存容量设置. 例如物理内存 64G 内存建议设为不高于 8
	export MAX_JOBS=4
	#下载源代码并开始编译安装 (此过程根据你的 CPU 和内存容量, 可能会持续 1~3 个小时或更久, 请耐心等待)
	git clone https://github.com/lihaoyun6/Block-Sparse-Attention
	cd Block-Sparse-Attention
	pip install --no-build-isolation ./
	```
3. 安装 flash-attention:  

	```bash
	pip install flash-attention --no-build-isolation
	```

## 鸣谢
[ComfyUI-WanVideoWrapper](https://github.com/kijai/ComfyUI-WanVideoWrapper) - kijai  
[draft-attention](https://github.com/shawnricecake/draft-attention) - shawnricecake  
[Block-Sparse-Attention](https://github.com/mit-han-lab/Block-Sparse-Attention) - mit-han-lab  
[Euler/d](https://github.com/eddyhhlure1Eddy/Euler-d) - eddyhhlure1Eddy  
