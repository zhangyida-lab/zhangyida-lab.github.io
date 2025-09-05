---
title: RocketMQ应用案例
date: 2025-09-05 16:06:35
tags: [微服务]
---




RocketMQ 在实战中用得非常广，尤其是电商、金融、物联网等对高并发和可靠消息要求比较高的场景。给你整理几个 **典型实战案例**，你可以挑选适合你项目的去研究和复现：



### 🚀 1. 电商系统：订单创建与支付异步解耦


- **场景**：用户下单 → 订单服务写入数据库 → 同时需要扣库存、发短信、记录积分。
- **做法**：


订单服务只负责写库并发送一条消息到 `order-topic`。


库存服务、短信服务、积分服务分别订阅该消息，各自执行逻辑。
- **好处**：


业务解耦，避免订单服务过重。


新增下游业务（比如推荐服务）时，只需订阅消息，不影响订单服务。


### 📦 2. 秒杀系统：削峰填谷


- **场景**：双 11 秒杀活动，瞬间高并发请求。
- **做法**：


用户请求到达后，先写入 RocketMQ 的 `seckill-topic`，快速返回请求已受理。


后端消费服务异步从队列拉取消息，按队列顺序逐步扣减库存、生成订单。
- **好处**：


保护数据库，防止被瞬间流量打垮。


支持流控与失败重试。


### 💳 3. 金融支付系统：分布式事务（事务消息）


- **场景**：转账 A → B，需要保证扣款和加款的一致性。
- **做法**：


使用 RocketMQ 的 **事务消息**机制。


扣款服务先发一条半消息（prepare），执行本地事务（扣款）。


如果成功 → commit 消息，下游消费（加款服务）才会执行。


如果失败 → rollback 消息，消息丢弃。
- **好处**：


保证跨系统操作的最终一致性。


### 📊 4. 日志收集与大数据分析


- **场景**：系统埋点日志需要收集到大数据平台分析用户行为。
- **做法**：


各个应用通过 RocketMQ producer 把日志写到 `log-topic`。


消费端是 Flink、Spark 或者 Hadoop 程序，实时/批量消费分析。
- **好处**：


日志解耦，不直接依赖数据库写入。


能支撑亿级日志吞吐。


### 📡 5. IoT（物联网设备消息采集）


- **场景**：数百万 IoT 设备上传数据（传感器数据、状态）。
- **做法**：


每个设备数据写入 RocketMQ 的 `iot-topic`。


消费端可以有：实时告警服务、数据清洗服务、存储服务（写入时序数据库）。
- **好处**：


高并发、高可靠。


一份数据可以同时供多个下游服务使用。






##  RocketMQ 现在有官方的 Python 客户端（`rocketmq-client-python`），适合做快速实战。
我给你设计一个 **电商订单系统解耦的 Python 实战示例**，简单演示：



## ⚙️ 场景说明


- 用户下单 → 订单服务写入数据库 → 发送消息到 RocketMQ。
- 库存服务、短信服务订阅消息，分别处理。


## 1️⃣ 安装依赖


```bash
pip install rocketmq-client-python

```


## 2️⃣ Producer：订单服务


```python
from rocketmq.client import Producer, Message
import time

def create_order(order_id, user_id, product_id):
    # 模拟写入数据库
    print(f"订单已创建: order_id={order_id}, user_id={user_id}, product_id={product_id}")

    # 发送消息到 RocketMQ
    msg = Message("order-topic")
    msg.set_keys(order_id)
    msg.set_tags("create")
    msg.set_body(f"{order_id},{user_id},{product_id}")
    ret = producer.send_sync(msg)
    print(f"消息发送结果: {ret.status}, msg_id: {ret.msg_id}")

if __name__ == "__main__":
    producer = Producer("order-producer-group")
    producer.set_name_server_address("127.0.0.1:9876")  # RocketMQ namesrv 地址
    producer.start()

    for i in range(1, 6):
        create_order(f"order_{i}", f"user_{i}", f"product_{i}")
        time.sleep(1)

    producer.shutdown()

```


