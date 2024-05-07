---
title: elasticsearch 安装
date: 2022/4/12 22:15:59
type: book
draft: true
category:
  - elasticsearch
keywords:
  - elasticsearch
---

```yml
version: "3"

services:
  scra_elastic:
    restart: always
    image: docker.elastic.co/elasticsearch/elasticsearch:8.1.2
    container_name: scra_elastic
    environment:
      discovery.type: single-node
      # 设置端口
    ports:
      - "9200:9200"
      - "9300:9300"
    networks:
      - elastic

  scra_kibana:
    restart: always
    image: docker.elastic.co/kibana/kibana:8.1.2
    container_name: scra_kibana
    #    environment:
    #      ELASTICSEARCH_HOSTS: '["http://scra_elastic:9200"]'
    #      SERVER_HOST: "127.0.0.1"
    # 设置端口
    ports:
      - "5601:5601"
    networks:
      - elastic

networks:
  elastic:
    driver: bridge
```
