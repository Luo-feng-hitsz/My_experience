# Use Miniconda
- ### new

conda create --name luofeng python=3.8 创建名为luofeng并包含Python3.8的新运行环境

* ### copy
    conda create -n new_env --clone old_env

## Activate environment
    conda activate env
## Demonstrate all the env
    conda info --envs     查看所有运行环境
## Delete the env
    conda remove -n flowers --all
    删除运行环境之后可以通过命令查看运行环境是否删除： conda info -e
## Others
    pyhton --version
    conda list
    conda install pkg
    conda remove -n pkg --all