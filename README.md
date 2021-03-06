# rabbitmq-springDemo
   spring与rabbitmq的整合,消息队列的生成与消费都在此工程,消息代理broker简单学习
   
## 消息工作线：producer-channel-exchange-queue-consumer

> 流程<br>
>>消息发送指定交换机和路由指令<br>
>>rabbitmq broker创建通道管理消息,放入指定的交换机,交换机再根据路由指令和与它绑定的队列进行匹配,放入匹配成功的队列中<br>
>>消费者同步或者异步接收指定队列的消息,并将消费情况通过回执告诉broker,broker阶段回执进行消息的处理（放入未确认消息或者出队列）<br>
   
## 概念及定义

>1.客户端发送消息<br>
>>消息体：支持Object类型<br>
>>消息路由指令routingKey:String<br>
>>消息发送需指定交换机：exchangeName,指定交换机的名称即可<br>
>>消息可以设置优先级<br>
  
>2.broker 处理消息<br>
>>2.1 channel<br>
 
>>>几乎所有的操作都在channel中进行，channel是进行消息读写的通道。客户端可建立多个channel，每个channel代表一个会话任务 <br>
>>>java代码创建channel,发送消息和接收消息<br>

>>2.2 exchange<br>     
   
>>>交换机类型exchange-type:direct,topic,fanout,header <br>
       direct:<br>
           binding: queue-队列名称, key: 完全匹配routingKey <br>
       topic:<br>
           binding: queue-队列名称, pattern：模糊匹配routingKey (*表示一个单词,#表示0个或多个单词)<br>
       fanout:<br>
           binding: queue-队列名称, 不需要匹配routingKey,所以绑定的队列都会放入消息<br>
           
>>>其他属性：<br>
       auto-delete:<br>
            交换机使用完成自动删除<br>
       durable：<br>
            交换机持久化<br>
           
>>2.3 queue

>>>消息队列确认机制选择：auto,manual,none<br>
       auto：消息被直接确认,监听到消息就会有回执给broker<br>
       manual: 结合业务，手动且必须显示给出回执：channel.basicAck(deliveryTag,true); broker才会删除消息<br>
>>>其他属性：参数配置 <br>
        auto-delete:消息队列使用完成自动删除<br>
        durable：消息队列持久化<br>
        exclusive：排他性,如果设置,其他用户看不见,但是此用户链接断开后，rabbitmq会删除此队列，无视是否持久化<br>
        
>3.客户端异步监听消息

>>3.1 消息的确认：<br>
      auto-ack：不需要手动给回执<br>
      manual-ack:必须手动给回执<br>
      
>>3.2 消息的获取：<br>
      直接指定方法,按照消息体的类型进行转换<br>
      继承MessageListener, onMessage(Message message),获取消息体并转换<br>
      继承ChannelAwareMessageListener,onMessage(Message message,Channel channel),可以获取channel手动确认消息<br>
