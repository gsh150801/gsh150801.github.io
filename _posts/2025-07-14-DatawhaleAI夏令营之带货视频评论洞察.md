---
layout:     post
title:      Datawhale AI夏令营之带货视频评论洞察
subtitle:   第一次正经尝试
date:       2025-07-14
author:     GSH
header-img: img/post-bg-re-vs-ng2.jpg
catalog: true
tags:
    - Blog
---

Datawhale AI夏令营之带货视频评论洞察

0.1 赛事背景
在电商直播爆发式增长的数字化浪潮下，短视频平台积累了海量带货视频及用户互动数据。这些数据不仅是消费者对商品体验的直接反馈，更蕴含着驱动商业决策的深层价值。在此背景下，基于带货视频评论的用户洞察分析，已成为品牌优化选品策略、评估网红带货效能的关键突破口。
带货视频评论用户洞察的核心逻辑，在于对视频内容与评论数据的联合深度挖掘。通过智能识别视频中推广的核心商品，结合评论区用户的情感表达与观点聚合，企业能够精准捕捉消费者对商品的真实态度与需求痛点。这种分析方式不仅能揭示用户对商品功能、价格、服务的多维评价，还可通过情感倾向聚类，构建消费者偏好画像，为选品策略优化和网红合作评估提供数据支撑。
本挑战赛聚焦"商品识别-情感分析-聚类洞察"的完整链条：参赛者需先基于视频内容建立商品关联关系，进而从非结构化评论中提取情感倾向，最终通过聚类总结形成结构化洞察。这一研究路径将碎片化的用户评论转化为可量化分析的商业智能，既可帮助品牌穿透数据迷雾把握消费心理，又能科学评估网红的内容种草效果与带货转化潜力，实现从内容营销到消费决策的全链路价值提升。在直播电商竞争白热化的当下，此类分析能力正成为企业构建差异化竞争优势的核心武器。

0.2 赛事任务
参赛者需基于提供的带货视频文本及评论文本数据，完成以下三阶段分析任务：
1）【商品识别】精准识别推广商品；
2）【情感分析】对评论文本进行多维度情感分析，涵盖维度见数据说明；
3）【评论聚类】按商品对归属指定维度的评论进行聚类，并提炼类簇总结词。

0.3 赛题数据
基于带货视频评论的用户洞察挑战赛数据集，主要包括两部分：一部分是用于商品识别的；另一部分则是用于根据评论来判断评论的各种倾向（情感倾向、是否具有场景倾向、是否具有疑问倾向、是否具有建议倾向）
难点集中于情感分析这一任务中，一方面是有标签的数据不多，另一方面则是因为这一任务的准确性也决定了后面的评论聚类任务的准确性。

baseline是全机器学习的方案，个人认为除了聚类任务以外，其他两个任务用大语言模型来完成再合适不过。因为LLM对语言的理解能力是比较强的。
分别对商品识别任务和情感分析任务编写提示词，下面是我编写的提示词：
```python
import pandas as pd
from sparkai.llm.llm import ChatSparkLLM, ChunkPrintHandler
from sparkai.core.messages import ChatMessage
#星火认知大模型Spark Max的URL值，其他版本大模型URL值请前往文档（https://www.xfyun.cn/doc/spark/Web.html）查看
SPARKAI_URL = 'wss://spark-api.xf-yun.com/v4.0/chat'
#星火认知大模型调用秘钥信息，请前往讯飞开放平台控制台（https://console.xfyun.cn/services/bm35）查看
SPARKAI_APP_ID = 'f580...5'
SPARKAI_API_SECRET = 'OTc5NTBjZWY0MDI5ZTE5YjQzMDky...m'
SPARKAI_API_KEY = '5a0911fb98a348a5ac60e2e6b636....'#需要注意这里
#星火认知大模型Spark Max的domain值，其他版本大模型domain值请前往文档（https://www.xfyun.cn/doc/spark/Web.html）查看
SPARKAI_DOMAIN = '4.0Ultra'#需要注意这里



prompt_product_identification = '''
角色：你现在是个导购.
任务：你需要根据商品视频信息（逗号分隔的视频描述信息和视频类别信息）来识别所涉及的商品是商品列表中的那一种，然后直接输出商品名称。
商品列表：'Xfaiyx Smart Recorder','Xfaiyx Smart Translator'。
-----------------------------------------------------------
示例1：
商品视频信息："Xfaiyx Smart Recorder Product Review,Muzain Reviews;;Gadget;;Technology;;Reviews;;Unboxing;;iflytec"
输出的商品名称："Xfaiyx Smart Recorder"

示例2：
商品视频信息："Would you use a Smart Record for school!! Use code Xfaiyx20 for 20% off @Xfaiyx #tiktokBackToSchool #XfaiyxRecorder #tiktokshopfinds #Xfaiyx #VoiceRecorder #AiVoiceRecorder #VoiceRecorderWithTranscription,tiktokbacktoschool;;Xfaiyxrecorder;;tiktokshopfinds;;Xfaiyx;;voicerecorder;;aivoicerecorder;;voicerecorderwithtranscription"
输出的商品名称："Xfaiyx Smart Recorder"

示例3：
商品视频信息："Champions League Schlägerei🫨👊🏻 Dortmund Fan rastet aus!🚨👮🏽‍♂️,"
输出的商品名称："Xfaiyx Smart Translator"

示例4：
商品视频信息："'은행 금리 52%' 역대 최악의 인플레이션, 이민가는 터키 사람들의 현실 - 번외편 🇹🇷"
输出的商品名称："Xfaiyx Smart Translator"
-----------------------------------------
接下来，请根据商品视频信息，识别并输出相关的商品名称。
商品视频信息："{video_inf}"
输出的商品名称：
'''


spark = ChatSparkLLM(
        spark_api_url=SPARKAI_URL,
        spark_app_id=SPARKAI_APP_ID,
        spark_api_key=SPARKAI_API_KEY,
        spark_api_secret=SPARKAI_API_SECRET,
        spark_llm_domain=SPARKAI_DOMAIN,
        streaming=False,
    )
import pandas as pd
video_data = pd.read_csv("origin_videos_data.csv")
comments_data = pd.read_csv("origin_comments_data.csv")
product_names = []
for i in range(len(video_data)):
    video_inf = str(video_data.loc[i,'video_desc'])+str(video_data.loc[i,'video_tags'])
    messages = [ChatMessage(
        role="user",
        content=prompt_product_identification.format(video_inf=video_inf)
    )]
    handler = ChunkPrintHandler()
    a = spark.generate([messages], callbacks=[handler])
    # print(a.generations[0][0].message.content)
    product_names.append(a.generations[0][0].message.content)
```



