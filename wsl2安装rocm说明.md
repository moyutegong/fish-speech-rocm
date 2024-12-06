# wsl2安装rocm说明

- **首先下载并安装amdgpu-install：**

```
sudo apt update
wget https://repo.radeon.com/amdgpu-install/6.2.3/ubuntu/jammy/amdgpu-install_6.2.60203-1_all.deb
sudo apt install ./amdgpu-install_6.2.60203-1_all.deb
```

- **安装rocm组件**

```
amdgpu-install -y --usecase=wsl,rocm --no-dkms
```

- **修复mportError: version 'GLIBCXX_3.4.30' not found异常**

```
ln -sf /usr/lib/x86_64-linux-gnu/libstdc++.so.6 ${CONDA_PREFIX}/lib/libstdc++.so.6
```

- **安装pytorch**

```
pip3 install torch torchvision torchaudio numpy==1.26.4 --index-url https://download.pytorch.org/whl/rocm6.2
```

- **替换虚拟环境runtime lib**

```
location=`pip show torch | grep Location | awk -F ": " '{print $2}'`
cd ${location}/torch/lib/
rm libhsa-runtime64.so*
cp /opt/rocm/lib/libhsa-runtime64.so.1.2 libhsa-runtime64.so
```

- **测试是否安装成功**

```
python3 -c 'import torch' 2> /dev/null && echo 'Success' || echo 'Failure'
python3 -c 'import torch; print(torch.cuda.is_available())'
python3 -c "import torch; print(f'device name [0]:', torch.cuda.get_device_name(0))"
python3 -m torch.utils.collect_env
```