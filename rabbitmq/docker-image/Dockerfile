FROM rabbitmq:3.7.10
MAINTAINER Vanderson Assis
RUN rabbitmq-plugins enable rabbitmq_management
RUN rabbitmq-plugins enable rabbitmq_peer_discovery_k8s
ADD rabbitmq.conf /etc/rabbitmq/