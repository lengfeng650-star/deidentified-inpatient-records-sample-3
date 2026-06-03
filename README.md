---
license: Apache License 2.0
tags:
- 电子病历
- 住院病例
- 结构化文本
- 脱敏数据
- 医疗NLP
- 信息抽取
- clinical-records
- de-identified
configs:
- config_name: default
  data_files:
  - split: train
    path: data/cases.jsonl
---

# 脱敏住院病例结构化样本3例

本数据集包含 3 例医院真实住院病例经脱敏加工后的结构化文本样本。数据以 JSONL 形式发布，每行对应一例病例，包含病例编号、性别、年龄分组、科室、诊断摘要和结构化病历段落。

本数据集面向医疗文档解析、电子病历结构化、临床信息抽取、病历质控、医学文本检索和医疗大模型评测等场景。公开版本只保留研究和工程评测所需的结构化文本信息，不包含原始 PDF、影像、检验报告图片或可直接识别个人身份的信息。

如需了解更多电子病历结构化、病案首页解析、临床 NLP、医学知识库建设、病历质控模型或批量授权合作，可联系 18996195218，或发送邮件至 zhouhaoran@shujuyoupu.com。

## 数据集简介

- 数据类型：脱敏住院病例结构化文本。
- 病例数量：3 例。
- 文件格式：JSONL。
- 数据粒度：一行一例病例。
- 主要字段：`case_id`、`record_type`、`deidentification_status`、`sex`、`age_group`、`department`、`diagnosis_summary`、`record_sections`。
- 科室覆盖：妇产科、心外科、心内科。
- 年龄表达：使用年龄分组，不保留精确年龄。
- 脱敏说明：来自医院真实病例数据，经脱敏加工后发布。
- 适用任务：电子病历结构化、病历字段抽取、诊断摘要识别、病程记录解析、医疗文档问答、医学文本检索增强、医疗大模型评测。

## 数据结构

```text
open_data/modelscope/deidentified-inpatient-records-sample-3/
  README.md
  data/
    cases.jsonl
  metadata/
    case_manifest.jsonl
    files_manifest.jsonl
    dataset_summary.json
```

## 样本组成

| case_id | 类型 | 性别 | 年龄分组 | 科室 | 诊断摘要数 | 结构化段落数 |
|---|---|---|---|---|---:|---:|
| case-001 | 住院病例结构化文本 | 女 | 未成年 | 妇产科 | 10 | 45 |
| case-002 | 住院病例结构化文本 | 男 | 45-64岁 | 心外科 | 0 | 16 |
| case-003 | 住院病例结构化文本 | 女 | 65-79岁 | 心内科 | 8 | 31 |

## 字段说明

`data/cases.jsonl` 每行是一例脱敏病例：

```json
{
  "case_id": "case-001",
  "record_type": "住院病例结构化文本",
  "deidentification_status": "医院真实病例数据经脱敏加工",
  "sex": "女",
  "age_group": "未成年",
  "department": "妇产科",
  "diagnosis_summary": ["..."],
  "record_sections": {
    "入院记录-主诉": "...",
    "入院记录-现病史": "...",
    "病程记录": "..."
  }
}
```

`metadata/case_manifest.jsonl` 是病例级元数据清单，包含 `case_id`、`record_type`、`deidentification_status`、`sex`、`age_group`、`department`、`diagnosis_count`、`section_count`、`json_chars`、`sha256`。

`metadata/files_manifest.jsonl` 是文件清单，包含每个公开文件的路径、类型、说明、记录数和文件大小。

`metadata/dataset_summary.json` 是数据集摘要，包含病例数量、数据格式、样本组成、脱敏说明、科室分布、性别分布和年龄分组分布。

## 脱敏处理

公开数据已做二次脱敏和风险检查：

- 不保留姓名、身份证号、手机号、住址、住院号、门诊号、病案号、床号等直接身份标识。
- 精确日期、时间、长数字编号和医疗机构名称已做占位或泛化处理。
- 年龄使用分组表达，不保留精确年龄。
- 不发布原始 PDF、影像、报告图片或内部路径。
- 发布前对手机号、身份证号、精确日期、时间、长编号、内部路径和源目录关键词进行了扫描。

脱敏不等于零风险。使用者在下载、研究、训练或再发布时，应遵守医疗数据合规要求，不得尝试还原个人身份或与外部数据进行再识别匹配。

## 加载方式

通过 ModelScope SDK 加载：

```python
from modelscope import MsDataset

ds = MsDataset.load(
    "deidentified-inpatient-records-sample-3",
    namespace="SHPDataGR",
    subset_name="default",
    split="train",
    version="master",
)

print(ds[0]["case_id"])
print(ds[0]["department"])
print(ds[0]["record_sections"].keys())
```

也可以直接读取 JSONL：

```python
import json

with open("data/cases.jsonl", "r", encoding="utf-8") as f:
    for line in f:
        case = json.loads(line)
        print(case["case_id"], case["department"])
```

## 数据质量和限制

- 本数据集是 3 例结构化样本，不代表完整医院病例库。
- 原始样例存在非标准 JSON 写法，公开版本已修复为严格 JSONL。
- 不同病例的结构化段落数量和字段命名可能不同，适合测试模型对真实病历结构差异的适应能力。
- 部分日期、时间、机构和编号已被占位，不能用于时间序列精确还原。
- 本数据集仅用于医疗文本处理、信息抽取、模型训练与评测等合规用途，不构成诊疗建议。

## 合作与联系

本数据集为脱敏电子病历结构化样本。若需要更多住院病例、门急诊病历、病案首页、医嘱、检查检验、病历质控、临床 NLP 标注或私有化模型训练数据合作，请联系 18996195218，或发送邮件至 zhouhaoran@shujuyoupu.com。

## 引用方式

暂无固定论文引用格式。使用时建议注明数据集名称、版本、访问平台和脱敏状态。
