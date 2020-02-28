---
layout: post
title: Flink ProcessFunction
categories: 数据科学
tags: Flink

---

### 基本概念 ###

- ProcessFunction是什么
底层API、能够访问所有流的基础构建模块
	- 事件
	- 	状态(容错、一致性)
	- 	定时器(event time、processtime、watermarker)

### 使用实例

```scala
val env=StreamExecutionEnvironment.getExecutionEnvironment
    env.setParallelism(1)
    val df=new SimpleDateFormat("yyyy-MM-dd HH:mm:ss")
    val stream:DataStream[(String, Long)]=env.readTextFile("/path/to/data").map { x =>
      val line = x.split("\t", -1)
      (line(5), df.parse(line(0)).getTime)
    }
	//ProcessFunction
    val processedStream=stream.process(new SideOutPut())
    processedStream.getSideOutput(new OutputTag[(String,Long)]("sideOutput")).print()
    //KeyedProcessFunction
	stream.keyBy(_._1).process(new MyProcessAlert())
//如果小于0就抛出到侧输出
  class SideOutPut() extends ProcessFunction[(String, Long),(String,Long)]{
    lazy val outputTag=new OutputTag[(String,Long)]("sideOutput")
    override def processElement(i: (String, Long), context: ProcessFunction[(String, Long), (String, Long)]#Context, collector: Collector[(String, Long)]): Unit = {
      if(i._2<=0){
        context.output(outputTag,i)
      }else
        collector.collect(i)
    }
  }
  //当时间戳比前一个时间戳小1000*60*60时就报警，
  class MyProcessAlert() extends KeyedProcessFunction[String,(String, Long),String]{
    //定义一个状态
    lazy val lastTime=getRuntimeContext.getState(new ValueStateDescriptor[Long]("lastTime",classOf[Long]))
    override def processElement(i: (String, Long), context: KeyedProcessFunction[String, (String, Long), String]#Context, collector: Collector[String]): Unit = {
      val preTime=lastTime.value()
      lastTime.update(i._2)
      //触发定时器报警,ontime会自动触发报警
      if(preTime-i._2>=1000*60*60){
        val currentTs=context.timerService().currentProcessingTime()+1000L
        context.timerService().registerProcessingTimeTimer(currentTs)

      }
    }

    override def onTimer(timestamp: Long, ctx: KeyedProcessFunction[String, (String, Long), String]#OnTimerContext, out: Collector[String]): Unit = {
      out.collect("data delayed 1hs")
    }
  }
```


