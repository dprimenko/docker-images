FROM java:8

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install --no-install-recommends -y supervisor curl && apt-get clean

# Elastic repositories
RUN wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
RUN echo "deb https://artifacts.elastic.co/packages/5.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-5.x.list

# Elasticsearch
RUN apt-get update && apt-get install --no-install-recommends -y elasticsearch && apt-get clean
RUN sed -i '/#cluster.name:.*/a cluster.name: logstash' /etc/elasticsearch/elasticsearch.yml
RUN sed -i '/#path.data: \/path\/to\/data/a path.data: /data' /etc/elasticsearch/elasticsearch.yml
ADD etc/supervisor/conf.d/elasticsearch.conf /etc/supervisor/conf.d/elasticsearch.conf

# Logstash
RUN apt-get install -y apt-transport-https
RUN apt-get install --no-install-recommends -y logstash && apt-get clean
ADD etc/supervisor/conf.d/logstash.conf /etc/supervisor/conf.d/logstash.conf
RUN /opt/logstash/bin/plugin install logstash-filter-translate

# Kibana
RUN apt-get install --no-install-recommends -y logstash && apt-get clean
RUN sed -i 's/port: 5601/port: 80/' /opt/kibana/config/kibana.yml
ADD etc/supervisor/conf.d/kibana.conf /etc/supervisor/conf.d/kibana.conf

EXPOSE 80

ENV PATH /opt/logstash/bin:$PATH

CMD [ "/usr/bin/supervisord", "-n", "-c", "/etc/supervisor/supervisord.conf" ]
