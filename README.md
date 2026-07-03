# zmd_lora_anima

# 终末地画风 LoRA（基于 anima-preview3-base）

本 LoRA 在 `anima-preview3-base` 模型上微调，用于生成《终末地》风格的角色图像。**训练工具为 Kohya**。  
由于数据集全部为角色图，该 LoRA 仅适合角色生成，室内外场景的画风表现不稳定，请谨慎用于场景图。

---

## 训练参数

- 基础模型：`anima-preview3-base`  
- 文本编码器：`qwen_3_06b_base`  
- VAE：`qwen_image_vae`  
- 数据集：68 张角色图像  
- 训练分辨率：896 × 896  
- 总步数：1675（计算：67张 × 10次重复 × 10个epoch ÷ batch size 4）  
- Epochs：10  
- Batch size：4  
- 学习率：5.00e-05  
- 优化器：AdamW8bit  
- 精度：BF16  
- LoRA 秩（Rank）：16  
- 硬件：NVIDIA GeForce RTX 5060（8GB VRAM，训练时功耗约24W / 145W，温度约51°C）

---

## 使用方式

- **触发词**：在提示词中加入 `zmd style` 即可激活本 LoRA 的风格。  
- **推理工具**：支持 LoRA 加载的任意界面（如 ComfyUI、Automatic1111 等均可）。  

---

## 注意事项

- 本 LoRA **不适用于** 室内外场景生成，场景画风容易混乱。  
- 仅建议用于**角色**生成任务。

<img width="1248" height="665" alt="image" src="https://github.com/user-attachments/assets/d0a394d9-c08b-45f2-9bb5-b7876792c22e" />
```md
## LoRA 训练过程简要分析

### 1. Loss 收敛情况
- `loss/average` 整体呈稳定下降趋势，从约 **0.07 → 0.0476**，说明模型持续学习并逐步收敛。
- 下降过程平滑，未出现明显震荡或发散，训练稳定性较好。
- 后期下降幅度变小，进入**收敛平台期**。

### 2. 当前 Loss 波动
- `loss/current` 波动较大（约 0 ~ 0.4+），属于正常 mini-batch 噪声范围。
- 未出现持续异常升高，说明训练未发生崩坏或过拟合初期迹象。

### 3. Epoch 级别趋势
- `loss/epoch_average` 持续下降（约 **0.060 → 0.046**）。
- 中间有轻微反弹，但整体趋势向下，说明泛化学习稳定。

### 4. 学习率变化
- `lr/unet` 使用平滑衰减策略，从 **5e-5 → 接近 0**。
- 与 loss 收敛趋势匹配，属于典型 cosine/linear decay 收敛过程。

### 5. 结论
- 训练过程稳定，无明显过拟合或崩溃迹象。


