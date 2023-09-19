`/root/docker-portainer/docker-stack.yml`：

```shell
version: '3.2'

services:
  agent:
    image: portainer/agent:2.5.1
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /var/lib/docker/volumes:/var/lib/docker/volumes
    networks:
      - carpedx-network
    deploy:
      mode: global
      placement:
        constraints: [node.platform.os == linux]

  portainer:
    image: portainer/portainer-ce:2.5.1
    command: -H tcp://tasks.agent:9001 --tlsskipverify
    ports:
      - "9000:9000"
      - "8000:8000"
    volumes:
      - portainer_data:/data
    logging:
      options:
        max-size: 50m
    networks:
      - carpedx-network
    deploy:
      mode: replicated
      replicas: 1
      placement:
        constraints: [node.role == manager]

networks:
  carpedx-network:
    external: true

volumes:
  portainer_data:
    driver_opts:
      type: "nfs"
      o: "addr=192.168.31.100,vers=4,soft,timeo=180,bg,tcp,rw"
      device: "192.168.31.100:/data/nfs/storage/portainer_data"
```





`/root/docker-nginx/docker-stack.yml` ：

```shell
version: '3.2'

services:
  nginx:
    image: openresty/openresty
    environment:
      TZ: "Asia/ShangHai"
    ports:
      - "80:80"
    volumes:
      - nginx_config:/etc/nginx
      - www_dir:/var/www
      - log_dir:/data/log
    networks:
      - carpedx-network
    sysctls:
      - net.ipv4.vs.conn_reuse_mode=0
      - net.ipv4.vs.expire_nodest_conn=1
    deploy:
      mode: replicated
      replicas: 4

  php:
    image: registry.cn-shanghai.aliyuncs.com/wushidu/php:7.4-fpm
    ports:
      - "7000:9000"
    volumes:
      - www_dir:/var/www
    networks:
      - carpedx-network
    sysctls:
      - net.ipv4.vs.conn_reuse_mode=0
      - net.ipv4.vs.expire_nodest_conn=1
    deploy:
      mode: replicated
      replicas: 4

networks:
  carpedx-network:
    external: true

volumes:
  nginx_config:
    driver_opts:
      type: "nfs"
      o: "addr=192.168.31.100,vers=4,soft,timeo=180,bg,tcp,rw"
      device: "192.168.31.100:/data/nfs/conf/nginx"

  www_dir:
    driver_opts:
      type: "nfs"
      o: "addr=192.168.31.100,vers=4,soft,timeo=180,bg,tcp,rw"
      device: "192.168.31.100:/data/nfs/www"

  log_dir:
    driver_opts:
      type: "nfs"
      o: "addr=192.168.31.100,vers=4,soft,timeo=180,bg,tcp,rw"
      device: "192.168.31.100:/data/nfs/log/nginx"
```





`/root/docker-elk/docker-compose.yml` ：

```shell
version: '3.2'

services:
  elasticsearch:
    build:
      context: elasticsearch/
      args:
        ELK_VERSION: $ELK_VERSION
    volumes:
      - type: bind
        source: ./elasticsearch/config/elasticsearch.yml
        target: /usr/share/elasticsearch/config/elasticsearch.yml
        read_only: true
      - type: volume
        source: elasticsearch
        target: /usr/share/elasticsearch/data
    ports:
      - "9200:9200"
      - "9300:9300"
    environment:
      ES_JAVA_OPTS: "-Xmx256m -Xms256m"
      ELASTIC_PASSWORD: changeme
      # Use single node discovery in order to disable production mode and avoid bootstrap checks.
      # see: https://www.elastic.co/guide/en/elasticsearch/reference/current/bootstrap-checks.html
      discovery.type: single-node
    networks:
      - elk

  logstash:
    build:
      context: logstash/
      args:
        ELK_VERSION: $ELK_VERSION
    volumes:
      - type: bind
        source: ./logstash/config/logstash.yml
        target: /usr/share/logstash/config/logstash.yml
        read_only: true
      - type: bind
        source: ./logstash/pipeline
        target: /usr/share/logstash/pipeline
        read_only: true
    ports:
      - "5044:5044"
      - "5000:5000/tcp"
      - "5000:5000/udp"
      - "9600:9600"
    environment:
      LS_JAVA_OPTS: "-Xmx256m -Xms256m"
    networks:
      - elk
    depends_on:
      - elasticsearch

  kibana:
    build:
      context: kibana/
      args:
        ELK_VERSION: $ELK_VERSION
    volumes:
      - type: bind
        source: ./kibana/config/kibana.yml
        target: /usr/share/kibana/config/kibana.yml
        read_only: true
    ports:
      - "5601:5601"
    networks:
      - elk
    depends_on:
      - elasticsearch

networks:
  elk:
    driver: bridge

volumes:
  elasticsearch:
```



