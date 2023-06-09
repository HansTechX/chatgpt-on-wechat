## 插件描述
一个能让chatgpt联网，搜索，数字运算的插件，将赋予强大且丰富的扩展能力  
使用该插件需在机器人回复你的前提下，在对话内容前加$tool；仅输入$tool将返回tool插件帮助信息，用于测试插件是否加载成功  
### 本插件所有工具同步存放至专用仓库：[chatgpt-tool-hub](https://github.com/goldfishh/chatgpt-tool-hub)


## 使用说明
使用该插件后将默认使用4个工具, 无需额外配置长期生效：
### 1. python
###### python解释器，使用它来解释执行python指令，可以配合你想要chatgpt生成的代码输出结果或执行事务

### 2. 访问网页的工具汇总(默认url-get)

#### 2.1 url-get
###### 往往用来获取某个网站具体内容，结果可能会被反爬策略影响

#### 2.2 browser
###### 浏览器，功能与2.1类似，但能更好模拟，不会被识别为爬虫影响获取网站内容

> 注1：url-get默认配置、browser需额外配置，browser依赖google-chrome，你需要提前安装好

> 注2：browser默认使用summary tool 分段总结长文本信息，tokens可能会大量消耗！

这是debian端安装google-chrome教程，其他系统请执行查找
> https://www.linuxjournal.com/content/how-can-you-install-google-browser-debian

### 3. terminal
###### 在你运行的电脑里执行shell命令，可以配合你想要chatgpt生成的代码使用，给予自然语言控制手段

> terminal调优记录：https://github.com/zhayujie/chatgpt-on-wechat/issues/776#issue-1659347640

### 4. meteo-weather
###### 回答你有关天气的询问, 需要获取时间、地点上下文信息，本工具使用了[meteo open api](https://open-meteo.com/)
注：该工具需要较高的对话技巧，不保证你问的任何问题均能得到满意的回复

> meteo调优记录：https://github.com/zhayujie/chatgpt-on-wechat/issues/776#issuecomment-1500771334

## 使用本插件对话（prompt）技巧
### 1. 有指引的询问
#### 例如：
- 总结这个链接的内容 https://github.com/goldfishh/chatgpt-tool-hub
- 使用Terminal执行curl cip.cc
- 使用python查询今天日期

### 2. 使用搜索引擎工具
- 如果有搜索工具就能让chatgpt获取到你的未传达清楚的上下文信息，比如chatgpt不知道你的地理位置，现在时间等，所以无法查询到天气

## 其他工具

### 5. wikipedia
###### 可以回答你想要知道确切的人事物

### 6. 新闻类工具

#### 6.1. news-api *
###### 从全球 80,000 多个信息源中获取当前和历史新闻文章

#### 6.2. morning-news *
###### 每日60秒早报，每天凌晨一点更新，本工具使用了[alapi-每日60秒早报](https://alapi.cn/api/view/93)

> 该tool每天返回内容相同

#### 6.3. finance-news
###### 获取实时的金融财政新闻

> 该工具需要解决browser tool 的google-chrome依赖安装

### 7. bing-search *
###### bing搜索引擎，从此你不用再烦恼搜索要用哪些关键词

### 8. wolfram-alpha *
###### 知识搜索引擎、科学问答系统，常用于专业学科计算

### 9. google-search *
###### google搜索引擎，申请流程较bing-search繁琐


### 10. arxiv(dev 开发中)
###### 用于查找论文


### 11. debug(dev 开发中，目前没有接入wechat)
###### 当bot遇到无法确定的信息时，将会向你寻求帮助的工具


### 12. summary
###### 总结工具，该工具必须输入一个本地文件的绝对路径

> 该工具目前是和其他工具配合使用，暂未测试单独使用效果


### 13. image2text
###### 将图片转换成文字，底层调用imageCaption模型，该工具必须输入一个本地文件的绝对路径


### 14. searxng-search *
###### 一个私有化的搜索引擎工具

> 安装教程：https://docs.searxng.org/admin/installation.html

---

###### 注1：带*工具需要获取api-key才能使用(在config.json内的kwargs添加项)，部分工具需要外网支持  
#### [申请方法](https://github.com/goldfishh/chatgpt-tool-hub/blob/master/docs/apply_optional_tool.md)

## config.json 配置说明
###### 默认工具无需配置，其它工具需手动配置，一个例子：
```json
{
  "tools": ["wikipedia", "你想要添加的其他工具"],  // 填入你想用到的额外工具名
  "kwargs": {
      "debug": true, // 当你遇到问题求助时，需要配置
      "request_timeout": 120,  // openai接口超时时间
      "no_default": false,  // 是否不使用默认的4个工具
      // 带*工具需要申请api-key，在这里填入，api_name参考前述`申请方法`
  }
}

```
注：config.json文件非必须，未创建仍可使用本tool；带*工具需在kwargs填入对应api-key键值对  
- `tools`：本插件初始化时加载的工具, 目前可选集：["wikipedia", "wolfram-alpha", "bing-search", "google-search", "news"] & 默认工具，除wikipedia工具之外均需要申请api-key
- `kwargs`：工具执行时的配置，一般在这里存放**api-key**，或环境配置
  - `debug`: 输出chatgpt-tool-hub额外信息用于调试
  - `request_timeout`: 访问openai接口的超时时间，默认与wechat-on-chatgpt配置一致，可单独配置
  - `no_default`: 用于配置默认加载4个工具的行为，如果为true则仅使用tools列表工具，不加载默认工具
  - `top_k_results`: 控制所有有关搜索的工具返回条目数，数字越高则参考信息越多，但无用信息可能干扰判断，该值一般为2
  - `model_name`: 用于控制tool插件底层使用的llm模型，目前暂未测试3.5以外的模型，一般保持默认

---

## 备注
- 强烈建议申请搜索工具搭配使用，推荐bing-search
- 虽然我会有意加入一些限制，但请不要使用本插件做危害他人的事情，请提前了解清楚某些内容是否会违反相关规定，建议提前做好过滤
- 如有本插件问题，请将debug设置为true无上下文重新问一遍，如仍有问题请访问[chatgpt-tool-hub](https://github.com/goldfishh/chatgpt-tool-hub)建个issue，将日志贴进去，我无法处理不能复现的问题
- 欢迎 star & 宣传，有能力请提pr

