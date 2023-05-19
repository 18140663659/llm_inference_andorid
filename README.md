# llm_inference_andorid


# 主要说明chatglm的android部署

  1. 使用fastllm做端侧推理:https://github.com/ztxz16/fastllm  
    1.1 源码编译pc和android版本  
    1.2 导出chatglm的量化版本：  
        python3 chatglm_export.py ../chatglm-6b.bin  
        ./quant -m chatglm -p ../chatglm-6b.bin -o ../chatglm-6b-int4.bin -b 4  
    1.3 安装termux：https://www.jianshu.com/p/2b7c0b7ac57d  
    1.4 将main和模1.2步骤导出的模型放置到可运行的位置，使用android studio放在/data/data/com.termus/home目录下  
    1.5 在termux命令行运行./main -m chatglm -p chatglm-6b-int4.bin  
