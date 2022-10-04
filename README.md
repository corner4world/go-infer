# Go实现的模型推理和API部署框架

深度学习模型在部署时通常以云方式部署，通过API对外提供推理服务。这个框架提供了部署API时的基本架构组件，实现了几个目标：
- API处理模块与模型推理模块解耦，降低高并发造成的网络和计算阻塞风险
- API处理模块与模型推理模块可进行分布式部署，均可实现横向扩展
- 使用Go语言实现，提高执行效率，简化部署和运维
- 业务逻辑使用callback方式调用，隐藏通用逻辑，开发时只需关注业务逻辑



其他功能：

- 服务端配置使用Yaml，分布式部署时可进行针对性配置
- API验签支持SHA256和SM2算法
- 模型示例
  - [BERT模型推理示例](examples/models/embedding)
  - [CNN模型推理示例](examples/models/mobilenet)
  - [Tensorflow模型权重转换示例](examples/export/export_tf_bert.py)
  - [Keras模型权重转换示例](examples/export/export_keras_cnn.py)
  - [ONNX模型权重推理示例](examples/models/facedet)
  - [PyTorch模型转换为ONNX格式示例](examples/export/pytorch_to_onnx.py)



## 分布式部署架构

<img src="doc/arch.png" alt="分布式部署架构" width="300" />



## 开发文档

1. [开发指南](doc/DEV.md)
2. [API文档模板](doc/API.md)
3. [框架测试](doc/TEST.md)
4. [Tensorflow运行环境](doc/TF.md)
