FROM harbor.qyvideo.net/duanyifei-test/elastalert:latest

ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
COPY dingtalk_alert.py /opt/elastalert/elastalert_modules/dingtalk_alert.py
RUN mkdir /opt/elastalert/rules


