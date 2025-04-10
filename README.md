---
title: 2025-03-08 下午場的實作命令參考

---

# 2025-03-08 下午場的實作命令參考

# Anything LLM

AnythingLLM 是由 Mintplex Labs 開發的一款全方位 AI 應用程式，旨在讓使用者輕鬆地與各種文件互動，提升生產力，並確保資料的私密性。

**主要特點：**

- **多模型支援：** AnythingLLM 支援多種大型語言模型（LLM），包括商業和開源模型，使用者可以根據需求選擇最適合的模型。
- **完全私密：** AnythingLLM 預設在本地執行，所有資料和模型都存儲在使用者的電腦上，確保資料不會被分享，除非使用者主動允許。
- 多文件格式： 該應用程式允許使用者上傳各種文件格式，如 PDF、Word、CSV、程式碼庫等，並能與之互動，滿足不同的業務需求。
    - 主要得益於檢索增強生成（Retrieval-Augmented Generation，簡稱 RAG）技術的應用。
    - 可以在使用 Anything LLM 時，透過 RAG 與 API 進行模型資料的驗證。

**安裝：**

- 下載網址：[https://anythingllm.com/desktop](https://anythingllm.com/desktop)

### 設定

**https://cloudinfra-services.ubilink.ai/02b62aba-db1e-42a1-8278-9d778445794d/<id>/caddy/v1**

<id> = slave001

![image](https://hackmd.io/_uploads/Hk1QF1H01x.png)

---

# cURL

取得模型

```jsx
GET https://cloudinfra-services.ubilink.ai/02b62aba-db1e-42a1-8278-9d778445794d/slave102/caddy/v1/models
```

```jsx
curl -X POST https://cloudinfra-services.ubilink.ai/02b62aba-db1e-42a1-8278-9d778445794d/slave103/caddy/v1/chat/completions -H "Content-Type: application/json" -H "Authorization: Bearer YOUR_API_KEY" -d '{
  "model": "deepseek-ai/DeepSeek-R1",
  "messages": [{"role": "user", "content": "請問今天的天氣如何？ (answer me in #zh-tw)"}],
  "temperature": 0.7
}'
```

# Postman

## List models

GET [https://cloudinfra-services.ubilink.ai/02b62aba-db1e-42a1-8278-9d778445794d/slave102/caddy/v1/models](https://cloudinfra-services.ubilink.ai/02b62aba-db1e-42a1-8278-9d778445794d/slave102/caddy/v1/models)

```jsx
GET [https://cloudinfra-services.ubilink.ai/02b62aba-db1e-42a1-8278-9d778445794d/slave102/caddy/v1/models](https://cloudinfra-services.ubilink.ai/02b62aba-db1e-42a1-8278-9d778445794d/slave102/caddy/v1/models)
```

## Inferencing

POST [https://cloudinfra-services.ubilink.ai/02b62aba-db1e-42a1-8278-9d778445794d/slaveXXX/caddy/v1/chat/completions](https://cloudinfra-services.ubilink.ai/02b62aba-db1e-42a1-8278-9d778445794d/slaveXXX/caddy/v1/chat/completions)

XXX = your id

## Body

```jsx
{
  "model": "deepseek-ai/DeepSeek-R1",
  "messages": [{"role": "user", "content": "請問今天的天氣如何？ (answer me in #zh-tw)"}],
  "temperature": 0.7
}
```

## Auth
![image 1](https://hackmd.io/_uploads/SkzSt1HR1g.png)

![image 2](https://hackmd.io/_uploads/rk6BYkSCyg.png)

# Cline

[https://marketplace.visualstudio.com/items?itemName=saoudrizwan.claude-dev](https://marketplace.visualstudio.com/items?itemName=saoudrizwan.claude-dev)

## Settings

API Provider: `OpenAI Compatible`

Base URL:

[https://cloudinfra-services.ubilink.ai/02b62aba-db1e-42a1-8278-9d778445794d/slaveXXX/caddy/v1](https://cloudinfra-services.ubilink.ai/02b62aba-db1e-42a1-8278-9d778445794d/slave137/caddy/v1)

xxx = your id

API Key: `123`

Model ID: `deepseek-ai/DeepSeek-R1`

![image 3](https://hackmd.io/_uploads/B1kDK1H0kl.png)

![image 4](https://hackmd.io/_uploads/BkjvYySCJx.png)


# 上課連線

會使用 JupyterLab 進行連線

1. 從訊息中得網址連線並輸入 Token
    
    [https://cloudinfra-services.ubilink.ai/02b62aba-db1e-42a1-8278-9d778445794d/<xxx>/lab](https://cloudinfra-services.ubilink.ai/02b62aba-db1e-42a1-8278-9d778445794d/slave87/lab/workspaces/auto-C)
    
![image 5](https://hackmd.io/_uploads/ryudY1rCkx.png)

    
2. 打開 Terminal
    
![image 6](https://hackmd.io/_uploads/ryaOF1SCyx.png)

3. 就可以輸入指令使用

---

# Dockerfile (供大家課後自行建立)

```docker
FROM nvcr.io/nvidia/tritonserver:24.04-py3-min

ENV DEBIAN_FRONTEND=noninteractive

# 環境變數設置
ENV SSH_USER=deepseek \
    SSH_PASSWORD=secure_123 \
    JUPYTER_CUSTOM_DISPLAY_URL=https://cloudinfra-services.ubilink.ai/02b62aba-db1e-42a1-8278-9d778445794d/nemo_lab \
    JUPYTER_BASE_URL=/02b62aba-db1e-42a1-8278-9d778445794d/nemo_lab \
    JUPYTER_TOKEN=your_secure_token

# 設置時區
RUN echo 'tzdata tzdata/Areas select Asia' | debconf-set-selections \
    && echo 'tzdata tzdata/Zones/Asia select Taipei' | debconf-set-selections

# 安裝基礎套件 - 第一層
RUN apt update -y \
    && apt install -y --no-install-recommends \
        software-properties-common \
    && add-apt-repository ppa:deadsnakes/ppa -y \
    && apt update \
    && rm -rf /var/lib/apt/lists/* \
    && apt clean

# 安裝 Python 3.10 - 第二層 
RUN apt update -y \
    && apt install -y --no-install-recommends \
        python3.10 \
        python3.10-dev \
        python3.10-distutils \
    && update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.10 1 \
    && update-alternatives --set python3 /usr/bin/python3.10 \
    && rm -rf /var/lib/apt/lists/* \
    && apt clean

# 安裝其他必要套件 - 第三層
RUN apt update -y \
    && apt install -y --no-install-recommends \
        curl \
        git \
        git-lfs \
        sudo \
        libibverbs-dev \
        openssh-server \
        openssh-client \
        iputils-ping \
        nodejs \
        npm \
        pdsh \
        openmpi-bin \
        openmpi-common \
        libopenmpi-dev \
        supervisor \
        tmux \
        iproute2 \
        vim \
    && curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py \
    && python3 get-pip.py

RUN apt install -y debian-keyring debian-archive-keyring apt-transport-https curl \
    && curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | gpg --dearmor -o /usr/share/keyrings/caddy-stable-archive-keyring.gpg \
    && curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | tee /etc/apt/sources.list.d/caddy-stable.list \
    && apt update \
    && apt install -y caddy

RUN rm -rf /var/lib/apt/lists/* \
    && apt clean

WORKDIR /app

# SSH 配置
RUN mkdir /var/run/sshd \
    && echo 'PermitRootLogin yes' >> /etc/ssh/sshd_config \
    && echo 'StrictHostKeyChecking no' >> /etc/ssh/ssh_config \
    && useradd -m -s /bin/bash ${SSH_USER} \
    && echo "${SSH_USER}:${SSH_PASSWORD}" | chpasswd \
    && usermod -aG sudo ${SSH_USER} \
    && echo "${SSH_USER} ALL=(ALL) ALL" >> /etc/sudoers \
    && mkdir -p /home/${SSH_USER}/.ssh \
    && chmod 700 /home/${SSH_USER}/.ssh \
    && chown -R ${SSH_USER}:${SSH_USER} /home/${SSH_USER}/.ssh

# 複製並安裝其他必要文件
COPY --chown=${SSH_USER}:${SSH_USER} . .
RUN rm /app/Dockerfile

# 設置 huggingface 目錄為只讀
RUN mkdir -p /home/${SSH_USER}/.cache \
    && mkdir -p /home/${SSH_USER}/.cache/huggingface \
    && chown -R ${SSH_USER}:${SSH_USER} /home/${SSH_USER}/.cache \
    && chown -R root:root /home/${SSH_USER}/.cache/huggingface \
    && chmod -R 555 /home/${SSH_USER}/.cache/huggingface

# 設置 supervisor 和啟動腳本
RUN mkdir -p /app/supervisor \
    && cp supervisord.conf /etc/supervisor/conf.d/ \
    && chmod +x /app/start_node.sh

# 清理不必要的文件
RUN rm -rf /root/.cache \
    && rm -rf /tmp/* \
    && rm -rf /var/tmp/*

# 安裝其他 Python 依賴
RUN pip3 install --no-cache-dir -r requirements.txt

# 切換到非 root 用戶
RUN chown -R ${SSH_USER}:${SSH_USER} /app

USER ${SSH_USER}

# 暴露端口
EXPOSE 8888 8000 22

# 設置啟動命令
CMD ["/app/start_node.sh"] 
```


---

# 程式碼

## Benchmark

```bash
git clone https://github.com/vllm-project/vllm

cd vllm/benchmarks

python3 benchmark_serving.py --backend openai --base-url http://127.0.0.1:30000 --dataset-name=random --model=deepseek-ai/DeepSeek-R1 --seed 12345 --save-result --tokenizer-mode=auto
```

## Caddy

```bash
sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https curl 

curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | sudo gpg --dearmor -o /usr/share/keyrings/caddy-stable-archive-keyring.gpg 

curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | sudo tee /etc/apt/sources.list.d/caddy-stable.list 

sudo apt update sudo apt install caddy

```

## Caddyfile

```docker
:8080 {
    route /02b62aba-db1e-42a1-8278-9d778445794d/<id>/caddy/* {
        uri strip_prefix /02b62aba-db1e-42a1-8278-9d778445794d/<id>/caddy
        reverse_proxy localhost:30000
    }
}

```

```docker
caddy run Caddyfile
```

https://cloudinfra-services.ubilink.ai/02b62aba-db1e-42a1-8278-9d778445794d/<id>/caddy/v1

## Ray Cluster 建立

```bash
ray start --head 
ray start --head --port 6380

ray start --address='192.168.68.77:6379' # 填入前面的 IP

ray status
```

## 安裝 vLLM

```bash
pip install vllm

python3 -m vllm.entrypoints.openai.api_server --model deepseek-ai/DeepSeek-R1  --tensor-parallel-size 16 --max-model-len 8192 --max-num-seqs 32 --enforce-eager  --host 0.0.0.0 --port 8000 --trust-remote-code --gpu-memory-utilization 0.95

NCCL_P2P_DISABLE=1 python3 -m vllm.entrypoints.openai.api_server --model deepseek-ai/DeepSeek-R1  --tensor-parallel-size 16 --max-model-len 8192 --max-num-seqs 32 --enforce-eager  --host 0.0.0.0 --port 8000 --trust-remote-code  --gpu-memory-utilization 0.95
```

## 安裝 SGL

```bash
pip3 install datamodel_code_generator

pip3 install --no-cache-dir --upgrade pip setuptools wheel html5lib six

pip3 install --no-cache-dir torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu124

git clone --depth=1 https://github.com/sgl-project/sglang.git

cd sglang

pip3 --no-cache-dir install -e "python[all]" --find-links https://flashinfer.ai/whl/cu124/torch2.5/flashinfer/
```

## SGL 推論

### Node1

```bash
python3 -m sglang.launch_server --model deepseek-ai/DeepSeek-R1 --tp 16 --dist-init-addr <ib_ip>:6379 --nnodes 2 --node-rank 0 --trust-remote-code --host 0.0.0.0
```

### Node2

```bash
python3 -m sglang.launch_server --model deepseek-ai/DeepSeek-R1 --tp 16 --dist-init-addr <ib_ip>:6379 --nnodes 2 --node-rank 1 --trust-remote-code --host 0.0.0.0
```

## vLLM

```bash
VLLM_HOST_IP=<ip> python3 -m vllm.entrypoints.openai.api_server  --model deepseek-ai/DeepSeek-R1 --tensor-parallel-size 16 --max-model-len 8192 --max-num-seqs 32 --enforce-eager --host 0.0.0.0 --port 8000 --trust-remote-code

VLLM_USE_RAY_SPMD_WORKER=1 VLLM_USE_RAY_COMPILED_DAG=1 VLLM_HOST_IP=172.16.8.54 python3 -m vllm.entrypoints.openai.api_server     --model deepseek-ai/DeepSeek-R1     --tensor-parallel-size 16     --max-model-len 8192     --max-num-seqs 32     --enforce-eager     --host 0.0.0.0     --port 8000 --trust-remote-code
```