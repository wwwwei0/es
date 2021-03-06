使用 [`Hanlp`](https://github.com/hankcs/HanLP) 与 [`FastText`](https://github.com/facebookresearch/fastText) 完成微博文本分类测试之二

**需要模型**

一个主分类预测模型、六个次分类预测模型（新闻内容、营销内容、用户主动提及、求助上访、行业文章、其他）

**文本分类预测流程**

文本输入 -&gt; 使用 `Hanlp` 处理文本（[关键词](http://www.hankcs.com/nlp/textrank-algorithm-to-extract-the-keywords-java-implementation.html)、[短语提取](http://www.hankcs.com/nlp/extraction-and-identification-of-mutual-information-about-the-phrase-based-on-information-entropy.html)）

处理后文本输入 -&gt; 主分类模型预测（预测结果如：新闻内容）

选择新闻内容对应的次文本预测模型 -&gt; 输入处理后的文本 -&gt; 模型预测分类结果（如：新闻评价）

**模型训练流程**

[文本语料](https://docs.google.com/spreadsheets/d/1KshnYphZzOR73TATEMebrCrx1bfwpH4GUkSUUZBWKWw/edit?usp=sharing)，只使用主分类、次分类、内容带转发三个字段

主分类（训练集 1500 条、测试集 100 条，随机样本）

```
处理后文本训练集

__label__求助上访 冤案 机关 花开功 淮安 人为 纪委 江苏省 最高人民法院 法院 造假 制造 淮安市 辩护 难道 检察院 案件 手里 校长 办案 执政 花姐 责任 刘金滨 律师 妻子 欺骗 陷害 不能 反腐倡廉 栽赃 花开功妻子 花开功校长 办案机关 责任花姐 刘金滨律师 花姐花开功 花开功妹妹 侄女花开功 假案冤案 冤案更多 在线最高人民检察院 头条花叶 妹妹花开功 律师花开功 是不是故弄玄虚 校长刘金滨 淮安花开功 花叶最高人民法院 花开功侄女 陷害栽赃 难以遁形 淮安头条 造假心中有数 手里辩护 及时雨反腐倡廉 人为假案 说是冤案 执政为民 栽赃执政 妻子及时雨
__label__营销内容 李宇春 追星 生长 野蛮 ## 影响力 大数 明星 歌手 现场 乐星 头条 一直 今日 实力 获得 恭喜 说话 App# 厉害 #@ @ForFans 实至名归 粉丝 粉丝团 李宇春野蛮 野蛮生长 李宇春恭喜 李宇春李宇春 李宇春粉丝团 歌手乐星 生长李宇春 生长追星 粉丝李宇春 追星李宇春 追星现场 大数影响力 头条明星 明星大数 生长实至名归 影响力歌手 现场粉丝 恭喜获得 获得头条 实力说话 一直实力
__label__新闻内容 红衣 更新 游戏 上海 虹桥 低头 过年 机场 出发 黄子 直通车 新闻组 装扮 头条 今日 打包 图片 网页 黄子韬 链接 上海虹桥 低头游戏 出发新闻组 头条更新 打包网页 新闻组直通车 更新上海 机场出发 游戏红衣 直通车黄子 红衣装扮 网页链接 虹桥机场 过年低头 黄子过年 黄子韬图片

处理后文本测试集

__label__新闻内容 黄子 飞虎 铁道 视频 下载 网页 成龙 电影 在线 票房 上映 链接 拍摄 花絮 更新 今日 头条 相关 KNewdayL mp ## 黄子韬 铁道飞虎 网页链接 下载网页 花絮在线 链接下载 链接视频 飞虎上映 飞虎视频 飞虎黄子 黄子拍摄 黄子铁道 黄子韬黄子 在线铁道 头条更新 上映票房 相关花絮 电影铁道 拍摄相关 更新电影
__label__新闻内容 少年 锦衣卫 宣传片 网页 期待 冬天 梦想 共同 青春 燃爆 热血 镜头 震撼 宣传 链接 来袭 优酷 看看 今日 全网 搜狐 上映 PPTV 奇艺 转发 微博 少年锦衣卫 锦衣卫宣传片 网页链接 优酷少年 全网上映 奇艺少年 宣传片奇艺 宣传片少年 宣传片来袭 宣传片看看 宣传片网页 链接搜狐 锦衣卫全网 搜狐少年 锦衣卫少年 锦衣卫宣传 震撼镜头 梦想青春 转发微博 期待少年 看看少年 宣传网页 共同期待 微博少年

训练模型 6个分类 100个测试集 精确度0.87 召回率0.87

admindeiMac:fastText admin$ ./fasttext supervised -input weibo1.txt -output weibo  -lr 1.0 -epoch 35 -wordNgrams 2 -bucket 200000 -dim 50 -loss hs
Read 0M words
Number of words:  23490
Number of labels: 6
Progress: 100.0%  words/sec/thread: 2651258  lr: 0.000000  loss: 0.023896  eta: 0h0m 
admindeiMac:fastText admin$ ./fasttext test weibo.bin weibo2.txt
N    100
P@1    0.87
R@1    0.87
```

新闻内容（训练集 794 条、测试集 54 条，随机样本）

```
处理后文本训练集

__label__娱乐新闻 刷屏 头条 先生 李敏镐 今日 头条刷屏 李敏镐先生
__label__新闻内容 娱乐 郭德纲 以下 郑爽 最喜欢 关键词 喜欢 李宇春 周冬雨 姚晨 唐嫣 周星驰 王凯 举行 侯鸿亮 北京 娱乐圈 下午 人气 明星 今天 盛典 行业 代表 到场 年度 指数 显示 今日 用户 最喜欢关键词 搜索郑爽 中青年最喜欢 侯鸿亮娱乐圈 关键词郑爽 关键词郭德纲 周冬雨唐嫣 周星驰姚晨 唐嫣王凯 喜欢郭德纲 姚晨李宇春 娱乐圈人气 李宇春周冬雨 王凯侯鸿亮 用户最喜欢 赵丽颖杨幂 赵本山赵薇 郑爽赵丽颖 郭德纲恒星 郭德纲赵本山 人气明星 喜欢搜索 娱乐盛典 恒星年度 盛典北京 代表到场 以下喜欢 明星娱乐 数据以下 年度娱乐
__label__科技新闻 中国 经济 声音 今日 财经 直播 快讯 央视网 新闻 报道 @cctv- 加油 启动 正式 调查 资讯 一直 斗鱼 @YYLIVE@ 央视 小米 第一时间 看点 @CCTV 中国网 态度 小时 观众 生活 传播 一直快讯 中国声音 中国故事 中国经济 中国观众 中国财经 中国网态度 传播中国 加油中国 周刊财经网 国家周刊 声音听得 央视财经 央视网小米 头条视频 小时看点 小米直播 快讯资讯 报道经济 数据传播 数据讲述 斗鱼直播 新闻央视网 正式启动 生活调查 直播一直 直播斗鱼 看点第一时间 第一时间经济 经济小时

处理后文本测试集

__label__新闻内容 马伊琍 OL 新剧 儿子 前半生 一家 接驾 养眼 靳东开 今日 街头 头条 靳东我 ## 靳东 演员 一家养眼 儿子街头 养眼头条 前半生马伊琍 头条马伊琍 接驾一家 新剧儿子 演员靳东 街头靳东开 靳东演员 靳东靳东我 靳东开接驾 靳东我前半生 马伊琍新剧
__label__新闻内容 专辑 张艺兴 今年 功夫 瑜伽 地址 欢迎 得到 losecontrol# 国际上 希望 是不是 好成绩 凤凰 单上也 国外 新闻 受众 努力 粉丝 单曲 直播 采访 之前 演唱会 计划 大概 上面 音乐 春晚 努力努力 功夫瑜伽 张艺兴功夫 专辑二胎 之前单曲 二胎可不 单上也好成绩 单曲国外 国外单上也 国际上得到 地址张艺兴 希望国际上 张艺兴凤凰 张艺兴张艺兴 春晚努力 瑜伽张艺兴 瑜伽春晚 粉丝受众 歌曲粉丝 专辑大概 专辑上面 努力视频 直播采访 音乐专辑 是不是希望 担心歌曲 凤凰新闻 新闻直播 可不中国 上面计划

训练模型 19个分类 51个测试集 精确度0.882 召回率0.882

admindeiMac:fastText admin$ ./fasttext supervised -input weibo1_1.txt -output weibo  -lr 1.0 -epoch 35 -wordNgrams 2 -bucket 200000 -dim 50 -loss hs
Read 0M words
Number of words:  16006
Number of labels: 19
Progress: 100.0%  words/sec/thread: 2629780  lr: 0.000000  loss: 0.096460  eta: 0h0m 
admindeiMac:fastText admin$ ./fasttext test weibo.bin weibo2_1.txt
N    51
P@1    0.882
R@1    0.882
Number of examples: 51
```

营销内容（训练集 442 条、测试集 27 条，随机样本）

```
处理后文本训练集

__label__活动推广 视频 数字 生长 获奖 歌手 专辑 星闻 野蛮 揭秘 影响力 销量 今日 突破 李宇春 厉害 专辑销量 影响力歌手 揭秘视频 数字专辑 星闻揭秘 李宇春影响力 歌手获奖 生长数字 获奖视频 视频星闻 野蛮生长 销量突破
__label__品牌推广 投票 不要 头条 今日 鹿晗 方法 重要 金扫帚 APP 下载 特别 偷懒 时间 一起 找到 下去 真的 链接 投到 下面 已经 攻破 阿鹿 所示 进入 搜索 cqs app 公投 通道 一起下去 下去投票 下载头条 下面方法 不要偷懒 不要鹿晗 偷懒时间 原博方法 头条搜索 头条金扫帚 少女祭司 已经投到 所示找到 找到投票 投票不要 投票通道 投票链接 投票阿鹿 搜索所示 方法进入 时间一起 特别重要 真的特别 祭司鹿晗 进入金扫帚 通道攻破 重要不要 金扫帚公投 金扫帚投票 链接下面

处理后文本测试集

__label__品牌推广 重温 记忆 民俗 年味 新浪 净角 著名 艺术家 嘉宾 大师 农历 曲艺 郭德纲 故事 张静 新闻 迎春 真实 口述 恭祝 季合集 呈现 新年 视频 农历民俗 净角艺术家 大师著名 年味季合集 年味民俗 年味记忆 张静郭德纲 新浪新闻 新浪视频 新闻故事 曲艺大师 民俗重温 真实口述 著名净角 视频年味 记忆张静 记忆新浪 迎春呈现 重温农历 重温记忆
__label__品牌推广 杨洋 知道 未来 搜狐 世界 ## 新闻 icon 发生 御膳房 正在 看见 都要 小太阳 手写 正能量 视频 #L 二爷 杨洋御膳房 搜狐新闻 御膳房杨洋 新闻知道 二爷杨洋 小太阳杨洋 御膳房二爷 手写杨洋 杨洋手写 杨洋搜狐 杨洋杨洋 杨洋正能量 杨洋看见 正能量小太阳 知道杨洋 都要知道 知道搜狐 知道视频 看见正在 正在发生 知道世界

训练模型 4个分类 27个测试集 精确度0.926 召回率0.926

admindeiMac:fastText admin$ ./fasttext supervised -input weibo1_2.txt -output weibo  -lr 1.0 -epoch 35 -wordNgrams 2 -bucket 200000 -dim 50 -loss hs
Read 0M words
Number of words:  5367
Number of labels: 4
Progress: 100.0%  words/sec/thread: 3499101  lr: 0.000000  loss: 0.092749  eta: 0h0m -14m 
admindeiMac:fastText admin$ ./fasttext test weibo.bin weibo2_2.txt
N    27
P@1    0.926
R@1    0.926
Number of examples: 27
```

用户主动提及（训练集 94 条、测试集 10 条，随机样本）

```
处理后文本训练集

__label__用户评价 资讯 搞定 事儿 不知 设计公司 形状 知名度 低成本 模仿 金融 借势 链链 炒作 不知设计公司 事儿资讯 低成本借势 借势炒作 知名度低成本 设计公司事儿 资讯知名度 金融模仿 链链金融
__label__其他 凤凰 记者站 全球 卫视 发言 凤凰网 资讯 网络 全媒体 凤凰在 介绍 简要 传播 先后 邹明亦 总编辑 勉励 负责人 肯定 做好 准备 副总裁 陈彤 迎接 总裁 一同 难以 重大 工作 总结 凤凰卫视 全球记者站 介绍凤凰在 全媒体网络 凤凰在全媒体 凤凰网副总裁 凤凰网资讯 刘爽凤凰 副总裁总编辑 卫视凤凰网 总编辑邹明亦 总裁陈彤 眼睛看世界 董事长刘爽 邹明亦先后 陈彤凤凰网 香港特别行政区大埔 凤凰花絮 花絮凤凰 勉励记者站 资讯总裁 卫视全球 资讯董事长 简要介绍 记者站主管 记者站负责人 传播营运 凤凰全球 华人凤凰 一同迎接
__label__平台入口 投票 告急 目前 网友 年度 金扫帚 追缉 APP 祭司 少女 选项 奇艺 鹿晗 票数 必须 清零 芦苇 今天 窗口 决出 总结 主攻 辛苦 微信 组委会 无论如何 加油 情况 一起 高危 网友投票 链接奇艺 投票网页 追缉年度 金扫帚投票 网页链接 下载头条 保下你鹿懂 加油告急 奇艺必须 奇艺投票 少女祭司 清零票数 环节保下你 票数告急 祭司鹿晗 选项清零 首轮网友 鹿晗芦苇 无论如何网友 高危主攻 投票下载 组委会决出 快报投票 投票快报 总结高危 主攻辛苦 投票名额 决出最后 名额明星

处理后文本测试集

__label__平台入口 田馥 网页 星球 链接 粉丝 Hebe 官方 微博 阿里 协助 进化 幸运 谦卑 图片 面对 新闻 聚焦 争取 贪玩 谢谢 @Tilda 资源 @Peg @summerwith 提供 歌迷 ＵＣ 超人 热点 福利 网页链接 阿里星球 田馥幸运 面对进化 田馥官方 官方微博 新闻田馥 粉丝聚焦 进化东方 星球粉丝 幸运谦卑 东方田馥 全靠歌迷 北京时间田馥 协助轮博 娱乐田馥 执行田馥 提供贪玩 新闻网易 星球田馥甄 田馥甄粉丝 看点网页 粉丝文案 网易新闻 腾讯新闻 账号田馥 贪玩谢谢 资源田馥 轮博查看 进化北京时间
__label__平台入口 快报 投票 鹿晗 不要 社会主义 网页 链接 Repost 快报投票 社会主义鹿晗 网页链接 鹿晗快报

训练模型 3个分类 10个测试集 精确度1 召回率1

admindeiMac:fastText admin$ ./fasttext supervised -input weibo1_3.txt -output weibo  -lr 1.0 -epoch 35 -wordNgrams 2 -bucket 200000 -dim 50 -loss hs
Read 0M words
Number of words:  2352
Number of labels: 3
Progress: 100.0%  words/sec/thread: 4062170  lr: 0.000000  loss: 0.695950  eta: 0h0m -14m 
admindeiMac:fastText admin$ ./fasttext test weibo.bin weibo2_3.txt
N    10
P@1    1
R@1    1
Number of examples: 10
```

求助上访（训练集 111 条、测试集 5 条，随机样本）

```
处理后文本训练集

 __label__投诉声明 东营 央视 新闻 今日 山东 发布 持续 以后 声明 本博 之下 阳光 暴露 终会 嘴脸 图片 丑恶 利诱 威逼 呵斥 恐吓 官官相护 剥夺 打拼 判决 事实 冤假错案 制造 葫芦案 葫芦 丑恶嘴脸 东营中院 东营创业 东营司法 东营国家 东营烧烤 东营市检察院 中国私法 中国政府中国 中院中国政府 书记在线 事实判决 人民检察院生活 以后持续 关注最高人民检察院 利诱丑恶 制造冤假错案 发布图片 司法东营 呵斥威逼 嘴脸终会 国家廉政 图片视频 在线山东 声明以后 央视关注 央视新闻 头条经济 威逼利诱 孙文亮葫芦
 __label__投诉声明 东营 央视 新闻 今日 山东 发布 持续 以后 声明 本博 之下 阳光 暴露 终会 嘴脸 图片 丑恶 利诱 威逼 呵斥 恐吓 官官相护 剥夺 打拼 判决 事实 冤假错案 制造 葫芦案 葫芦 丑恶嘴脸 东营中院 东营创业 东营司法 东营国家 东营烧烤 东营市检察院 中国私法 中国政府中国 中院中国政府 书记在线 事实判决 人民检察院生活 以后持续 关注最高人民检察院 利诱丑恶 制造冤假错案 发布图片 司法东营 呵斥威逼 嘴脸终会 国家廉政 图片视频 在线山东 声明以后 央视关注 央视新闻 头条经济 威逼利诱 孙文亮葫芦

处理后文本测试集

 __label__投诉声明 更多 宝宝 房子 关注 央视网 新闻 新京报 北京 晚报 民声 信报 媒体 报道 公正 网页 详情 链接 公平 追求 时报 发布 中国 社会 和谐 记者 周刊 覃丽慧 罗昌平 京华 黑心 新京报央视网 宝宝房子 信报报道 关注民声 发布新京报 央视网京华 央视网宝宝 开发商蒙骗 晚报更多 更多关注 更多媒体 网页链接 罗昌平新京报 胡蓉腾讯 腾讯新闻 腾讯胡蓉 覃丽慧罗昌平 记者覃丽慧 链接宝宝 黑心开发商 详情网页 京华时报 报道详情 房子和谐 公平公正 追求公平 新闻周刊 媒体关注 和谐社会 北京晚报
 __label__求助上访 律师 网易 新闻 启蒙 意义 朱孝顶 分享 偶像 追星 强拆 都有 求助 私信 案子 孝顶 支持 客户端 网易新闻 律师偶像 追星强拆 强拆私信 私信求助 个案都有 偶像网易 偶像追星 分享网易 客户端网易 支持孝顶 新闻客户端 新闻追星 朱孝顶律师 都有启蒙 求助律师 律师分享 启蒙意义

训练模型 5个分类 5个测试集 精确度0.8 召回率0.8

admindeiMac:fastText admin$ ./fasttext supervised -input weibo1_4.txt -output weibo  -lr 1.0 -epoch 35 -wordNgrams 2 -bucket 200000 -dim 50 -loss hs
Read 0M words
Number of words:  1362
Number of labels: 5
Progress: 100.0%  words/sec/thread: 3991056  lr: 0.000000  loss: 0.571666  eta: 0h0m -14m 
admindeiMac:fastText admin$ ./fasttext test weibo.bin weibo2_4.txt
N    5
P@1    0.8
R@1    0.8
Number of examples: 5
```

行业文章（训练集 794 条、测试集 54 条，随机样本）

```
处理后文本训练集

__label__行业文章 中年 身世 土家 微博 腾讯 新闻 轉發 土家身世 微博土家 腾讯新闻 身世腾讯 轉發微博
__label__行业文章 东风 新闻 媒体 应有 获得 美国 中国 服役 快递 使命 大国 实力 尊严 铸就 点兵 尊重 书房 腾讯 聊聊 今晚 客户端 东风快递 东风服役 中国美国 书房点兵 大国尊严 媒体东风 实力铸就 尊重媒体 应有尊重 新闻媒体 新闻客户端 服役中国 点兵东风 美国获得 腾讯新闻 获得应有

处理后文本测试集

__label__行业文章 今日 头条 微博 内容 三巨头 何去何从 文章 秘书 发布 何去 内容三巨头 微博头条 三巨头何去 三巨头何去何从 发布头条 头条文章 头条微博 微博秘书 秘书微博 文章微博
__label__行业文章 收视 卫视 湖南 电视剧 市场 事儿 不俗 起来 表现 几部 细看 李慧珍 走高 黄金档 全天 连夺 宝座 头条 平台 漂亮 今日 分享 连续 全天收视 几部收视 分享头条 卫视电视剧 卫视连夺 卫视连续 收视不俗 收视表现 李慧珍收视 湖南卫视 漂亮李慧珍 电视剧市场 细看起来 连夺宝座 黄金档平台

训练模型 2个分类 4个测试集 精确度1 召回率1

admindeiMac:fastText admin$ ./fasttext supervised -input weibo1_5.txt -output weibo  -lr 1.0 -epoch 35 -wordNgrams 2 -bucket 200000 -dim 50 -loss hs
Read 0M words
Number of words:  934
Number of labels: 2
Progress: 100.0%  words/sec/thread: 3274850  lr: 0.000000  loss: 0.655315  eta: 0h0m -14m 
admindeiMac:fastText admin$ ./fasttext test weibo.bin weibo2_5.txt
N    4
P@1    1
R@1    1
Number of examples: 4
```

其他（训练集 794 条、测试集 54 条，随机样本）

```
处理后文本训练集

__label__搞笑段子 签名 喜欢 特朗普 心电图 据说 笔迹 具有 专家 非常 看出 事情 看待 妥协 觉得 地震 记录 攻击性 曝光 资讯 特朗普签名 据说特朗普 签名特朗普 签名曝光 签名看出 资讯据说 心电图地震 笔迹专家 具有攻击性 喜欢妥协 喜欢看待 看待事情 看出非常 地震记录 非常具有
__label__官方声明 QQ 官方 开通 账号 网友 答复 政府部门 冒用 上当受骗 仔细 客户端 陕西省 资讯 头条 今日 辨别 APP 新闻 公众 微信 微博 教育厅 混淆视听 四大 新浪 新华 人民 腾讯 公开 领域 严正声明 人民新华 从未开通 仔细辨别 倾听回应 公开账号 冒用政府部门 咨询是否 四大官方 回应辟谣 声明从未 官方微博 官方账号 开通公开 开通官方 开通新浪 微信公众 政府部门答复 教育厅官方 新华四大 新媒体领域 新浪腾讯 新闻客户端 是否开通 混淆视听行径 热心网友 网友仔细 网友咨询 腾讯人民 行径表示
__label__其他 组合 STS 转发 网页 支持 链接 谢谢 支持转发 网页链接 谢谢支持 转发组合

处理后文本测试集

__label__鸡汤 新闻 客户端 凤凰 归途 路上 腊月 回家 凤凰新闻 回家路上 客户端归途 新闻客户端
__label__其他 夏莉 显示 jbdxbl 新闻 网易 湖畔 @NoPingWest 观星 @-KoNoSuBang- _URaRa 缺德 手机 -Queen 型号 部落 大象 Twitter 劲爆 SnHine 劲爆大象 夏莉湖畔 大象部落 手机型号 新闻显示 显示手机 湖畔观星 缺德网易 网易新闻 观星缺德 部落夏莉

训练模型 6个分类 2个测试集 精确度1 召回率1

admindeiMac:fastText admin$ ./fasttext supervised -input weibo1_6.txt -output weibo  -lr 1.0 -epoch 35 -wordNgrams 2 -bucket 200000 -dim 50 -loss hs
Read 0M words
Number of words:  292
Number of labels: 6
Progress: 100.0%  words/sec/thread: 2805315  lr: 0.000000  loss: 0.000000  eta: 0h0m 
admindeiMac:fastText admin$ ./fasttext test weibo.bin weibo2_6.txt
N    2
P@1    1
R@1    1
Number of examples: 2
```

**FastText**

`FastText` 是 `facebook` 开源的一个词向量计算以及文本分类工具，该工具的理论基础是以下两篇论文：

[Enriching Word Vectors with Subword Information](https://arxiv.org/pdf/1607.04606v1.pdf)

[Bag of Tricks for Efficient Text Classification](https://arxiv.org/pdf/1607.01759v2.pdf)

[FastText 源码分析](https://heleifz.github.io/14732610572844.html)

这篇论文提出了用` word n-gram` 的向量之和来代替简单的词向量的方法，以解决简单 `word2vec` 无法处理同一词的不同形态的问题。

使用到的相关参数相关参数

```
-lr 学习率 –epoch 迭代次数 -wordNgrams -bucket –neg 负采样 –loss loss function {ns,hs, softmax}  ---dim 词向量维度 –ws 窗口大小
```

**Hanlp**

获取文本关键字及短语提取，使用到以下两个算法：

[《TextRank算法自动摘要的Java实现》](http://www.hankcs.com/nlp/textrank-algorithm-java-implementation-of-automatic-abstract.html)

[《基于互信息和左右信息熵的短语提取识别》](http://www.hankcs.com/nlp/extraction-and-identification-of-mutual-information-about-the-phrase-based-on-information-entropy.html)

