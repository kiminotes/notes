# sarama 源码概览

[sarama](https://github.com/Shopify/sarama) 是 [Shopify](https://github.com/Shopify) 开源的 Golang 版本的 [kafka](https://kafka.apache.org/) 客户端。使用 sarama 的过程中粗略地读了 sarama 的部分源码，本文做一个简单的纪要。后续如果有深入的研究，也会进一步分享具体的实现细节。

sarama 源码从下网上可以分为 4 层。
1. 网络传输层 - 使用 Golang 的 [net](https://pkg.go.dev/net) 包向 kafka broker 发送请求 (参见 Broker 的 [write](https://github.com/Shopify/sarama/blob/main/broker.go#L876) 方法) 以及接收来自 kafka broker 的响应 (参见 Broker 的 [responseReceiver](https://github.com/Shopify/sarama/blob/main/broker.go#L1032) 方法)。在这之上封装了请求响应模型 (参见 Broker 的 [send](https://github.com/Shopify/sarama/blob/main/broker.go#L884) 方法)。
2. kafka protocol - 使用请求响应模型实现 [kafka protocol](https://kafka.apache.org/protocol.html) 以及相应的 [encoder](https://github.com/Shopify/sarama/blob/6d970c751edfe80a21c91376ce749070541e4352/encoder_decoder.go#L11) 和 [decoder](https://github.com/Shopify/sarama/blob/6d970c751edfe80a21c91376ce749070541e4352/encoder_decoder.go#L15)。想要了解 sarama 实现了 kafka protocol 中哪些请求响应类型可以参考 [allocateBody](https://github.com/Shopify/sarama/blob/main/request.go#L119) 方法。
3. kafka 的[客户端](https://github.com/Shopify/sarama/blob/main/client.go#L15)和[管理客户端](https://github.com/Shopify/sarama/blob/main/admin.go#L16)。
4. 发送 kafka 消息的[同步发送客户端](https://github.com/Shopify/sarama/blob/6d970c751edfe80a21c91376ce749070541e4352/sync_producer.go#L15)和[异步发送客户端](https://github.com/Shopify/sarama/blob/6d970c751edfe80a21c91376ce749070541e4352/async_producer.go#L19)，以及消费消息的 [ConsumerGroup](https://github.com/Shopify/sarama/blob/6d970c751edfe80a21c91376ce749070541e4352/consumer_group.go#L19) 和 [Consumer](https://github.com/Shopify/sarama/blob/6d970c751edfe80a21c91376ce749070541e4352/consumer.go#L54)。
