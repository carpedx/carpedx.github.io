---
layout: post
title: ChatGPT Prompt提示词
categories: gpt
description: 了解常见GPT应用背后的原理和对应prompt写法
keywords: gpt, prompt
---



**了解常见GPT应用背后的原理和对应prompt写法**

> API 文档：[OpenAI API](https://help.openai.com/en/articles/6654000-best-practices-for-prompt-engineering-with-openai-api)、[Azure OpenAI API](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/concepts/advanced-prompt-engineering?pivots=programming-language-chat-completions)
>
> API 案例：[OpenAI Cookbook](https://github.com/openai/openai-cookbook) 覆盖到了绝大多数应用场景
>
> Prompt教程：[吴恩达 Prompt 教程](https://github.com/GitHubDaily/ChatGPT-Prompt-Engineering-for-Developers-in-Chinese)

------



#### 清晰，明确，避免模糊的词语

**案例：**

较差 ❌

```tex
写一首关于 OpenAI 的诗。
```

较好 ✅

```tex
以{著名诗人}的风格写一首关于 OpenAI 的鼓舞人心的短诗，重点关注最近推出的 DALL-E 产品（DALL-E 是一种文本到图像的 ML 模型）
```



#### 用 `###` 或者 `"""` 将指令和待处理的内容分开

**案例：**

较差 ❌

```tex
将下面的文本总结为最重要点的要点列表。

{此处输入文字}
```

较好 ✅

```go
将下面的文本总结为最重要点的要点列表。

文本："""
{此处输入文本} 
"""
```



#### 指定输出的格式

**案例：**

较差 ❌

```tex
提取下面文本中提到的实体。提取以下 4 种实体类型: 公司名称、人名、特定主题和主题。

文本：{文本}
```

较好 ✅

```tex
提取下面文本中提到的重要实体。首先提取所有公司名称，然后提取所有人名，然后提取适合内容的特定主题，最后提取一般总体主题

所需格式：
公司名称：<逗号分隔的公司名称列表>
人名：-||-
特定主题：-||-
一般主题：-||-

文本：{此处输入文本}
```



#### 与其只说不该做什么，不如说该做什么

**案例：**

较差 ❌

```tex
以下是代理与客户之间的对话。不要询问用户名或密码。不要重复。

客户：我无法登录我的帐户。
代理人：
```

较好 ✅

```tex
以下是代理与客户之间的对话。代理将尝试诊断问题并提出解决方案，同时避免询问任何与 PII 相关的问题。不要询问用户名或密码等 PII，而是让用户参阅帮助文章 www.samplewebsite.com/help/faq

客户：我无法登录我的帐户。
代理人：
```



#### 特定于代码生成，使用“引导词”将模型推向特定模式

**案例：**

较差 ❌

```tex
# 编写一个简单的 python 函数
# 1. 向我询问一个以英里为单位的数字
# 2. 将英里转换为公里
```

较好 ✅

> 在下面的代码示例中，向模型添加“ *import ”提示，表明它应该开始用 Python 编写。*（类似地，“SELECT”是 SQL 语句开始的一个很好的提示。）

```tex
# 编写一个简单的 python 函数
# 1. 向我询问一个以英里为单位的数字
# 2. 它将英里转换为公里
 
import
```



#### 角色扮演

> [ChatGPT 英文调教指南](https://github.com/f/awesome-chatgpt-prompts)，[ChatGPT 中文调教指南](https://github.com/PlexPt/awesome-chatgpt-prompts-zh)

**案例：**

较差 ❌

```tex
给我一个减肥计划
```

较好 ✅

```tex
我想让你扮演一个专业的健身私人教练。你应该利用你的运动科学知识、营养建议和其他相关因素为你的客户定制专业的计划。
```



#### `Few-Shot` 少样本提示

**案例：**

```tex
这组中的奇数加起来等于偶数：4,8,9,75,12,2,1
答：答案是False
这一组中的奇数加起来为偶数：17,10,19,4,8,12,24
答：答案是True
这一组中的奇数加起来为偶数：16,11,14,4,8,13,24
答：答案是True
这组中的奇数加起来等于偶数：17,9,10,12,13,4,2
答：答案是False
这一组中的奇数加起来为偶数：15,32,5,13,82,7,1
答：
```



#### `CoT` 思维链（引导模型思考）

加上这个咒语就可以形成思维链

> Let's think step by step
>
> 让我们一步一步地思考

**案例：**

```tex
问: 罗杰有5个网球，他又买了两罐网球。每罐可以有3个网球，他现在有多少个网球？
答: 罗杰从5个球开始。2罐3只网球是6只网球，5+6=11只，答案是11
问: 自助餐厅有23个苹果。如果他们吃了20个，又买了6只，他们有多少只苹果？
答:
```

```tex
问：这个群体中的奇数加起来是一个偶数：4,8,9,15,12,2,1
答：把所有奇数(9,15,1)相加，得到25，答案是False
问：这个组中的奇数加起来等于偶数：15,32,5,13,82,7,1
答：
```



#### Search API + GPT

> 结合搜索数据让GPT回答

思路如下：

1. 应用端先去做搜索
2. 然后再应用层面把搜索的结果去调用GPT模型
3. 让GPT对搜索的结果进行总结和归纳来回答用户问题

提问规则：

```tex
WEBSEARCH_PTOMPT_TEMPLATE =
"""
web search results:
{web_results}

Current date: {current_date}

Instructions: Using the provided websearch results, write a comprehensivereply to the given query. Make sure to cite results using[[number](URL)] notation after thereference.

Query: {query}

Reply in {reply_language}
"""
```

> {web_results} 搜索API返回的结果
>
> {current_date} 当前日期
>
> {query} 提的问题
>
> {reply_language} 返回语言



#### Embedding（嵌入）Search + GPT

> 结合本地数据让GPT回答

思路如下：

1. 首先把数据按照某种规则切片，每一个切片小于GPT能接受的最大字符限制
2. 切片后通过 Embedding 转换成向量，保存到本地向量数据库
3. 接着把用户问题转换成向量，从数据库中找到（通过计算距离）最相关的几个片段
4. 最后把片段和用户的问题整理成一个prompt，传给GPT帮我们总结回答

提问规则：

```tex
Answer the question based on the context below,and if the question can't beanswered based on the context, say "I don't know"

Context: {context}

Question: {query}

Answer:
```

> {context} 最相关的几个片段
>
> {query} 提的问题



#### ReAct 实现 GPT 插件

思路如下：

1. 让模型进行推理
2. 在推理的过程中，决定调用那个外部工具
3. 当调用外部工具后，再把工具生成的结果告诉模型
4. 然后继续推理下一步，直到把任务完成

**案例（下列内容中加粗字体为GPT返回的数据）：**

第一步（提问）

>尽可能的去回答以下问题，你可以使用以下的工具：
>
>搜索API：当你需要去公网搜索最新信息的时候可以用到
>计算器：当你需要回答数学计算的时候可以用到
>
>请使用以下格式回答：
>
>问题：你必须回答的问题
>
>思考：你应该一直保持思考，思考要怎么解决问题
>动作：<一个工具名>。每次动作只选择一个工具。工具列表 [搜索API，计算器]
>输入：<调用工具时需要传入的参数>
>观察：<工具返回的结果>
>
>..这个“思考-动作-输入-观察”的循环可以重复N次
>
>思考：最后，你应该知道最终结果了
>最终结果：针对于原始问题，输出最终结果。以“任务完成”结尾。
>
>开始！
>问题：周杰伦今年多大了？他的年龄的0.23次方是多少
>思考：
>
>**我需要知道周杰伦今年的年龄，然后进行计算。**
>**动作：搜索API**
>**输入：周杰伦年龄**



第二步（设置 `Stop Sequences`）

> ``Stop Sequence` 是一种 GPT 设置，通过它可以确定你的聊天会话的结束标志。当你在 ChatGPT 中设置 `Stop sequence` 为 `观察` 时，表示你的聊天会话结束标志是 `观察`。

设置 `Stop Sequences` 为 `观察`、`任务完成`



第三步（去搜索周杰伦的年龄）

得到`44岁`



第四步（输入年龄继续提问）

>尽可能的去回答以下问题，你可以使用以下的工具：
>
>搜索API：当你需要去公网搜索最新信息的时候可以用到
>计算器：当你需要回答数学计算的时候可以用到
>
>请使用以下格式回答：
>
>问题：你必须回答的问题
>
>思考：你应该一直保持思考，思考要怎么解决问题
>动作：<一个工具名>。每次动作只选择一个工具。工具列表 [搜索API，计算器]
>输入：<调用工具时需要传入的参数>
>观察：<工具返回的结果>
>
>..这个“思考-动作-输入-观察”的循环可以重复N次
>
>思考：最后，你应该知道最终结果了
>最终结果：针对于原始问题，输出最终结果。以“任务完成”结尾。
>
>开始！
>问题：周杰伦今年多大了？他的年龄的0.23次方是多少
>思考：
>
>**我需要知道周杰伦今年的年龄，然后进行计算。**
>**动作：搜索API**
>**输入：周杰伦年龄**
>
>观察：44岁
>思考：
>
>**我需要计算周杰伦年龄的0.23次方，然后得到结果。**
>**动作：计算器**
>**输入：44^0.23**



第五步（计算44的0.23次方）

得到 `2.38777892984`



第六步（继续提问）

>尽可能的去回答以下问题，你可以使用以下的工具：
>
>搜索API：当你需要去公网搜索最新信息的时候可以用到
>计算器：当你需要回答数学计算的时候可以用到
>
>请使用以下格式回答：
>
>问题：你必须回答的问题
>
>思考：你应该一直保持思考，思考要怎么解决问题
>动作：<一个工具名>。每次动作只选择一个工具。工具列表 [搜索API，计算器]
>输入：<调用工具时需要传入的参数>
>观察：<工具返回的结果>
>
>..这个“思考-动作-输入-观察”的循环可以重复N次
>
>思考：最后，你应该知道最终结果了
>最终结果：针对于原始问题，输出最终结果。以“任务完成”结尾。
>
>开始！
>问题：周杰伦今年多大了？他的年龄的0.23次方是多少
>思考：
>
>**我需要知道周杰伦今年的年龄，然后进行计算。**
>**动作：搜索API**
>**输入：周杰伦年龄**
>
>观察：44岁
>思考：
>
>**我需要计算周杰伦年龄的0.23次方，然后得到结果。**
>**动作：计算器**
>**输入：44^0.23**
>
>观察：2.38777892984
>思考：
>
>**最终结果：周杰伦今年44岁，他的年龄的0.23次方是2.38777892984。**

