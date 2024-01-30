# nemo_test

- ⾸先通过链接 https://developer.nvidia.com/nemo-framework/join 申请NeMo Framework Container的GA。提交申请后应该很快就能⾃动获得通过。

## BF16 pretraining

- 在提交训练任务的节点拉取 https://github.com/NVIDIA/NeMo-Megatron-Launcher/
- 参照 https://github.com/Wong4j/NeMo-Megatron-Launcher/commit/a1a7eb0c195064422a01453dbd6b6490800ba5de 修改配置，需要注意的点有,在 launcher_scripts/conf/config.yaml 设置 defaults.cluster 和 cluster_type: slurm集群填写 bcm, k8s集群填写 k8s。
设置集群相关的信息（因为涉及到集群的信息，该commit不包含）：slurm集群修改 launcher_scripts/conf/cluster/bcm.yaml， k8s集群修改 launcher_scripts/conf/cluster/k8s.yaml。若使用wandb可视化训练结果，需要将wandb的key写入一个文件，并在 launcher_scripts/conf/config.yaml 的 wandb_api_key_file 字段指定其路径。若不使用wandb，需要将 launcher_scripts/conf/training/gpt3/175b.yaml 的 exp_manager.create_wandb_logger 设为 False 。
其他修改参照该commit进行即可。

## Dataset preparation
将 launcher_scripts/conf/config.yaml 的 stage 设置为 data_preparation。在 /path/to/NeMo-Megatron-Launcher/launcher_scripts 目录下执行
```bash
python3 main.py
```
即可完成Pile数据集0-1分块的下载和预处理。

## Submit training job
在数据准备好之后，将 launcher_scripts/conf/config.yaml 的 stage 设置为 training。在 /path/to/NeMo-Megatron-Launcher/launcher_scripts 目录下执行
```bash
python3 main.py
```
即可提交训练任务。

## Check the training results
- 若使用wandb，则可在wandb的网站上看到训练结果，其中 train_step_timing in s 是每个step的时间。
- 若没有使用wandb，则可以在训练log里查找 train_step_timing in s 字段


## FP8 pretraining
在 BF16 pretrain 的基础上，参考 https://github.com/Wong4j/NeMo-Megatron-Launcher/commit/a1a7eb0c195064422a01453dbd6b6490800ba5de 修改launcher_scripts/conf/training/gpt3/175b_fp8.yaml即可进行FP8训练。
