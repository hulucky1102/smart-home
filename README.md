Overview
-- **家居领域任务导向型对话系统 ** -针对嵌入式离线所提出的家居领域任务导向型对话系统基本架构如下：

简单实现设备有 空调、窗帘、灯、电饭煲、风扇、微波炉、加湿器 

*********************************************************
** 存在一些问题， 不是最终版本。只是给没思路的同学一个简单的思路 **
*********************************************************

意图识别实体抽取 -- 状态追踪 -- 动作选择 -- 系统回复提取出状态表中相对应的槽值

# 一 意图识别和实体抽取

意图识别和实体抽取采用了两种模型架构：

1) Embedding -- Bilstm -- Layernormal -- GlobalConv -- Dense 神经网络进行搭建.

2) Embedding -- Encoder -- Dense 神经网络模型进行搭建，且Encoder部分采用参数复用进一步缩小参数量.

# 二 状态追踪

根据家居场景设备的不同，构建不同的状态表，进行场景隔离。减少多轮对话中不同场景间的跳转对动作选择的影响，以及系统回复是槽位值的抽取。

# 三 动作选择

动作选择采用了两种方式（先规则后模型）：

1) 通过story数据集构建训练数据，数据采用one-hot对输入进行编码。
Input -- Embedding -- Bilstm -- Layernormal -- GlobalConv -- Dense

Action： 模型前一轮的Action输出，Action只追溯到当前意图所对应的前一轮。 Entities： 槽值取最多前三轮槽值所对应的Key，不关注槽位所提取出的具体值，只关注于槽位是否被填充。 Intent： 意图的取值为当前轮意图的输出以及前一轮的意图 Label : 标签为当前轮的Action

2) 通过对Story处理构建为规则映射列表，将NER的输出在列表中进行匹配。若匹配成功取出相对应的Action，否则将NER的输出输入模型进行Acton判断，由于意图可能存在未表明设备的情形，因此在当前轮操作设备未明确的情况下采取设备状态继承。


# 四 系统回复

根据Action提取出状态表中相对应的槽值并选择相对应的预设动作。当关键值缺失时进行必要的询问。