``/root/docker-elk/docker-stack.yml` ：

```shell
version: '3.3'

volumes:
  es_data:
    driver_opts:
      type: "nfs"
      o: "addr=192.168.31.100,vers=4,soft,timeo=180,bg,tcp,rw"
      device: "192.168.31.100:/data/nfs/storage/es_data"
  filebeat_data:
    driver_opts:
      type: "nfs"
      o: "addr=192.168.31.100,vers=4,soft,timeo=180,bg,tcp,rw"
      device: "192.168.31.100:/data/nfs/storage/filebeat_data"
  log_data:
    driver_opts:
      type: "nfs"
      o: "addr=192.168.31.100,vers=4,soft,timeo=180,bg,tcp,rw"
      device: "192.168.31.100:/data/nfs/log"

services:

  elasticsearch:
    image: registry.cn-shanghai.aliyuncs.com/wushidu/elasticsearch:7.9.3
    ports:
      - "9200:9200"
      - "9300:9300"
    configs:
      - source: elastic_config
        target: /usr/share/elasticsearch/config/elasticsearch.yml
    volumes:
      - es_data:/usr/share/elasticsearch/data
    environment:
      ES_JAVA_OPTS: "-Xmx256m -Xms256m"
      ELASTIC_PASSWORD: love520555
      # Use single node discovery in order to disable production mode and avoid bootstrap checks.
      # see: https://www.elastic.co/guide/en/elasticsearch/reference/current/bootstrap-checks.html
      discovery.type: single-node
      # Force publishing on the 'elk' overlay.
      network.publish_host: _eth0_
    networks:
      - carpedx-network
    deploy:
      mode: replicated
      replicas: 1

  logstash:
    image: registry.cn-shanghai.aliyuncs.com/wushidu/logstash:7.9.3
    ports:
      - "5044:5044"
      - "5000:5000"
      - "9600:9600"
    configs:
      - source: logstash_config
        target: /usr/share/logstash/config/logstash.yml
      - source: logstash_pipeline
        target: /usr/share/logstash/pipeline/logstash.conf
    environment:
      LS_JAVA_OPTS: "-Xmx256m -Xms256m"
    networks:
      - carpedx-network
    deploy:
      mode: replicated
      replicas: 1

  kibana:
    image: registry.cn-shanghai.aliyuncs.com/wushidu/kibana:7.9.3
    ports:
      - "5601:5601"
    configs:
      - source: kibana_config
        target: /usr/share/kibana/config/kibana.yml
    networks:
      - carpedx-network
    deploy:
      mode: replicated
      replicas: 1

  apm-server:
    image: registry.cn-shanghai.aliyuncs.com/wushidu/apm-server:7.9.3
    command:
        # Disable strict permission checking on 'apm-server.yml' configuration file
        # https://www.elastic.co/guide/en/beats/libbeat/current/config-file-permissions.html
      - --strict.perms=false
    configs:
      - source: apm_config
        target: /usr/share/apm-server/apm-server.yml
    ports:
      - '8200:8200'
    networks:
      - carpedx-network
    depends_on:
      - elasticsearch
    deploy:
      mode: replicated
      replicas: 1

  filebeat:
    image: registry.cn-shanghai.aliyuncs.com/wushidu/filebeat:7.9.3
    configs:
      - source: filebeat_config
        target: /usr/share/filebeat/filebeat.yml
    environment:
      - LOGSTASH_HOST=logstash
      - KIBANA_HOST=kibana
      - ELASTICSEARCH_USERNAME=elastic
      - ELASTICSEARCH_PASSWORD=love520555
    volumes:
      - filebeat_data:/usr/share/filebeat/data
      - log_data:/data
    networks:
      - carpedx-network
    depends_on:
      - logstash
    deploy:
      mode: replicated
      replicas: 1

configs:

  elastic_config:
    file: ./elasticsearch/config/elasticsearch.yml
  logstash_config:
    file: ./logstash/config/logstash.yml
  logstash_pipeline:
    file: ./logstash/pipeline/logstash.conf
  kibana_config:
    file: ./kibana/config/kibana.yml
  metricbeat_config:
    file: ./beats/metricbeat/metricbeat.yml
  filebeat_config:
    file: ./beats/filebeat/filebeat.yml
  apm_config:
    file: ./extensions/apm-server/config/apm-server.yml

networks:
  carpedx-network:
    external: true
```

