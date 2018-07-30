---
layout: post
title: Spark管理kafka offsets实现Exactly-once
categories: 数据科学
tags: Spark

---

### 实现方法 ###

```scala

Object KafkaOffsetDeal{
    def saveOffset(Dstream:InputStream[(String,String)],redisHost:String,redisPort:Int,redisDB:Int,groupid:String)={
        if(Dstream!=null){
            Dstream.foreachRDD{rdd=>
                val offsetRanges=rdd.asInstanceOf[HashOffsetRanges].offsetRanges
                saveOffset(redisHost,redisPort,rredisDB,groupid,offsetRanges)
            }
        }
    }
    def saveOffset(redisHost:String,redisPort:Int,redisDB:Int,groupid:String,offsetRange:Array[OffsetRange])={
        val redis=new Jedis(redisHost,redisPort,30000)
        redis.auth("passwd")
        redis.select(redisDB)
        for(offsetrange<-offsetRanges){
            val startoffset=offsetrange.fromOffset
            val endoffset=offsetrange.untilOffset
            val partition=offsetrange.partition
            val topic=offsetrange.topic
            redis.hset("key",topic+"|"+partition,startoffset+"|"+endoffset)
        }
        redis.close()
    }
    def getStream(redisHost:String,redisPort:Int,redisDB:Int,groupid:String,topic:String,ssc:StreamContext,kafkaParams:Map[String,String])={
        val offset=loadoffsets(redisHost,redisPort,redisDB,groupid,topic)
        val topicSet=Set(topic)
        val TotalDStream=null
        if(offset.size!=0){
            val messageHandle=(mmd:MessageAndMetadata[String,String])=>(mmd.topic,mmd.message())
            TotalDStream=kafkaUtils.createDirectStream[String,String,StringDecoder,StringDecoder,(String,String)](ssc,kafkaParams,offset,messageHandle)
        }else{
            TotalDStream=kafkaUtils.createDirectStream[String,String,StringDecoder,StringDecoder](ssc,kafkaParams,topicSet)
        }
        TotalDStream
    }
    def loadoffsets(redisHost:String,redisPort:Int,redisDB:Int,groupid:String,topic:String)={
        val redis=new Jedis(redisHost,redisPort,30000)
        redis.auth("passwd")
        redis.select(redisDB)
        var offsets=Map[TopicAndPartition,Long]()
        val data=redis.hgetAll("key")
        for((k,v)<-data){
            val list1=k.split("\\|")
            val list2=v.split("\\|")
            val topic=list1(0)
            val partition=list1(1).toInt
            val offset=list2(1).toLong
            val topicandpartition=TopicAndPartition(topic,partition)
            offsets+=(topicandpartition->offset)
        }
        redis.close
        offsets
    }
}
```


