---
title: fabric Error 分析
date: 2019-10-31 18:08:45
tags:
- Fabric
categories: 
- 总结
---

个人在学习 fabric1.0 项目中遇到的 一些 错误和原因总结，如发现错误即时指出。


1. ERROR: could not find an available, non-overlapping IPv4 address pool among the defaults to assign to the network

because： docker network ls | wc -l 不能超过30 个


2. Error: open chaincodes/net/test1/1.0/test1.out: no such file or directory

because： cc 包路径不能是相对的 应该是绝对的


3. ERRO : GetActivePeer failed: There's no fit peer!

because：从网络addorgchannel 之前主网络要有一个锚节点



4. Caused by: x509: certificate is valid for peer0.org2.net.example.com, not peer0.org1.net.example.com

because： peer 的端口号写错了



5. not find chaincode 实例化成功但 查询报错找不到对应chaincode

because： channel 名字写错了， 安装cc 和 实例化的时候要指定 channel 名字


6. code = Unknown desc = Failed to deserialize creator identity, err MSP org1MSP is unknown - <nil>

because：

1. 查询交易遇到的问题， peer1 没加入channel 每个peer 都要加入channel

2. 执行交易命令 没有加 -C mychannel , 没有指定正确的channel



7. invoke ok， query 却没有金额变化

because： 安装chaincode 时背书策略 写错了 msp， 可以看对应的peer log



8.Error: proto: can't skip unknown wire type 6 for common.Envelope

because: 更新配置块操作， 新增的组织内容写错了， 里面用jq 命令， 新增组织是复制之前已有的组织内容 生成的



9. Error: Error sending transaction invoke: Got unexpected status: BAD_REQUEST - version:1 response:<status:200 message:"OK" > payload:

because: 执行过更新配置块， 删除了Org, 再用这个组织发交易就会报这种错误， 看对应peer log Org3MSP unKnow



10. Error: exspect MspId org4MSP, receice Org4MSP

because: peer yaml 文件有个 环境变量写成了 小写的org4MSP了



11. CONFIG_UPDATE because: Attempted to include a member which is not in the consortium

because: 创建了新的 channel.tx 如果里面添加了不在创世区块的里机构 ， 在执行创建channel就报这个错



12. [cauthdsl] func2 -> ERRO 2f95 Principal deserialization failure (MSP Org3MSP is unknown) for identity xxxx

because: 不在创世机构里面的组织， 发起创建channel交易。 order 报错



13. chaincode error (status: 500, message: Cannot create ledger from genesis block, due to LedgerID already exists))

because: 重复 加入同一个channel
