FROM registry.redhat.io/rhel8/go-toolset as builder

WORKDIR app 

RUN git clone https://github.com/kubernetes-csi/csi-proxy
RUN sudo make build



