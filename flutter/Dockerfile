FROM ubuntu:latest 
SHELL ["/bin/bash", "-c"]
WORKDIR /workspace
RUN apt update && apt install -y vim less git curl unzip
RUN git clone https://github.com/flutter/flutter.git -b stable 
ENV PATH="${PATH}:/workspace/flutter/bin"
RUN flutter doctor
RUN flutter create project
WORKDIR /workspace/project