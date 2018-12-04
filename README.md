# ElastAlert
[toc]

## 介绍
> elastalert 通过查询es,匹配自定义规则发出报警；支持多种探测规则、多种报警渠道；

* 可结合kibana UI web;
* 将定义rules写入es,实现数据高可用；
* 可实现跨es探测；

## 实践
实现限制：
* 版本问题：elastalert 只支持5.x 或 6.3以上版本es/kibana;由于我们使用6.1.1，无法直接在kibana中安装；
* 考虑通过在k8s中单独部署6.5版本es and kibana,探测其他es信息，也存在限制性：
    * 持久化问题： rules写入elastalert,如果漂移rules数据丢失；(k8s持久化方式)
    * 环境依赖问题: elastalert-create-index 需要关联esalert与es环境，才能创建索引；

目前实现：
* 单机部署高版本es、kibana探测线上log、dw ES,实现报警；

未完成：
* [ ] elastalert高可用方式：
    * [ ] 是否可实现类似alertmanager,多台服务alert同步、抑制机制？
* [ ] 封装onepiece接口报警,目前dingtalk；
* [ ] 部分查询类型支持用法存在问题(luncene语法不熟悉):
    * [ ] flatline 流水保底数探测存在问题

## 支持查询类型
```
“Match where there are X events in Y time” (frequency type)(Y分钟内错误数有X个)
“Match when the rate of events increases or decreases” (spike type)(突升、陡降)
“Match when there are less than X events in Y time” (flatline type)(流水保底数)
“Match when a certain field matches a blacklist/whitelist” (blacklist and whitelist type)
“Match on any event matching a given filter” (any type)
“Match when a field has two different values within some time” (change type)
```
参考：http://elastalert.qyvideo.net/app/elastalert-kibana-plugin

## rules定义
> https://elastalert.readthedocs.io/en/latest/recipes/writing_filters.html#writingfilters
> https://elastalert.readthedocs.io/en/latest/ruletypes.html

## 部署(docker/路径下提供es/kibana/esalert docker & k8s部署参考文件)
### 6.5 es/kibana
> https://www.elastic.co/guide/en/elasticsearch/reference/6.5/install-elasticsearch.html
> https://www.elastic.co/guide/en/kibana/current/install.html

### elastalert  
> https://elastalert.readthedocs.io/en/latest/running_elastalert.html

elastalrt 分为elastalert-server & elastalert两个服务，前者为kibana plugin提供后端nodejs服务，后者定义规则、探测ES;



```
1. stop kibana;
2. bin/kibana-plugin install https://github.com/bitsensor/elastalert-kibana-plugin/releases/download/1.0.1/elastalert-kibana-plugin-1.0.1-6.5.0.zip
3. start kibana;

发现hang住情况，ctrl+c取消并不影响使用；
```

```
docker run -d -p 3030:3030 \
    -v `pwd`/config/elastalert.yaml:/opt/elastalert/config.yaml \
    -v `pwd`/config/config.json:/opt/elastalert-server/config/config.json \
    -v `pwd`/rules:/opt/elastalert/rules \
    -v `pwd`/rule_templates:/opt/elastalert/rule_templates \
    -v `pwd`/elastalert_modules/dingtalk_alert.py:/opt/elastalert/elastalert_modules/dingtalk_alert.py \
    --net="host" \
    --name elastalert harbor.qyvideo.net/duanyifei-test/elastalert:latest_v1 
```

## 参考
> https://github.com/bitsensor/elastalert
> https://github.com/bitsensor/elastalert-kibana-plugin

## 总结
* docker log没有明确每次发送和接受的数据，不利于调试；
* 不便于做alert高可用；

**可以读源码，自己实践探测、及可视化配置功能；**