```python
import pandas as pd
from sparkai.llm.llm import ChatSparkLLM, ChunkPrintHandler
from sparkai.core.messages import ChatMessage
#星火认知大模型Spark Max的URL值，其他版本大模型URL值请前往文档（https://www.xfyun.cn/doc/spark/Web.html）查看
SPARKAI_URL = 'wss://spark-api.xf-yun.com/v3.5/chat'#'wss://spark-api.xf-yun.com/v4.0/chat'
#星火认知大模型调用秘钥信息，请前往讯飞开放平台控制台（https://console.xfyun.cn/services/bm35）查看
SPARKAI_APP_ID = 'f580...5'
SPARKAI_API_SECRET = 'OTc5NTBjZWY0MDI5ZTE5YjQzMDky..lm'
SPARKAI_API_KEY = '5a0911fb98a348a5ac60e2e6b63*.*.'#需要注意这里
#星火认知大模型Spark Max的domain值，其他版本大模型domain值请前往文档（https://www.xfyun.cn/doc/spark/Web.html）查看
SPARKAI_DOMAIN = 'generalv3.5'#'4.0Ultra'#需要注意这里

prompt_emotion_identification = '''
角色：你现在是个评论数据分析员。
任务：你需要根据'Xfaiyx Smart Recorder'和'Xfaiyx Smart Translator'两种商品的文本评论信息来进行情感分析。
你需要输出sentiment_category、user_scenario 、user_question、user_suggestion四种指标。
其中sentiment_category表示关于商品的情感倾向分类，其分类的数值含义为：1（正面）、2（负面）、3（正负都包含）、4（中性）、5（不相关）；
user_scenario表示评论是否与用户的使用场景（旅游出行等）有关，0表示否，1表示是；
user_question表示评论是否与用户对商品的疑问（怎么使用、是否需要联网等）有关，0表示否，1表示是；
user_suggestion表示评论是否与用户对商品的建议有关，0表示否，1表示是；
对于每条评论信息，你仅需要直接输出四种指标对应的标签数值（用逗号分隔）。
如果评论不是中文，请先翻译成中文，然后分析，最后直接输出四种指标对应的标签数值（用逗号分隔）。
-----------------------------------------------------------
示例1：
评论："Pro just develop bro codes"
输出的四种指标标签数值："5,0,0,0"

示例2：
评论："how much is the price"
输出的四种指标标签数值："1,0,1,0"

示例3：
评论："The audio quality is surprisingly clear, even in crowded places. Perfect for interviews or lectures!"
输出的四种指标标签数值："1,1,0,0"

示例4：
评论："Defo buying this with your code 👍🏾"
输出的四种指标标签数值："1,0,0,0"

示例5：
评论："The s24 ultra does this already for free 😭"
输出的四种指标标签数值："2,0,0,0"

示例6：
评论："Les va quitar el trabajo a los intérpretes profesionales, pero es una herramienta impresionante."
输出的四种指标标签数值："3,0,0,0"

示例7：
评论："I think Xfaiyx could be improved by adding a feature to slow down translations for better clarity."
输出的四种指标标签数值："4,0,0,1"

示例8：
评论："I’ve been using the Xfaiyx Smart Translator for budget travel, and it’s been great for basic communication. Does it support voice commands?"
输出的四种指标标签数值："1,1,1,0"
-----------------------------------------
接下来，请根据商品评论，识别并输出相关的指标对应的标签数值。
商品评论："{comment_text}"
输出的四种指标标签数值：
'''

spark = ChatSparkLLM(
        spark_api_url=SPARKAI_URL,
        spark_app_id=SPARKAI_APP_ID,
        spark_api_key=SPARKAI_API_KEY,
        spark_api_secret=SPARKAI_API_SECRET,
        spark_llm_domain=SPARKAI_DOMAIN,
        streaming=False,
    )
import pandas as pd
video_data = pd.read_csv("origin_videos_data.csv")
comments_data = pd.read_csv("origin_comments_data.csv")
# emotion_indexs = []
for i in range(4224, len(comments_data)):
    if i%100 == 0:
        print(i)
    comment_text = str(comments_data.loc[i,'comment_text'].strip())
    messages = [ChatMessage(
        role="user",
        content=prompt_emotion_identification.format(comment_text=comment_text)
    )]
    handler = ChunkPrintHandler()
    a = spark.generate([messages], callbacks=[handler])
    # print(a.generations[0][0].message.content)
    emotion_indexs.append(a.generations[0][0].message.content)
```



