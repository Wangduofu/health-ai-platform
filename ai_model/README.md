# 智医通 AI 模型

## 目录结构

```
/ai_model
├── data/                  # 训练数据目录
├── models/                # 模型文件目录
├── training/              # 训练脚本
│   ├── finetune.py        # 模型微调主脚本
│   ├── data_processor.py  # 数据处理模块
│   ├── model_optimizer.py # 模型优化模块
│   ├── run_training.py    # 训练流程主脚本
│   └── config.json        # 训练配置文件
├── inference/             # 推理脚本
│   ├── symptom_recognition.py # 症状识别模块
│   ├── risk_assessment.py     # 风险评估模块
│   └── main.py               # 推理测试主程序
└── requirements.txt       # 依赖项
```

## 模型说明

智医通AI模型基于Llama-3-8B进行轻量化微调，专注于医疗领域的自然语言理解和生成。模型主要实现以下功能：

1. **症状识别**：从用户描述中提取关键症状信息
2. **风险评估**：基于症状组合评估潜在疾病风险
3. **问诊路径生成**：根据初始症状动态生成后续问诊路径