## 3️⃣ Consumer：库存服务


```python
from rocketmq.client import PushConsumer

def callback(msg):
    order_id, user_id, product_id = msg.body.decode("utf-8").split(",")
    print(f"[库存服务] 收到订单: {order_id}, 商品 {product_id} 扣减库存")
    return

if __name__ == "__main__":
    consumer = PushConsumer("stock-consumer-group")
    consumer.set_name_server_address("127.0.0.1:9876")
    consumer.subscribe("order-topic", callback)
    consumer.start()

    print("库存服务已启动，等待消息...")
    try:
        while True:
            pass
    except KeyboardInterrupt:
        consumer.shutdown()

```


## 4️⃣ Consumer：短信服务


```python
from rocketmq.client import PushConsumer

def callback(msg):
    order_id, user_id, product_id = msg.body.decode("utf-8").split(",")
    print(f"[短信服务] 给用户 {user_id} 发送下单成功短信: 订单 {order_id}")
    return

if __name__ == "__main__":
    consumer = PushConsumer("sms-consumer-group")
    consumer.set_name_server_address("127.0.0.1:9876")
    consumer.subscribe("order-topic", callback)
    consumer.start()

    print("短信服务已启动，等待消息...")
    try:
        while True:
            pass
    except KeyboardInterrupt:
        consumer.shutdown()

```


## 🎯 运行流程


1. 启动 RocketMQ `namesrv` 和 `broker`。

```bash
sh bin/mqnamesrv
sh bin/mqbroker -n 127.0.0.1:9876

```
2. 启动消费者（库存服务、短信服务）。
3. 运行生产者（订单服务），发送订单消息。

效果：


- 订单服务只管下单和发消息。
- 库存服务和短信服务独立订阅消息，各自执行逻辑。
- 扩展新功能（比如积分服务）时，只要新写一个 Consumer 订阅 `order-topic` 即可。


要不要我帮你扩展一下这个案例，做一个 **秒杀削峰填谷的 Python 实战**（带 Redis 限流 + RocketMQ 异步下单）？
# 用户问题
iot开发的应用场景
# 回答
IoT（物联网）开发的应用场景非常广泛，可以说几乎所有需要 **设备联网、数据采集、远程控制、智能化处理** 的行业都能用得上。帮你总结几个典型应用场景，按行业来分类：



## 🏭 工业制造（IIoT）


- **设备监控与预测性维护**


给生产设备加传感器，实时采集温度、振动、电流等数据。


通过 IoT 平台分析异常 → 提前预警，避免设备停机。
- **生产线优化**


设备联网，把工艺参数上传到云端，进行大数据分析 → 提高良品率。


## 🏙️ 智慧城市


- **智慧路灯**：灯具接入 IoT 平台，远程调光、节能控制。
- **智能交通**：红绿灯联网、路口车流量监测，结合 AI 做交通调度。
- **环境监测**：空气质量监测站点联网，实时监控 PM2.5、噪音、水质等。


## 🏥 医疗健康


- **远程监护**：智能手环、血压计、心电仪联网 → 医生/家属实时监测。
- **智慧病房**：床位状态、输液监控联网，减少人工巡视。
- **健康管理**：用户可穿戴设备采集运动、睡眠数据 → 云端分析健康趋势。


## 🏠 智能家居


- **智能音箱**：语音控制家电（灯、电视、空调）。
- **智能安防**：门磁、摄像头联网 → 手机远程查看和报警推送。
- **节能管理**：家电联网，自动优化用电（如空调根据温度自动调节）。


## 🚜 农业与畜牧业


- **智慧农业**


土壤湿度、光照、温度传感器 → 自动控制灌溉、施肥。
- **智慧养殖**


水产养殖池的溶氧传感器 → 自动控制增氧泵，减少鱼虾死亡率。


