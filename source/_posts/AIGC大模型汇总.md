---
title: AIGC大模型汇总
date: 2023-05-26 13:55:30
categories: 算法
tags: ["AIGC"]
---

转自[https://github.com/chenking2020/FindTheChatGPTer](https://github.com/chenking2020/FindTheChatGPTer)。
汇总开源AIGC大模型，持续更新。

<!-- more -->

## 自主模型篇

该类方法通过自主设计模型或者优化GPT、T5等模型，来实现大模型的预训练、监督微调以及强化学习过程。

### ChatYuan

链接：

* [https://github.com/clue-ai/ChatYuan](https://github.com/clue-ai/ChatYuan)

ChatYuan（元语AI）是由元语智能开发团队开发和发布的，自称第一个国内最早的一个功能型对话大模型，可以写文章、写作业、写诗歌、做中英文间的翻译；一些法律等特定领域问题也可以提供相关信息，该模型目前只支持中文。从披露的技术细节看，底层采用7亿参数规模的T5模型，并基于PromptClue进行了监督微调形成了ChatYuan。该模型基本上是ChatGPT技术路线的三步的第一步，没有实现奖励模型训练和PPO强化学习训练。

### Colossal AI

链接：

* [https://github.com/hpcaitech/ColossalAI](https://github.com/hpcaitech/ColossalAI)

最近，ColossalAI开源了他们的ChatGPT实现。分享了他们的三步策略，完整实现了ChatGPT核心的技术路线。

### ChatGLM

链接：

* [https://github.com/THUDM/ChatGLM-6B](https://github.com/THUDM/ChatGLM-6B)

ChatGLM是清华技术成果转化的公司智谱AI开源的GLM系列的对话模型，支持中英两个语种，目前开源了其62亿参数量的模型。其继承了GLM之前的优势，在模型架构上进行了优化，从而使得部署和应用门槛变低，实现大模型在消费级显卡上的推理应用。

从技术路线上看，其实现了ChatGPT强化学习人类对齐策略，使得生成效果更佳贴近人类价值，其目前能力域主要包括自我认知、提纲写作、文案写作、邮件写作助手、信息抽取、角色扮演、评论比较、旅游建议等，目前其已经开发了正在内测的1300亿的超大模型，算是目前开源平替里面参数规模较大的对话大模型。

该团队近期开源了ChatGLM-6B的多模态版，支持图像、中文和英文的多模态对话。语言模型部分采用ChatGLM-6B，图像部分通过训练BLIP2-Qformer构建起视觉模型与语言模型的桥梁，整体模型共78亿参数。VisualGLM-6B依靠来自于CogView数据集的30M高质量中文图文对，与300M经过筛选的英文图文对进行预训练，中英文权重相同。该训练方式较好地将视觉信息对齐到ChatGLM的语义空间；之后的微调阶段，模型在长视觉问答数据上训练，以生成符合人类偏好的答案。

VisualGLM-6B开源地址为：[https://github.com/THUDM/VisualGLM-6B](https://github.com/THUDM/VisualGLM-6B)

### PaLM-rlhf-pytorch

链接：

* [https://github.com/lucidrains/PaLM-rlhf-pytorch](https://github.com/lucidrains/PaLM-rlhf-pytorch)

其号称首个开源ChatGPT平替项目，其基本思路是基于谷歌语言大模型PaLM架构，以及使用从人类反馈中强化学习的方法（RLHF）。PaLM是谷歌在今年4月发布的5400亿参数全能大模型，基于Pathways系统训练。其可以完成写代码、聊天、语言理解等任务，并且在大多数任务上具有强大的少样本学习性能。同时采用了ChatGPT一样的强化学习机制，能让AI的回答更加符合情景要求，降低模型毒性。

### OpenFlamingo

链接：

* [https://github.com/mlfoundations/open_flamingo](https://github.com/mlfoundations/open_flamingo)

OpenFlamingo是一个对标GPT-4、支持大型多模态模型训练和评估的框架，由非盈利机构LAION重磅开源发布，其是对DeepMind的Flamingo模型的复现。目前开源的是其基于LLaMA的 OpenFlamingo-9B模型。Flamingo模型在包含交错文本和图像的大规模网络语料库上进行训练，具备上下文少样本学习能力。OpenFlamingo实现了原始Flamingo中提出的相同架构，在一个新的多模态C4数据集的5M样本和LAION-2B的10M样本上训练而来。

### MOSS

链接：

* [https://github.com/OpenLMLab/MOSS](https://github.com/OpenLMLab/MOSS)

今年2月21日，复旦大学发布了MOSS，并开放公测，在公测崩溃后引起一些争议。现在该项目迎来重要更新和开源。开源的MOSS支持中英两个语种，且支持插件化，如解方程、搜索等。参数量16B，在约七千亿中英文以及代码单词上预训练得到，后续经过对话指令微调、插件增强学习和人类偏好训练具备多轮对话能力及使用多种插件的能力。

### mPLUG-Owl

链接：

* [https://github.com/X-PLUG/mPLUG-Owl](https://github.com/X-PLUG/mPLUG-Owl)

与miniGPT-4、LLaVA类似，其是一个对标GPT-4的开源多模态大模型，其延续了mPLUG系列的模块化训练思想。其目前开源了7B参数量的模型，同时第一次针对视觉相关的指令理解提出一个全⾯的测试集 OwlEval，通过人工评测对比了已有模型，包括LLaVA、MiniGPT-4等工作，其展示出更优的多模态能力，尤其在多模态指令理解能力、多轮对话能力、知识推理能力等方⾯表现突出。目前遗憾的是跟其他图文大模型一样，仍然只支持英文，但中文版已在其待开源List中。

### PandaLM

链接：

* [https://github.com/WeOpenML/PandaLM](https://github.com/WeOpenML/PandaLM)

PandaLM是一个模型评估大模型，旨在对其他大模型生成内容的偏好进行自动评价，节省人工评估成本。PandaLM自带有Web界面进行分析，同时还支持Python代码调用，仅用三行代码即可对任意模型和数据生成的文本评估，使用很方便。

## LLaMA篇

该类方法主要以LLaMA模型为base，训练出新的模型。LLaMA是由Meta发布的全新人工智能大型语言模型，在生成文本、对话、总结书面材料、证明数学定理或预测蛋白质结构等任务上方面表现良好。LLaMA模型支持20种语言，包括拉丁语和西里尔字母语言，目前看原始模型并不支持中文。可以说LLaMA的史诗级泄露大力推进了类ChatGPT的开源发展。

### stanford-alpaca

链接：

* [https://github.com/tatsu-lab/stanford_alpaca](https://github.com/tatsu-lab/stanford_alpaca)

斯坦福发布的alpaca（羊驼模型），是一个基于LLaMA-7B模型微调出一个新模型，其基本原理是让OpenAI的text-davinci-003模型以self-instruct方式生成52K指令样本，以此来微调LLaMA。该项目已将训练数据、生成训练数据的代码和超参数开源，模型文件尚未开源，以一天多达到5.6K星的关注度。该项工作由于成本低廉、数据易得，大受欢迎，也开启了低成本ChatGPT的效仿之路。

### ChatLLaMA

链接：

* [https://github.com/nebuly-ai/nebullvm/tree/main/apps/accelerate/chatllama](https://github.com/nebuly-ai/nebullvm/tree/main/apps/accelerate/chatllama)

是由Nebuly+AI推出的基于人类反馈强化学习的LLaMA+AI聊天机器人的开源实现，它的技术路线类似 ChatGPT，该项目上线刚刚 2 天，狂揽 5.2K 星。

ChatLLaMA 训练过程算法实现主打比 ChatGPT 训练更快、更便宜，据说能快近15倍，主要特色有：

* 完整的开源实现，允许用户基于预训练的 LLaMA 模型构建 ChatGPT 风格的服务；
* LLaMA 架构更小，使得训练过程和推理速度更快，成本更低；
* 内置了对 DeepSpeed ZERO 的支持，以加速微调过程；
* 支持各种尺寸的 LLaMA 模型架构，用户可以根据自身偏好对模型进行微调。

### OpenChatKit

链接：

* [https://github.com/togethercomputer/OpenChatKit](https://github.com/togethercomputer/OpenChatKit)

OpenChatKit由前OpenAI研究员所在的Together团队，以及LAION、Ontocord.ai团队共同打造。OpenChatKit包含200亿个参数，用GPT-3的开源版本GPT-NoX-20B进行微调。同时，不同ChatGPT的强化学习，OpenChatKit采用一个60亿参数的审核模型，对不合适或者是有害的信息进行过滤，确保生成内容的安全和质量。

### BELLE

链接：

* [https://github.com/LianjiaTech/BELLE](https://github.com/LianjiaTech/BELLE)

基于 Stanford Alpaca ，实现基于Bloom、LLama的监督微调。Stanford Alpaca 的种子任务都是英语，收集的数据也都是英文，该开源项目是促进中文对话大模型开源社区的发展，针对中文做了优化，模型调优仅使用由ChatGPT生产的数据（不包含任何其他数据）。

### alpaca-lora

alpaca-lora是斯坦福大学的另一个巨作，其使用LoRA（low-rank adaptation）技术复现了Alpaca的结果，用了一个更加低成本的方法，只在一块RTX 4090显卡上训练5个小时得到了一个Alpaca水平相当的模型。而且，该模型可以在树莓派上运行。在该项目中，其使用了Hugging Face的PEFT来实现廉价高效的微调。PEFT 是一个库（LoRA 是其支持的技术之一），可以让你使用各种基于 Transformer的语言模型并使用LoRA对其进行微调，从而使得在一般的硬件上廉价而有效地微调模型。该项目github地址是：
[https://github.com/tloen/alpaca-lora](https://github.com/tloen/alpaca-lora)

尽管 Alpaca和alpaca-lora取得了较大的提升，但其种子任务都是英语，缺乏对中文的支持。一方面除了以上提到Belle收集到了大量的中文语料，另一方面基于alpaca-lora等前人工作，来自华中师范大学等机构的三位个人开发者开源的中文语言模型骆驼 (Luotuo)，单卡就能完成训练部署。目前该项目释放了两个模型 luotuo-lora-7b-0.1、luotuo-lora-7b-0.3，还有一个模型在计划中。其github地址是：
[https://github.com/LC1332/Chinese-alpaca-lora](https://github.com/LC1332/Chinese-alpaca-lora)

### Dolly

Dolly在Alpaca的启发下，用Alpaca数据集，在GPT-J-6B上实现微调，由于Dolly本身是一个模型的“克隆”，所以团队最终决定将其命名为“多莉”。这种克隆式在Alpaca启发下越来越多，总结起来大致采用Alpaca开源的数据获取方式，在6B或者7B规模大小的旧模型上进行指令微调，获得类似ChatGPT的的效果。这种思想很经济，也能迅速模仿出ChatGPT的韵味来，广受欢迎，一经推出star爆棚。该项目github地址是：

[https://github.com/databrickslabs/dolly](https://github.com/databrickslabs/dolly)

4月12日，Databricks发布了Dolly2.0，号称业内第一个开源、遵循指令的LLM，数据集由Databricks员工生成，并进行了开源且可用于商业目的。新提出的Dolly2.0是一个120亿参数的语言模型，基于开源EleutherAI pythia模型系列，针对小型开源指令记录语料库进行了微调。

开源地址为：
[https://github.com/databrickslabs/dolly](https://github.com/databrickslabs/dolly)  
[https://huggingface.co/databricks/dolly-v2-12b](https://huggingface.co/databricks/dolly-v2-12b)

### Vicuna和Chinese-Vicuna

斯坦福学者继推出alpaca后，联手CMU、UC伯克利等，推出一个全新模型——130亿参数的Vicuna（俗称小羊驼、骆马）。仅需300美元就能实现ChatGPT 90%的性能。Vicuna是通过在ShareGPT收集的用户共享对话上对LLaMA进行微调训练而来，测试过程使用GPT-4作为评判标准，结果显示Vicuna-13B在超过90%的情况下实现了与ChatGPT和Bard相匹敌的能力。

UC伯克利LMSys org近期又发布了70亿参数的Vicuna，不仅体积小、效率高、能力强，而且只需两行命令就能在M1/M2芯片的Mac上运行，还能开启GPU加速！
github开源地址为：
[https://github.com/lm-sys/FastChat/](https://github.com/lm-sys/FastChat/)

另一个中文版的进行了开源Chinese-Vicuna ，github地址为：
[https://github.com/Facico/Chinese-Vicuna](https://github.com/Facico/Chinese-Vicuna)

### LMFLOW

ChatGPT爆火后，都在寻找通往圣殿的快捷之路，一些类ChatGPT开始出现，尤其是低成本效仿ChatGPT成为一个热门途径。LMFlow就是在这种需求场景下诞生的产物，他使得在3090这样的普通显卡上也能炼大模型。该项目由香港科技大学统计和机器学习实验室团队发起，致力于建立一个全开放的大模型研究平台，支持有限机器资源下的各类实验，并且在平台上提升现有的数据利用方式和优化算法效率，让平台发展成一个比之前方法更高效的大模型训练系统。

利用该项目，即便是有限的计算资源，也能让使用者针对专有领域支持个性化训练。例如LLaMA-7B，一张3090耗时 5 个小时即可完成训练，成本大幅降低。该项目还开放了网页端即刻体验问答服务 (lmflow.com)。LMFlow的出现和开源使得普通资源可以训练问答、陪伴、写作、翻译、专家领域咨询等各种任务。目前很多研究者们正在尝试用该项目训练650亿甚至更高参数量的大模型。

该项目github地址为：
[https://github.com/OptimalScale/LMFlow](https://github.com/OptimalScale/LMFlow)

### Baize白泽

该项目提出了一个自动收集 ChatGPT 对话的方法，让 ChatGPT 自我对话，批量生成高质量多轮对话数据集，分别收集了5万条左右Quora、StackOverflow和MedQA的高质量问答语料，并已经全部开源。同时其改进了LLama模型，效果还不错。白泽同样采用目前低成本的LoRA微调方案，获得白泽-7B、13B 和30B三种不同尺度，以及一个医疗垂直领域的模型。遗憾的是中文名字起的不错，但目前仍然不支持中文，中文的白泽模型据悉在计划中，未来发布。其开源github地址是：
[https://github.com/project-baize/baize](https://github.com/project-baize/baize)

### Koala考拉

基于LLama的ChatGPT平替继续发酵，UC伯克利的伯克利发布了一个可以在消费级GPU上运行的对话模型Koala，参数达到13B。Koala 的训练数据集包括如下几个部分：ChatGPT数据和开源数据（Open Instruction Generalist (OIG)、斯坦福 Alpaca 模型使用的数据集、Anthropic HH、OpenAI WebGPT、OpenAI Summarization）。Koala模型在EasyLM中使用JAX/Flax实现，用了8 个A100 GPU，完成2轮迭代需要6个小时。评测效果优于Alpaca，达到ChatGPT 50%的性能。

开源地址：[https://github.com/young-geng/EasyLM](https://github.com/young-geng/EasyLM)

### StackLLaMA

随着斯坦福Alpaca的出现，一大堆基于LLama的羊驼家族和扩展动物家族开始出现，终于Hugging Face研究人员近期发布了一篇博客StackLLaMA：用RLHF训练LLaMA的实践指南。同时也发布了一个70亿参数的模型——StackLLaMA。这是一个通过人类反馈强化学习在LLaMA-7B微调而来的模型。详细见其博客地址：
[https://huggingface.co/blog/stackllama](https://huggingface.co/blog/stackllama)

### Chinese-LLaMA-Alpaca

该项目针对中文对LLaMA进行了优化，并开源了其精调对话系统。该项目具体步骤包括：

1. 词表扩充，采用sentencepiece在中文数据上进行了训练构建，并与LLaMA词表进行了合并；
2. 预训练，在新词表上，约20G左右的通用中文语料进行了训练，训练中运用了LoRA技术；
3. 利用Stanford Alpaca，在51k数据上进行了精调训练获得对话能力。

开源地址为：[https://github.com/ymcui/Chinese-LLaMA-Alpaca](https://github.com/ymcui/Chinese-LLaMA-Alpaca)

### Deep Speed Chat

该项目带来了全民ChatGPT的时代，训练成本再次大幅降低。项目是微软基于其Deep Speed优化库开发而成，具备强化推理、RLHF模块、RLHF系统三大核心功能，可将训练速度提升15倍以上，成本却大幅度降低。例如，一个130亿参数的类ChatGPT模型，只需1.25小时就能完成训练。
开源地址为：[https://github.com/microsoft/DeepSpeed](https://github.com/microsoft/DeepSpeed)

### Wombat

该项目采取了不同于RLHF的方式RRHF进行人类偏好对齐，RRHF相对于RLHF训练的模型量和超参数量远远降低。RRHF训练得到的Wombat-7B在性能上相比于Alpaca有显著的增加，和人类偏好对齐的更好。

开源地址为：[https://github.com/GanjinZero/RRHF](https://github.com/GanjinZero/RRHF)

### Guanaco

Guanaco是一个基于目前主流的LLaMA-7B模型训练的指令对齐语言模型，原始52K数据的基础上，额外添加了534K+条数据，涵盖英语、日语、德语、简体中文、繁体中文（台湾）、繁体中文（香港）以及各种语言和语法任务。丰富的数据助力模型的提升和优化，其在多语言环境中展示了出色的性能和潜力。

开源地址为：[https://github.com/Guanaco-Model/Guanaco-Model.github.io](https://github.com/Guanaco-Model/Guanaco-Model.github.io)

### LLMZoo（凤凰Phoenix和Chimera）

LLMZoo，即LLM动物园开源项目维护了一系列开源大模型，其中包括了近期备受关注的来自香港中文大学（深圳）和深圳市大数据研究院的王本友教授团队开发的Phoenix（凤凰）和Chimera等开源大语言模型，其中文本效果号称接近百度文心一言，GPT-4评测号称达到了97%文心一言的水平，在人工评测中五成不输文心一言。

Phoenix 模型有两点不同之处：在微调方面，指令式微调与对话式微调的进行了优化结合；支持四十余种全球化语言。

开源地址为：[https://github.com/FreedomIntelligence/LLMZoo](https://github.com/FreedomIntelligence/LLMZoo)

### OpenAssistant

OpenAssistant是一个开源聊天助手，其可以理解任务、与第三方系统交互、动态检索信息。据其说，其是第一个在人类数据上进行训练的完全开源的大规模指令微调模型。该模型主要创新在于一个较大的人类反馈数据集（详细说明见数据篇），公开测试显示效果在人类对齐和毒性方面做的不错，但是中文效果尚有不足。

开源地址为：[https://github.com/LAION-AI/Open-Assistant](https://github.com/LAION-AI/Open-Assistant)

### HuggingChat

HuggingChat是Huggingface继OpenAssistant推出的对标ChatGPT的开源平替。其能力域基本与ChatGPT一致，在英文等语系上效果惊艳，被成为ChatGPT目前最强开源平替。但笔者尝试了中文，可谓一塌糊涂，中文能力还需要有较大的提升。HuggingChat的底座是oasst-sft-6-llama-30b，也是基于Meta的LLaMA-30B微调的语言模型。

该项目的开源地址是：[https://huggingface.co/OpenAssistant/oasst-sft-6-llama-30b-xor](https://huggingface.co/OpenAssistant/oasst-sft-6-llama-30b-xor)  
在线体验地址是：[https://huggingface.co/chat](https://huggingface.co/chat)

### StableLM

StableVicuna是一个Vicuna-13B v0（LLaMA-13B上的微调）的RLHF的微调模型。

StableLM-Alpha是以开源数据集the Pile（含有维基百科、Stack Exchange和PubMed等多个数据源）基础上训练所得，训练token量达1.5万亿。

为了适应对话，其在Stanford Alpaca模式基础上，结合了Stanford's Alpaca, Nomic-AI's gpt4all, RyokoAI's ShareGPT52K datasets, Databricks labs' Dolly, and Anthropic's HH.等数据集，微调获得模型StableLM-Tuned-Alpha

该项目的开源地址是：[https://github.com/Stability-AI/StableLM](https://github.com/Stability-AI/StableLM)

### 华驼(HuaTuo)

该模型垂直医学领域，经过中文医学指令精调/指令集对原始LLaMA-7B模型进行了微调，增强了医学领域上的对话能力。

该项目的开源地址是：[https://github.com/SCIR-HI/Huatuo-Llama-Med-Chinese](https://github.com/SCIR-HI/Huatuo-Llama-Med-Chinese)

### ChatRWKV(Raven)

该模型的底座采用了自主研发的RWKV语言模型，100% RNN，微调部分仍然是经典的Alpaca、CodeAlpaca、Guanaco、GPT4All、 ShareGPT等。其开源了1B5、3B、7B和14B的模型，目前支持中英两个语种，提供不同语种比例的模型文件。

该项目的开源地址是：
[https://github.com/BlinkDL/ChatRWKV](https://github.com/BlinkDL/ChatRWKV)  
[https://huggingface.co/BlinkDL/rwkv-4-raven](https://huggingface.co/BlinkDL/rwkv-4-raven)

### SELF-ALIGN和Dromedary

目前大部分类ChatGPT基本都是采用人工对齐方式，如RLHF，Alpaca模式只是实现了ChatGPT的效仿式对齐，对齐能力受限于原始ChatGPT对齐能力。卡内基梅隆大学语言技术研究所、IBM 研究院MIT-IBM Watson AI Lab和马萨诸塞大学阿默斯特分校的研究者提出了一种全新的自对齐方法。其结合了原则驱动式推理和生成式大模型的生成能力，用极少的监督数据就能达到很好的效果。该项目工作成功应用在LLaMA-65b模型上，研发出了Dromedary（单峰骆驼）。

该项目的开源地址是：[https://github.com/IBM/Dromedary](https://github.com/IBM/Dromedary)

### LLaVA

LLaVA是一个多模态的语言和视觉对话模型，类似GPT-4，其主要还是在多模态数据指令工程上做了大量工作，目前开源了其13B的模型文件。从性能上，据了解视觉聊天相对得分达到了GPT-4的85%；多模态推理任务的科学问答达到了SoTA的92.53%。该项目的开源地址是：
[https://github.com/haotian-liu/LLaVA](https://github.com/haotian-liu/LLaVA)

### miniGPT-4

从名字上看，该项目对标GPT-4的能力域，实现了一个缩略版。该项目来自来自沙特阿拉伯阿卜杜拉国王科技大学的研究团队。该模型利用两阶段的训练方法，先在大量对齐的图像-文本对上训练以获得视觉语言知识，然后用一个较小但高质量的图像-文本数据集和一个设计好的对话模板对预训练的模型进行微调，以提高模型生成的可靠性和可用性。该模型语言解码器使用Vicuna，视觉感知部分使用与BLIP-2相同的视觉编码器。

该项目的开源地址是：[https://github.com/Vision-CAIR/MiniGPT-4](https://github.com/Vision-CAIR/MiniGPT-4)

### InstructBLIP

该项目与上述MiniGPT-4底层具有很大相通的地方，文本部分都使用了Vicuna，视觉部分则是BLIP-2微调而来。在论文和评测中，该模型在看图理解、逻辑推理和对话描述方面具有强大的优势，甚至号称超过GPT-4。InstructBLIP强大性能主要体现在视觉-语言指令数据集构建和训练上，使得模型对未知的数据和任务具有零样本能力。在指令微调数据上为了保持多样性和可及性，研究人员一共收集了涵盖了11个任务类别和28个数据集，并将它们转化为指令微调格式。同时其提出了一种指令感知的视觉特征提取方法，充分利用了BLIP-2模型中的Q-Former架构，指令文本不仅作为输入给到LLM，同时也给到了QFormer。

该项目的开源地址是：[https://github.com/salesforce/LAVIS/tree/main/projects/instructblip](https://github.com/salesforce/LAVIS/tree/main/projects/instructblip)

### BiLLa

BiLLa是开源的推理能力增强的中英双语LLaMA模型，该模型训练过程和Chinese-LLaMA-Alpaca有点类似，都是三阶段：词表扩充、预训练和指令精调。不同的是在增强预训练阶段，BiLLa加入了任务数据，且没有采用Lora技术，精调阶段用到的指令数据也丰富的多。该模型在逻辑推理方面进行了特别增强，主要体现在加入了更多的逻辑推理任务指令。

该项目的开源地址是：[https://github.com/Neutralzz/BiLLa](https://github.com/Neutralzz/BiLLa)

### Ziya-LLaMA-13B-v1

该项目是由IDEA开源，被成为"姜子牙"，是在LLaMA-13B基础上训练而得。该模型也采用了三阶段策略，一是重新构建中文词表；二是在千亿token量级数据规模基础上继续预训练，使模型具备原生中文能力；最后经过500万条多任务样本的有监督微调（SFT）和综合人类反馈训练（RM+PPO+HFFT+COHFT+RBRS），增强各种AI能力。其同时开源了一个评估集，包括常识类问答、推理、自然语言理解任务、数学、写作、代码、翻译、角色扮演、翻译9大类任务，32个子类，共计185个问题。

该项目的开源地址是：[https://huggingface.co/IDEA-CCNL/Ziya-LLaMA-13B-v1](https://huggingface.co/IDEA-CCNL/Ziya-LLaMA-13B-v1)  
评估集开源地址是：[https://huggingface.co/datasets/IDEA-CCNL/Ziya-Eval-Chinese](https://huggingface.co/datasets/IDEA-CCNL/Ziya-Eval-Chinese)
