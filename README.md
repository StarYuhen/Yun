# 真 · 云运动 一键跑步脚本
# 注意！已经有同学遇到被检测出软件作弊而导致健跑全部不合格的情况！请谨慎使用该脚本。

## 写在前面

#### 取消更新

已停止维护，取消更新，原因:

有人准备那开源作品拿去卖跑步，帮人代跑，没理由浪费自己的时间来维护。


## 仓库介绍

对 https://github.com/kontori/yun 进行更新兼容

支持云运动3.0.0 版本。

旧版本请看原仓库，本仓库不支持旧版本

    Data = {
        "cipherKey": "BIi2FjS6uXwMu2QvHwAT6JlAh+p8fxFEJTQl8Olbz+CIBr6XBiUeNlwgUIsD/87Bpd+b+Db3sAnYajArr2P62nXj/3eZGvWjji1z08nthQlfEd/6obyLwFAk9UGz2iVvq0/EsfRIHfELI3+zi1SCercirujBAWELxQ==",
        "content": sm4_encrypt("1c233a33887619d7f98b405cf1d1ae6f", data)
    }

他是请求格式，cipherKey和SM4已固定在脚本内，如果觉得固定值有问题，欢迎修改。


如果学校的接口版本老旧，可以使用上面指引的代仓库链接


若是最后 发送结束信号...后报错，则代表请求过于频繁，可以尝试设置一下时长


## 疑难杂症解决办法及更新

在[issues5](https://github.com/StarYuhen/Yun/issues/5)
第1次尝试... .. 第100次尝试 类型的多次尝试问题，该问题由打卡点位引起。

main.py 选择第88行，其中的   # print("data：", data)，将数据打印

"points":"118.361148,31.284591|118.360042,31.283665|118.359749,31.282562|118.362264,31.281532|118.362945,31.282576|118.362268"

字段随机取一个，如  118.361148,31.284591  写入config.ini的point值解决该问题

-- --

在 [issues3](https://github.com/StarYuhen/Yun/issues/3) 
修复打卡点问题报错

-- --

在[issues1](https://github.com/StarYuhen/Yun/issues/1)
修复  {"msg":"服务异常，请稍后重试！","code":500}

-- --
### 免责声明

一切代码仅供研究学习，请勿用于非法用途。
使用中造成的一切后果，本人概不负责。

### 一些规则

#### 打卡点的选取规则

绝大部分的数据来自于服务器的返回值，包括打卡点。以合工大为例，服务器将返回一串打卡点，而程序将从中随机选取`raDislikes`个作为本次跑步所有的打卡点。

其中部分打卡点由于太靠近学校边缘，有时会导航到校外（例如合工大的 117.209175,31.774432 打卡点），需要手动去除。点位需要抓包获取，确保完全一致。
> 更新：
> 由于云运动增加了打卡点，并且都非常靠近学校边缘，去除打卡点的方式不再方便（但仍将保留），现引入`allow_overflow_distance`参数，如果总公里数偏移量超出这一限制，将尝试重新规划路线。

#### 路线的规划规则

成功获取一定数量的打卡点后，依次将打卡点置入任务列表，同时计算路径距离、消耗时间，若总距离在置入`raMinDislikes`个点位之前已达到了用户配置的`min_distance`，之后的打卡点将不作处理；反之，继续放置点位，若超出`raDislikes`个，则从头开始放置，即“回跑”。

由于高德地图路径规划的精度有限，致使导航终点与实际打卡点存在偏差，可能出现”瞬移“。

## 使用方法

### ~~1. 给项目star~~

### 2. 获取高德地图开发者密钥

脚本使用高德地图API规划跑步路径，故需要获取开发者密钥。有能力的可以自己尝试更精确的路径规划哦

登录 https://console.amap.com/dev/key/app 

![image](https://github.com/kontori/images/raw/main/yun-1.png)

点击右上角的创建新应用，应用名称随便，应用类型选择出行。

创建完毕后，点击应用上方的添加按钮，

<img src="https://github.com/kontori/images/raw/main/yun-2.png" alt="" width="450">

Key名称随便，服务平台选择**Web服务**，IP白名单留空即可。你将会得到一个Key，这就是我们的开发者密钥。**（不是安全密钥！）**

### 3. 将项目下载到本地

### 4. 配置

你需提前在config.ini文件中完成一些简单的配置。**不要随意删除配置字段。**

```
[Yun]
# 云运动服务器的完整Url，一般来说不需要更改
yun_host = https://sports.yzhiee.com:8085
# 学校服务器的完整url，例如合工大的为http://210.45.246.53:8080，请一定不要遗漏 http:// 
# 如选择在初次启动时登录，会自动获取该地址，可留空
# 注：有些学校服务器的ip为内网ip，可能需要使用校园网
school_host = http://210.45.246.53:8080
# 云运动的加密密钥
key = YUNZHIEE
# 云运动app版本
app_edition = 3.0.0

[User]
# 用户token，可通过抓包获取。如果你不知道怎么获得，请留空，运行时会引导登录
token = 
# 设备id，可通过抓包获取(每次登录都是不同的设备id，我怀疑它就是个随机数，不要尝试用抓包以外的方法获取)，
# 可留空，运行时会自动生成15位随机数
device_id = 
# 高德地图开发者密钥
map_key = 
# 设备名称
device_name = Xiaomi(Mi10)
# 安卓系统版本（大版本号）ios用户自行抓包分析哦
sys_edition = 12

[Run]
# 初始位置经纬度，精确到小数点后6位，可修改
point = 117.205608,31.772887
# 去除指定打卡点，用于解决路径异常的问题。但注意，即使删除失败，程序仍将继续。数组类型
exclude_points = ["117.209175,31.774432"]
# 最少公里数，float
min_distance = 2
# 允许超出公里数，float
allow_overflow_distance = 0.1
# 以下两个配置只会影响单点配速，即决定路线是绿色、黄色还是红色
# 具体范围见云运动公告
# 最小配速偏移量，float
single_mileage_min_offset = 1
# 最大配速偏移量，float
single_mileage_max_offset = -1
# 最小步频偏移量，int
cadence_min_offset = 60
# 最大步频偏移量，int
cadence_max_offset = -100
# 单次发送split_point个数，如果你不知道这个是什么，就不要修改
split_count = 10
# 最终配速最小值
min_consume = 4.50
# 最终配速最大值
max_consume = 5.50
```

### 5. 运行

测试环境：Python 3.9.5

#### 安装依赖

```
pip install -r requirements.txt
```

#### run

确保config.ini文件与main.py处于同级目录，然后

```
python main.py
```

按照提示操作即可。

### 6. 退出登录

每次运行、任务执行结束时，可选择退出登录，以避免系统检测出多设备登录。若Token为抓包获取，确保与真机一致时，一般来说，可无需退出。
