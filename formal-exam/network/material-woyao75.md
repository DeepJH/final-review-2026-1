# 1. VLAN作用以及原理

## VLAN作用
- 隔离广播域：限制广播范围抑制广播风暴
- 增强安全性：VLAN间二层隔离 ，防止未授权访问
- 简化管理：逻辑分组不受物理位置限制
    
## VLAN原理
- 802.1Q标签：在以太网中插入4字节Tag标识
- 端口转发规则：Access口剥离/添加标签连接终端；Trunk口带标签透传多VLAN；跨VLAN需三层路由

## 为什么要划VLAN？
为了解决广播风暴，保证物理安全，不然随意一台电脑发广播消息，所有电脑都会接收，划分VLAN实现只有相同VLAN才能接收广播
        
# 2. 阐述单臂路由的原理和作用

## 核心作用
实现不同VLAN之间的通信
        
## 工作原理
通过划分接口，把接口如0/1分为0/1.1和0/1.2，一个字接口传一个VLAN的数据
        
# 3. TCP/IP网络体系结构

## 划分为4层

| 层次 | 功能说明 | 
| --- | --- |
| 应用层 | 直接为用户提供网络服务 |
| 传输层 | 提供端到端的通信服务 |
| 网际层 | 寻址、数据打包和路由选择 |
| 网络接口层 | 处理帧的发送与接收 |
        
## 划分为5层

| 层次 | 功能说明 | 
| --- | --- |
| 应用层 | 直接为用户提供网络服务 |
| 传输层 | 提供端到端的通信服务 |
| 网络层 | 寻址、数据打包和路由选择 |
| 数据链路层 | 研究帧、MAC地址、差错检测 |
| 物理层 | 研究比特流传输、信号编码 |
        
## 划分为7层

| 层次 | 对应关系 | 功能说明 |
| --- | --- | --- |
| 应用层 | 应用层 | 用户接口与服务 |
| 表示层 | 无 | 加密、压缩、格式转换 |
| 会话层 | 无 | 会话管理、同步 |
| 传输层 | 传输层 | 端到端通信 |
| 网络层 | 网际层 | 路由与寻址 |
| 数据链路层 | 网络接口层 | 帧传输与介质访问控制 |
| 物理层 | 网络接口层 | 比特流传输 |
        
# 4. ARP原理以及解析方法
## 原理
(empty...)
## 如何解析？
根据IP地址解析MAC地址
# 实验一命令配置
1. 基础配置(PC1、PC2、PC3的IP、掩码、网关)
2. CLI命令行配置
- Switch2

```bash
enable
config t
vlan 10
exit
interface fa0/5
switchport mode access
switchport access vlan 10
exit
interface fa0/24
switchport mode trunk
```

- Switch1

```bash
enable
config  t
ip routing
vlan 10
exit
vlan 20
exit
interface fa0/24
switchport trunk encapsulation dot1q
switchport mode trunk
exit
interface fa0/5(0/8)
switchport mode access
switchport access vlan 10(20)
exit
interface Vlan10(20)
ip address 192.168.10(20).1 255.255.255.0
no shutdown
exit
```
                
# 实验二命令配置
1.  基础配置(PC1、PC2、PC3的IP、掩码、网关)
2. CLI命令行配置
- Switch1配置(Switch2同理)

```bash
enable
config t
vlan 10
exit
vlan 20
exit
interface fa0/1(0/2)
switchport mode access
switchport access vlan 30(10)
exit
interface fa0/24
switchport mode trunk
```

- Switch0配置

```bash
enable
config t
ip routing
vlan 10(20 30 200)
exit
interface range fa0/1 - 2
switchport trunk encapsulation dot1q
switchport mode trunk
exit
interface Vlan 10(20 30 200)
ip address 192.168.10(20 30 200).1 255.255.255.0
no shutdown
exit
```