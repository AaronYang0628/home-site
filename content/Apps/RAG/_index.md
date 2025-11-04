+++
title = "Milvus RAG"
description = "基于 Milvus 向量数据库与阿里云百炼 Embedding/LLM 的 RAG Demo"
+++


本 Demo 展示了一个轻量化的基于 Milvus 向量数据库的检索增强生成（RAG）系统。系统设计用于将外部知识库、图片与文档与大模型能力结合，提供高质量、可更新的问答与相似检索功能。

在线体验: [http://rag.demo.72602.online](http://rag.demo.72602.online)

### 核心功能

1. 外挂知识库搜索：支持将外部知识库（例如静态文档集合、数据库或其他数据源）接入 RAG，用户可以直接用自然语言进行检索，系统会返回检索结果并结合 LLM 生成答案。
2. 图片相似检索：用户可以上传一张图片，系统会对该图片进行向量化，并在 Milvus 中执行相似度搜索，返回 Top-10 最相似的图片结果。
3. 文档上传与增量学习：用户可以上传文档（PDF、TXT、Markdown 等），系统会对文档进行解析、分块、向量化并将向量写入 Milvus。文档被索引后，后续用户提问时，新的知识会被检索到并用于生成更准确的回答。

### 技术架构

- 前端：用户界面（上传图片/文档，发起查询，查看结果）
- 后端服务：接收请求，负责文档解析、图片预处理、调用百炼的 embedding/LLM 接口以及对 Milvus 的读写
- Milvus：向量数据库，负责存储向量索引并提供近似最近邻搜索（ANN）
- 阿里云百炼平台：提供 embedding（将文本/图片/区块转换为向量）与 LLM（基于检索结果生成答案）能力

数据流程示例：

1. 图片相似检索：
	 - 用户上传图片 -> 后端调用百炼图片/多模态 embedding 接口 -> 得到向量 -> 在 Milvus 中搜索 Top-N -> 返回相似图片并展示给用户
2. 文档上传与索引：
	 - 用户上传文档 -> 后端把文档分块（chunking）并清洗 -> 调用百炼文本 embedding -> 向 Milvus upsert 向量与元数据（来源、段落 id 等）
	 - 后续用户提问时，先在 Milvus 检索相关段落，再把检索到的上下文连同用户问题发送给百炼 LLM 以生成答案

### 部署与配置要点

前置条件：
- Kubernetes 集群
- Milvus 服务 , https://in03-891eca6c21bd4e5.serverless.aws-eu-central-1.cloud.zilliz.com
- 阿里云百炼账号与 API 权限
- [[可选]]()：ArgoCD 用于部署管理

部署示例（使用 ArgoCD）：

```shell
kubectl -n argocd apply -f /root/home-site/content/Apps/RAG/rag.app.values.yaml
argocd app sync milvus-rag-app
```


### 可配置/优化点

- 向量检索参数：选择合适的索引类型（IVF、HNSW 等）与搜索参数以在召回与延迟之间取得平衡
- 文档分块策略：根据文档类型调整 chunk size 与 overlap，以帮助检索上下文完整性
- 缓存策略：对热门检索结果或生成结果做缓存以降低调用成本
- 数据权限与隐私：对用户上传的文档与图片做好访问控制与加密（在 K8s Secret 和存储层面）

### 典型应用场景

- 企业知识库问答
- 智能客服与工单助手
- 文档检索与摘要
- 图像检索与相似推荐

### 安全与成本考虑

- API 调用费用：使用百炼的 embedding/LLM 会产生调用成本，建议设置配额与限流
- 数据合规：敏感数据上传前应脱敏或加密
- 访问控制：对索引数据及检索结果应用 RBAC 与审计