## 🚚 物流与车联网


- **车联网**：车辆位置、速度、油耗实时上传 → 优化调度、降低油耗。
- **冷链物流**：温度传感器监控运输过程，保证疫苗/生鲜的安全。
- **资产追踪**：货柜、包裹加 GPS 设备 → 实时追踪运输状态。


## ⚡ 能源与公用事业


- **智能电网**：电表联网，远程抄表、动态调度电力负荷。
- **石油天然气**：油井、管道压力传感器联网，实时监控泄漏风险。
- **智能水务**：水表、管道传感器联网，监控用水与漏损。


## 📡 典型 IoT 应用架构


1. **感知层**（传感器、摄像头、RFID、GPS）。
2. **网络层**（NB-IoT、LoRa、5G、Wi-Fi、MQTT、CoAP）。
3. **平台层**（设备接入、消息队列如 RocketMQ、数据存储）。
4. **应用层**（智慧城市、智能制造、智能家居等）。


👉 IoT 的一个共性就是：**海量设备 + 异构协议 + 大规模消息处理**，这时候用到 **MQTT / RocketMQ / Kafka** 等消息中间件去做数据采集和分发，就能解耦设备和应用。



# iot和rokectmq结合的场景
IoT 和 RocketMQ 的结合，其实就是 **“海量设备数据如何高效采集、传输、处理”** 的问题。


## 🔗 IoT 为什么需要 RocketMQ？


IoT 系统有几个典型特点：


1. **设备规模大**：可能是几万台，甚至上百万个传感器。
2. **消息频率高**：比如温度传感器每秒上报一次，轻松就是亿级消息。
3. **异构协议多**：MQTT、CoAP、HTTP、LoRa、NB-IoT……
4. **应用场景多**：同一条数据可能要同时供给监控、告警、分析、存储。

👉 所以 IoT 平台通常会在 **设备接入网关** 后，接入一个 **高性能消息中间件**，RocketMQ 就是一个非常合适的选择。



## 🚀 RocketMQ 在 IoT 中的作用


1. **数据采集与转发**

- 设备通过 MQTT / CoAP 上报 → 网关转换后写入 RocketMQ。
- RocketMQ 将数据分发给多个下游消费者（存储、实时分析、告警系统）。
2. **削峰填谷**

- 高并发设备同时上报，RocketMQ 缓冲数据，避免直接压垮数据库。
3. **异步解耦**

- IoT 平台只负责接入设备，把消息投递到 RocketMQ。
- 下游业务（如可视化平台、AI 分析、告警系统）独立订阅消息。
4. **可靠消息与顺序保证**

- IoT 常有场景需要保证数据顺序（如设备状态机变化）。
- RocketMQ 提供 **顺序消息** 和 **事务消息**，保证一致性。
5. **大数据/AI 对接**

- RocketMQ 的消息可直接被 Flink、Spark、Hadoop 等大数据框架消费，用于实时计算与 AI 模型训练。


## 📡 IoT + RocketMQ 典型架构


```swift
设备端 (传感器/网关) 
   ↓  MQTT/CoAP/HTTP
IoT 接入网关 (协议转换/认证)
   ↓
RocketMQ (高并发消息通道)
   ├── 实时告警服务（超温报警）
   ├── 时序数据库（存储历史数据）
   ├── 可视化大屏（监控）
   └── AI 分析（预测性维护）

```


## 🛠️ 实战举例


### 场景：智慧工厂设备监控


- 设备端：每台 CNC 机床上传温度、电流、振动数据（MQTT）。
- IoT 网关：接入数据，转发到 RocketMQ `iot-device-topic`。
- 消费者：


**监控服务**：订阅消息，实时大屏显示。


**告警服务**：发现温度 &gt; 阈值，触发短信报警。


**预测性维护**：消息流入 Flink，结合历史数据训练模型，预测设备故障。

