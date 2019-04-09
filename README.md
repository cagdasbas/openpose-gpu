# openpose-gpu
Turing optimized nvidia/cuda:10.1-cudnn7-runtime rebased openpose docker image

Install the nvidia docker hooks
```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-container-runtime/$distribution/nvidia-container-runtime.repo | \
  sudo tee /etc/yum.repos.d/nvidia-container-runtime.repo
sudo yum install -y nvidia-container-runtime-hook nvidia-container-runtime
sudo service docker restart
```
For nvidia only docker host run the following
```bash
sudo mkdir -p /etc/systemd/system/docker.service.d
sudo tee /etc/systemd/system/docker.service.d/override.conf <<EOF
[Service]
ExecStart=
ExecStart=/usr/bin/dockerd --host=fd:// --add-runtime=nvidia=/usr/bin/nvidia-container-runtime
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```
For general purpose docker host add the following to /etc/docker/daemon.json and restart the docker daemon

```yaml
{
  "runtimes": {
      "nvidia": {
        "path": "/usr/bin/nvidia-container-runtime",
        "runtimeArgs": []
      }
  }
}
```

Run the container with following command
```bash
docker run -it --rm --runtime=nvidia cagdasbas/openpose-gpu:10.1-cudnn7-runtime bash
```
