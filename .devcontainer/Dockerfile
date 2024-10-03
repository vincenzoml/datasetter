FROM nvcr.io/nvidia/pytorch:24.08-py3

RUN apt update
RUN apt -y dist-upgrade
RUN apt -y install git git-lfs python3 python3-pip htop screen unzip psmisc nvtop wget libgl-dev libegl-dev libfreetype-dev libglib2.0-0 ca-certificates curl gnupg ffmpeg net-tools

RUN curl -fsSL https://deb.nodesource.com/setup_22.x -o nodesource_setup.sh
RUN bash ./nodesource_setup.sh
RUN apt -y install nodejs
RUN corepack enable

# RUN mkdir /python
# RUN export PATH=$PATH:/python/.local/bin
# ENV PYTHONPATH=$PYTHONPATH:/python

# RUN python -m pip install --upgrade pip
# RUN pip install --target=/python safetensors opencv-python-headless opencv-contrib-python torch torchsde transformers safetensors accelerate scikit-image numexpr lpips imageio-ffmpeg segment_anything timm addict yapf insightface onnxruntime openmim  GitPython python-color-transfer pydub datasets pilgram spandrel kornia piexif omegaconf diffusers ultralytics gitpython torchaudio sentencepiece

RUN apt -y install telnet

RUN adduser datasetter
USER datasetter
