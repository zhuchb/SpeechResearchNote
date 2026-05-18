# 关于ms-swift的环境配置

## torch版本问题
按照官方github的命令安装。
```bash
pip install ms-swift
```
但是这个命令会强制给我按一个和cuda环境不匹配的torch版本，就会出现device_map: CPU，模型没有加载到cuda环境上。解决办法是卸载torch，重新安装torch。我这里的环境按照下面的命令安装即可。
```bash
pip3 install torch torchvision --index-url https://download.pytorch.org/whl/cu126
```

## RL时vllm不是必须的
运行swift rlhf，会出现vllm包不存在的报错。一开始我啥也没想，直接就pip了，然后出现**惊天大坑**。

直接pip install vllm，会重新安装torch以及其他各种依赖的版本。表面看起来一片和谐，包也安装好了。重新infer sft训练好的节点，发现整个输出和安装vllm之前不一样了，训练好的模型的能力消失了。

其实vllm不是swift rlhf必须的，但是它会自动加载，下面是绕过它的办法。

找到你的环境下面这个文件
```bash
xxx//anaconda3/envs/new-swift/lib/python3.12/site-packages/swift/rollout/multi_turn.py
```

修改第8行的依赖。把GRPOVllmENgine注释掉即可。
```python
#from swift.infer_engine import GRPOVllmEngine
from typing import TYPE_CHECKING

if TYPE_CHECKING:
    from swift.infer_engine import GRPOVllmEngine
else:
    GRPOVllmEngine = object
```