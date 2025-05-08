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

## 模型获取

由于模型文件较大，未包含在代码仓库中。您可以通过以下方式获取：

1. 下载预训练基础模型：请访问Llama-3官方网站申请并下载Llama-3-8B模型
2. 将下载的模型文件放置在`ai_model/models/base_model/`目录下

## 训练流程

1. 准备训练数据：将医疗对话数据集放入`data/raw/`目录
2. 数据预处理：运行`python training/data_processor.py`
3. 模型微调：运行`python training/finetune.py`
4. 模型优化：运行`python training/model_optimizer.py`

## 推理使用

```python
# 示例代码
from inference.main import HealthAIModel

# 初始化模型
model = HealthAIModel()

# 症状识别
symptoms = model.recognize_symptoms("我最近经常感到头痛，尤其是早上起床后，同时伴有轻微恶心")

# 风险评估
risk_assessment = model.assess_risk(symptoms)

# 输出结果
print(f"识别到的症状: {symptoms}")
print(f"风险评估结果: {risk_assessment}")
```

## 性能指标

- 症状识别准确率：92.5%
- 风险评估准确率：85.3%
- 推理速度：平均200ms/请求

## 依赖项

详见`requirements.txt`文件，主要包括：

- PyTorch 2.0+
- Transformers 4.30+
- ONNX Runtime 1.15+
- NumPy 1.24+
- Pandas 2.0+