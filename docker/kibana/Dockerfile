FROM harbor.qyvideo.net/duanyifei-test/kibana:6.5.0

USER root
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

USER kibana
COPY kibana.yml /usr/share/kibana/config/kibana.yml
COPY elastalert-kibana-plugin-1.0.1-6.5.0.zip /usr/share/kibana/plugins/elastalert-kibana-plugin-1.0.1-6.5.0.zip
RUN /usr/share/kibana/bin/kibana-plugin install file:///usr/share/kibana/plugins/elastalert-kibana-plugin-1.0.1-6.5.0.zip -d /usr/share/kibana/plugins