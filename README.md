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
  
  2. 端侧代码开发
    2.1 下载最新android studio、配置好sdk和ndk  
    2.2 File -> new project -> native C++  
    2.3 将fastllm中的include、src、mian.cpp目录拷贝到main->cpp目录  
    2.4 编写CMakeLists.txt  
		cmake_minimum_required(VERSION 3.5)  

		set(INCLUDE_DIR ${CMAKE_SOURCE_DIR}/include)  
		set(SRC_DIR ${CMAKE_SOURCE_DIR}/src)  

		FILE(GLOB INCLUDE_DIR_FILES ${INCLUDE_DIR}/**)  
		FILE(GLOB SRC_DIR_FILES ${SRC_DIR}/**)  


		include_directories(${INCLUDE_DIR})  
		include_directories(${SRC_DIR})  


		add_library( # Sets the name of the library.  
					 llm  

					 # Sets the library as a shared library.  
					 SHARED  

					 # Provides a relative path to your source file(s).  
					 ${SRC_DIR_FILES}  
					 main.cpp  
					 native-lib.cpp )  

		find_library( # Sets the name of the path variable.  
					  log-lib  

					  # Specifies the name of the NDK library that  
					  # you want CMake to locate.  
					  log )  

		target_link_libraries( # Specifies the target library.  
							   llm  

							   # Links the target library to the log library  
							   # included in the NDK.  
							   ${log-lib} )  
    2.5 将chatglm-6b-int4.bin上传至/data/data/your_project_name/  
    2.5 编写jni相关代码，最简情况可以如下:  

		RunConfig config;  
		config.model = "chatGLM";  
		config.path = "/data/data/your_project_name/chatglm-6b-int4.bin";  
		config.threads = 4;  
		fastllm::SetThreads(config.threads);  
		fastllm::ChatGLMModel chatGlm;  
		chatGlm.LoadFromFile(config.path);  
		printf("%s", "加载完成");  

		//进行模型推理  
		std::string prompt = "一年四节是哪四季";  
		std::string ret = chatGlm.Response(prompt);  
		printf("%s", ret.c_str());  


		std::string hello = "Hello from C++";  
		return env->NewStringUTF(ret.c_str());  
