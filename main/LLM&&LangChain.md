# **[LangChain 官方文档（中文/英文）](https://docs.langchain.com)**

+ 关键模块要点：
 LLMChain：基础链
PromptTemplate：提示词模板
ConversationChain：对话链
RetrievalQA：文档问答入口
VectorStore：向量库（例如 FAISS）

+ [RAG 教程](https://python.langchain.com/v0.1/docs/use_cases/question_answering/)
 ## 🌟1. LLMChain：基础链

这是最基础的“链”，功能是：
👉 用提示词（Prompt） + LLM（大模型）来生成答案。

📦 构成：
PromptTemplate（提示词模板）
LLM（比如 OpenAI、通义千问、ChatGLM 等）
LLMChain = 把上面两个组合起来运行

📌 示例代码：
```
from langchain.llms import OpenAI
from langchain.prompts import PromptTemplate
from langchain.chains import LLMChain

# 定义提示词模板
template = "请用一句话介绍{topic}"
prompt = PromptTemplate.from_template(template)

# 使用 OpenAI 作为语言模型
llm = OpenAI(temperature=0.7)

# 构建 LLMChain
chain = LLMChain(prompt=prompt, llm=llm)

# 执行
result = chain.run("人工智能")
print(result)
```

## 🧩 2. PromptTemplate：提示词模板

👉 就是“给大模型说话用的提示词”，像你平时和我说：“请生成一段介绍xx的文字”。

📌 示例代码：
```
from langchain.prompts import PromptTemplate

template = "请解释一下什么是{concept}。"
prompt = PromptTemplate.from_template(template)

# 替换模板变量
print(prompt.format(concept="量子计算"))
```

## 🗣️ 3. ConversationChain：对话链

👉 让模型可以记住你之前说过什么，形成上下文连续的对话。

📌 示例代码：
```
from langchain.chains import ConversationChain
from langchain.memory import ConversationBufferMemory
from langchain.llms import OpenAI

llm = OpenAI()
memory = ConversationBufferMemory()

conversation = ConversationChain(llm=llm, memory=memory)

print(conversation.run("你好！"))
print(conversation.run("我想了解人工智能。"))
print(conversation.run("它会取代人类工作吗？"))
```

## 📄 4. RetrievalQA：文档问答链

👉 把本地文档做成问答系统，比如：“上传公司手册 → 问它公司请假制度”。

📌 示例代码：
```
#假设你已经有一个 PDF 文档或 txt 文件。
from langchain.document_loaders import TextLoader
from langchain.vectorstores import FAISS
from langchain.embeddings import OpenAIEmbeddings
from langchain.chains import RetrievalQA
from langchain.llms import OpenAI

# 1. 加载文档: 把文档变成一段段文本块
loader = TextLoader("example.txt")
documents = loader.load()

# 2. 转向量 + 建立向量库:    把文档中每段内容变成向量，变成一个可检索的知识库
embeddings = OpenAIEmbeddings()
db = FAISS.from_documents(documents, embeddings)

# 3. 构建检索问答系统：retriever类似一个搜索器，在向量库中查找相关的段落；
# RetrievalQA 是大模型 + 检索的组合链（RAG 模式），你问的问题会：
## 先去“向量库”里找相关段落
## 然后再交给 OpenAI() 回答
retriever = db.as_retriever()
qa = RetrievalQA.from_chain_type(llm=OpenAI(), retriever=retriever)

# 4. 提问
print(qa.run("公司请假流程是什么？"))
```

 ## 🎓 5. VectorStore：向量库（例如 FAISS）

👉 把文本转换成向量（Embedding），存入数据库后可以“相似匹配”。

比如：你问“休假规则”，它可以从向量库里找到最相关的段落内容，再丢给 LLM 回答你。

常用向量库：
本地：FAISS、Chroma
云端：Pinecone、Weaviate

