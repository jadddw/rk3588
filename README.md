  # 智能水产养殖多模态感知与移动交互系统

  ## 1. 项目简介
  本项目面向智慧水产养殖场景，构建了一套集环境感知、视觉识别、网页管理、手机端交互、云台控制与知识问答于一体的嵌入式应用
  系统。

  系统以 RK3588 板端为核心运行平台，完成以下主要功能：

  - 采集并展示温度、pH、浊度等环境参数
  - 通过摄像头进行人脸检测与人脸识别
  - 统一管理账户、照片与打卡状态
  - 提供网页端视频页、传感器页、人员管理页和知识介绍页
  - 提供手机端登录、打卡、状态交互与动作反馈
  - 通过串口桥接下位机传感器数据
  - 通过本地知识库与智能体服务实现项目讲解与问答

  本项目的软件结构采用“板端核心服务 + 网页前端 + 手机端应用 + 串口桥接”的分层设计，适合用于比赛展示、课程设计、原型验证
  和后续功能扩展。

  ---

  ## 2. 项目组成

  本项目当前建议按两部分管理与上传：

  ### 2.1 板端源码
  来源目录：

  ```text
  custom-monitor-facework

  主要包含：

  - distributed_preview/
  - static/
  - knowledge_base/

  说明：

  - distributed_preview/ 为板端核心 Python 服务目录
  - static/ 为网页前端资源
  - knowledge_base/ 为本地知识库与项目说明内容

  ### 2.2 手机端源码

  来源压缩包：

  rk_app_control_work.zip

  当前手机端上传包为精简源码包，仅包含：

  - lib/
  - android/
  - test/
  - pubspec.yaml

  说明：

  该压缩包用于提交核心手机端源码，不包含构建缓存、APK、IDE 配置等非必要文件。

  ———

  ## 3. 系统功能概述

  ### 3.1 板端功能

  板端负责整个系统的核心运行与状态管理，主要包括：

  - 网页服务分发
  - 视频采集与视觉状态管理
  - 人脸识别与账户联动
  - 传感器串口数据接入
  - 打卡状态与账户状态维护
  - 云台相关接口
  - 本地知识问答与智能体接口

  ### 3.2 网页端功能

  网页端主要页面包括：

  - index.html：系统主页
  - video.html：视频与云台页面
  - sensors.html：传感器状态页面
  - attendance.html：账户与打卡状态页面
  - add_person.html：人员录入页面
  - knowledge.html：项目介绍页面
  - llm.html：问答与智能交互页面

  ### 3.3 手机端功能

  手机端主要功能包括：

  - 登录
  - 打卡
  - 视频状态查看
  - 云台控制相关交互
  - 智能问答交互
  - 状态连接与配置

  ### 3.4 传感器功能

  当前系统已接入下位机串口输出的三类环境参数：

  - 温度
  - pH
  - 浊度

  板端串口桥接程序会解析文本格式串口数据，并写入板端状态接口供网页端展示。

  ———

  ## 4. 推荐目录结构

  板端部署建议目录：

  /root/custom-monitor

  推荐结构如下：

  /root/custom-monitor/
    distributed_preview/
    static/
    knowledge_base/
    models/
    runtime/

  说明：

  - distributed_preview/：Python 服务、运行脚本、systemd 文件、串口桥接
  - static/：网页前端资源
  - knowledge_base/：项目知识库
  - models/：视觉识别与推理模型
  - runtime/：账户库、人脸图库、上传目录等运行时数据

  ———

  ## 5. 板端依赖说明

  ## 5.1 系统环境建议

  建议板端环境为：

  - Ubuntu / Debian 类 Linux
  - Python 3.10
  - systemd
  - ffmpeg
  - v4l2-ctl
  - curl

  ## 5.2 系统级依赖安装

  建议安装以下依赖：

  sudo apt update
  sudo apt install -y python3 python3-pip ffmpeg v4l-utils curl

  ## 5.3 Python 依赖

  根据当前源码，建议安装以下 Python 包：

  pip3 install numpy pyserial joblib scipy opencv-python onnxruntime

  如果启用 NPU / RKNN / RKLLM 相关功能，还需要安装板端实际环境中的以下组件：

  - rknnlite
  - Rockchip NPU 运行库
  - 可选：rktransformers
  - 对应的 RKLLM 服务环境

  说明：

  部分依赖不是单纯 pip install 即可解决，尤其是 RKNN 和 RKLLM，通常需要板端提前具备对应运行环境。

  ———

  ## 6. 模型文件说明

  模型建议统一放置在：

  /root/custom-monitor/models

  当前工程涉及的主要模型包括：

  /root/custom-monitor/models/RetinaFace_resnet50_320_int8.rknn
  /root/custom-monitor/models/face_recognition_sface_2021dec.onnx
  /root/custom-monitor/models/emotion_enet_b0_8_best_vgaf_fp.rknn
  /root/custom-monitor/models/emotion_labels_8.txt
  /root/custom-monitor/models/yolo_world_v2s.rknn

  可选模型：

  /root/custom-monitor/models/yolo11n_rkopt_int8.rknn
  /root/custom-monitor/models/face_detection_yunet_2023mar.onnx
  /root/custom-monitor/models/face_detection_yunet_2023mar_int8.rknn

  如果相关模型文件不存在，则对应功能会降级或不可用。

  ———

  ## 7. 运行时数据路径

  当前运行时涉及的重要路径包括：

  账户库：

  /root/custom-monitor/runtime/account_store/accounts.json

  人脸图库：

  /root/custom-monitor/runtime/face_gallery

  上传目录：

  /root/custom-monitor/distributed_preview/runtime/uploads

  运行配置文件：

  /root/custom-monitor/distributed_preview/runtime/split.env

  建议首次部署时创建：

  sudo mkdir -p /root/custom-monitor/runtime/account_store
  sudo mkdir -p /root/custom-monitor/runtime/face_gallery
  sudo mkdir -p /root/custom-monitor/distributed_preview/runtime/uploads

  ———

  ## 8. 板端运行配置说明

  核心配置文件：

  /root/custom-monitor/distributed_preview/runtime/split.env

  该文件用于配置以下内容：

  - 摄像头路径
  - 图像采集分辨率与帧率
  - 视频翻转与旋转参数
  - 云台串口
  - 传感器串口
  - 人脸识别模型路径
  - 情绪识别模型路径
  - 各服务超时参数
  - LLM 与智能体相关参数

  ### 8.1 摄像头配置

  示例配置：

  VISION_CAMERA=/dev/v4l/by-path/platform-fc880000.usb-usb-0:1.3:1.0-video-index0
  VISION_WIDTH=1280
  VISION_HEIGHT=720
  VISION_FPS=120
  VISION_FOURCC=MJPG
  VISION_PIPELINE=ffmpeg
  VISION_FLIP=2
  VISION_ROTATE=180

  ### 8.2 云台串口配置

  示例配置：

  VISION_GIMBAL_SERIAL_PORT=/dev/ttyS9
  VISION_GIMBAL_SERIAL_BAUD=1152000

  ### 8.3 传感器串口配置

  示例配置：

  VISION_SENSOR_SERIAL_PORT=/dev/ttyUSB0
  VISION_SENSOR_SERIAL_BAUD=115200
  VISION_SENSOR_PROTOCOL=custom_ascii
  VISION_SENSOR_SOURCE=lower-mcu-1
  VISION_SENSOR_API_BASE=http://127.0.0.1:8090
  VISION_SENSOR_API_PATH=/api/vision/sensors/sample

  ### 8.4 人脸识别配置

  示例配置：

  VISION_FACE_RECOGNITION_MODEL=/root/custom-monitor/models/face_recognition_sface_2021dec.onnx
  VISION_FACE_RECOGNITION_GALLERY_DIR=/root/custom-monitor/runtime/face_gallery
  VISION_FACE_RECOGNITION_THRESHOLD=0.62

  ———

  ## 9. 板端端口说明

  当前默认服务端口如下：

  - GUI 网页入口：8090
  - LLM 服务：8091
  - Vision 服务：8092
  - Agent 服务：8093
  - RKLLM 上游服务：8080

  常用访问入口示例：

  http://192.168.197.169:8090
  http://192.168.197.169:8090/video.html
  http://192.168.197.169:8090/sensors.html
  http://192.168.197.169:8090/attendance.html
  http://192.168.197.169:8090/add_person.html

  ———

  ## 10. 板端启动说明

  ## 10.1 主服务启动

  板端主服务使用 systemd 管理，服务名为：

  custom-monitor.service

  常用命令：

  sudo systemctl daemon-reload
  sudo systemctl enable custom-monitor.service
  sudo systemctl restart custom-monitor.service
  sudo systemctl status custom-monitor.service --no-pager

  主服务启动后，应能拉起以下组件：

  - GUI 服务
  - Vision 服务
  - Agent 服务
  - LLM 服务
  - 视频采集相关进程

  ## 10.2 传感器桥接服务启动

  传感器桥接服务名为：

  custom-monitor-sensor-bridge.service

  常用命令：

  sudo systemctl daemon-reload
  sudo systemctl enable custom-monitor-sensor-bridge.service
  sudo systemctl restart custom-monitor-sensor-bridge.service
  sudo systemctl status custom-monitor-sensor-bridge.service --no-pager

  查看日志：

  sudo journalctl -u custom-monitor-sensor-bridge.service -n 80 --no-pager

  ———

  ## 11. 串口接入说明

  ## 11.1 传感器串口

  当前默认传感器串口为：

  /dev/ttyUSB0

  波特率：

  115200

  设备检查命令：

  ls -l /dev/ttyUSB* /dev/ttyACM* 2>/dev/null

  测试串口数据：

  stty -F /dev/ttyUSB0 115200
  timeout 5 cat /dev/ttyUSB0

  当前桥接程序兼容如下格式的真实串口文本：

  temp=25.18 C, turbidity_adc=53, turbidity_mv=86, turbidity_level=dirty, ph_adc=1624, ph_po_mv=2618, pH=6.35,
  ph_level=acidic

  桥接程序会自动忽略非样本提示行，例如：

  [USART2 received data]
  Three-sensor test ready
  USART1 (PA9/PA10) + USART2 (PD5/PD6) both active

  ## 11.2 云台串口

  当前默认云台串口为：

  /dev/ttyS9

  波特率：

  1152000

  如果板子型号、串口映射或外设连接变化，需要同步修改 split.env。

  ———

  ## 12. 板端健康检查

  主服务健康检查：

  curl -sS http://127.0.0.1:8090/health

  视觉配置检查：

  curl -sS http://127.0.0.1:8090/api/vision/profile

  账户状态检查：

  curl -sS http://127.0.0.1:8090/api/vision/accounts

  传感器状态检查：

  curl -sS http://127.0.0.1:8090/api/vision/sensors/status

  ———

  ## 13. 手机端部署说明

  ## 13.1 手机端环境要求

  建议环境：

  - Flutter SDK >=3.3.0 <4.0.0
  - Android Studio 或 Flutter 命令行环境
  - 已配置 Android SDK
  - 已连接 Android 手机或可用模拟器

  ## 13.2 手机端源码内容

  当前手机端精简源码包只包含：

  - lib/
  - android/
  - test/
  - pubspec.yaml

  这是当前允许上传的最小必要 Android 源码集合。

  ## 13.3 Flutter 依赖

  当前 pubspec.yaml 中主要依赖如下：

  - dio
  - flutter_riverpod
  - intl
  - image_picker
  - shared_preferences
  - cupertino_icons

  安装依赖：

  flutter pub get

  建议检查：

  flutter analyze

  ## 13.4 手机端构建

  在手机端工程根目录执行：

  flutter pub get
  flutter build apk --debug

  如果只做静态检查：

  flutter analyze

  ## 13.5 APK 生成路径

  构建完成后，APK 默认位于：

  build/app/outputs/flutter-apk/app-debug.apk

  ## 13.6 安装到手机

  使用 ADB 安装：

  adb install -r build/app/outputs/flutter-apk/app-debug.apk

  如果需手动安装，也可以将 APK 拷入手机后直接安装。

  ———

  ## 14. 手机端连接说明

  当前手机端默认板端地址为：

  http://192.168.197.169:8090

  手机端主要访问的接口包括：

  - /api/vision/frame.jpg
  - /api/vision/observe
  - /api/vision/accounts/session
  - /api/vision/accounts/checkin
  - /api/agent/...

  如果现场 IP 地址变化，需要同步修改手机端设置或源码中的默认地址。

  ———

  ## 15. 代码上传建议

  ## 15.1 建议上传内容

  ### 板端

  建议上传整个板端源码目录：

  custom-monitor-facework

  重点保留：

  - distributed_preview/
  - static/
  - knowledge_base/

  ### 手机端

  当前建议上传精简源码包对应内容：

  - lib/
  - android/
  - test/
  - pubspec.yaml

  ## 15.2 不建议上传内容

  板端不要上传：

  - __pycache__/
  - 运行时缓存
  - 上传缓存
  - 实际账户数据
  - 实际人脸图库照片
  - 板端临时状态文件

  手机端不要上传：

  - build/
  - .dart_tool/
  - .idea/
  - APK
  - 本地缓存文件

  ———

  ## 16. 当前代码包完整性提示

  当前手机端精简源码包适合用于：

  - 比赛源码提交
  - GitHub 上传
  - 报告引用
  - 核心功能复现

  当前板端源码包应重点确认以下文件是否齐全：

  - distributed_preview/ipc_http.py
  - distributed_preview/gui_service.py
  - distributed_preview/llm_service.py
  - distributed_preview/scripts/start_split_stack.sh
  - distributed_preview/scripts/stop_split_stack.sh

  如果缺少上述文件，板端服务可能无法完整启动。

  ———

  ## 17. 已知说明

  1. 当前系统的软件部分已经打通板端服务、网页端、手机端和传感器串口链路。
  2. 当前传感器桥接程序已兼容真实下位机文本协议。
  3. 当前手机端上传包是按“单文件不超过 25MB”的限制精简后的版本。
  4. 如果后续需要完整恢复手机端工程，可在此基础上补回 pubspec.lock、README.md、analysis_options.yaml 等文件。
  5. 如果后续需要完整恢复板端部署环境，除源码外还需补齐模型文件、RKNN/RKLLM 运行环境和实际运行脚本。

  ———