这样 RocketMQ 在中间充当 **消息中枢**，把 IoT 的高并发数据流管理得很稳。



👉 要不要我给你写一个 **Python 版 IoT + RocketMQ 简单实战 Demo**？比如模拟 100 个传感器不断上传温度数据 → RocketMQ → 后端订阅处理。
# 用户问题
可以
# 回答
好 👍 我来给你一个 **Python 版 IoT + RocketMQ Demo**，场景模拟 **100 个传感器不停上传温度数据**，RocketMQ 作为消息中枢，下游服务订阅消息做处理。



## ⚙️ 环境准备


1. RocketMQ 运行起来（namesrv + broker）：

```bash
sh bin/mqnamesrv
sh bin/mqbroker -n 127.0.0.1:9876

```
2. 安装 Python 客户端：

```bash
pip install rocketmq-client-python

```


## 1️⃣ Producer：模拟 IoT 设备（传感器上报温度）


```python
import random
import time
from rocketmq.client import Producer, Message

producer = Producer("iot-sensor-producer")
producer.set_name_server_address("127.0.0.1:9876")
producer.start()

def simulate_sensor(sensor_id):
    """模拟一个传感器上报温度"""
    temperature = round(random.uniform(20, 40), 2)  # 随机温度 20~40℃
    msg = Message("iot-topic")
    msg.set_keys(sensor_id)
    msg.set_tags("temperature")
    msg.set_body(f"{sensor_id},{temperature}")
    ret = producer.send_sync(msg)
    print(f"[发送] 传感器 {sensor_id} 温度={temperature}°C, 状态={ret.status}")

if __name__ == "__main__":
    try:
        while True:
            for i in range(1, 101):  # 模拟 100 个传感器
                simulate_sensor(f"sensor_{i}")
            time.sleep(2)  # 每 2 秒采集一次
    except KeyboardInterrupt:
        producer.shutdown()

```


## 2️⃣ Consumer A：实时监控服务


```python
from rocketmq.client import PushConsumer

def callback(msg):
    sensor_id, temperature = msg.body.decode("utf-8").split(",")
    print(f"[监控服务] {sensor_id} 当前温度: {temperature}°C")
    return

if __name__ == "__main__":
    consumer = PushConsumer("monitor-consumer-group")
    consumer.set_name_server_address("127.0.0.1:9876")
    consumer.subscribe("iot-topic", callback)
    consumer.start()
    print("监控服务已启动，等待消息...")
    try:
        while True:
            pass
    except KeyboardInterrupt:
        consumer.shutdown()

```


## 3️⃣ Consumer B：告警服务（超过 35℃ 报警）


```python
from rocketmq.client import PushConsumer

def callback(msg):
    sensor_id, temperature = msg.body.decode("utf-8").split(",")
    temperature = float(temperature)
    if temperature &gt; 35:
        print(f"[告警服务] ⚠️ {sensor_id} 超温报警: {temperature}°C")
    return

if __name__ == "__main__":
    consumer = PushConsumer("alert-consumer-group")
    consumer.set_name_server_address("127.0.0.1:9876")
    consumer.subscribe("iot-topic", callback)
    consumer.start()
    print("告警服务已启动，等待消息...")
    try:
        while True:
            pass
    except KeyboardInterrupt:
        consumer.shutdown()

```


## 🎯 运行效果


1. 启动 **监控服务** 和 **告警服务**（两个 Consumer）。
2. 启动 **Producer**，模拟 100 个传感器上报数据。
3. 你会看到：

- 监控服务实时打印所有传感器温度。
- 告警服务只在温度 &gt; 35℃ 时输出报警信息。


这样，一个简化版的 **IoT + RocketMQ 流程** 就跑通了：


- 设备 → RocketMQ → 多个下游服务（监控 / 告警）。
- 后续你还可以加：**存储服务（写入时序数据库 InfluxDB）**、**大数据分析服务（Flink/Kafka Connect）**。
