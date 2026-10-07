# ToonShaderMath

定制 UE 5.7.4 · Toon / Substrate Shader 节选

> 围绕 Toon Profile、光照分层与角色材质，组织五类风格化着色路径。

[GitHub 仓库](https://github.com/walh520/ToonShaderMath)

## 项目简介

基于定制 UE 5.7.4 的 Toon / Substrate 着色实现。本仓库收录 11 个 USH 文件，覆盖 Toon Profile 定义、Substrate Toon 数据、直接/间接光照，以及脸部、头发、眼睛和布料的专用 Shader。

公开内容为 Shader 节选。完整运行还需要配套的引擎侧材质节点、Profile 资源、BSDF 编码和光照接入。

## 演示

[Toon 作品演示](https://www.bilibili.com/video/BV11Sej6kEGi/) · [作品总集](https://www.bilibili.com/video/BV1MVak6jEPv/)

视频使用完整工程的材质、模型、Profile 资源和定制引擎；本仓库提供其中的核心 Shader 阅读入口。

## 实现与贡献

项目工作聚焦 Toon 与 UE Substrate 的集成：设计 Toon 数据契约和 Profile atlas 布局，组织五类着色分发、直接光分层、间接光与 transport helper，并实现 Face frame 及 Hair/Eye/Cloth 专用逻辑。

着色模型使用 GGX/Schlick、Kajiya–Kay、Charlie/Neubelt 和八面体编码等已有方法。

## 核心功能

- ToonProfileDefinitions/Common 定义并读取 Profile 与区域样式，布局版本为 7，包含八个材质区域。
- SubstrateToon 建立 typed payload，按 Default Cel、Face、Hair、Eye、Cloth 五种材质编译类型分发。
- Direct 路径处理光照分层过渡、导数抗锯齿、阴影组合与调试输出。
- Face/FaceFrame 处理脸部坐标和 SDF；Hair、Eye、Cloth 提供专用着色求值。
- Indirect 与 Transport 分别处理间接光和物理传输近似。

## 方案与取舍

| 选择 | 作用 |
| --- | --- |
| Profile/区域样式与逐像素材质数据分开 | 明确类型、参数和资源归属，便于复用角色材质风格。 |
| 五种静态算法类型与专用 payload | 适配脸、发、眼、布料各自的数据和着色需求。 |
| 栅格直接光与 transport 分开 | 分别组织风格化分层与物理传输求值。 |
| 接入定制 UE Substrate | 与引擎材质编译、BSDF 和光照路径协作。 |

当前公开文件以栅格 Toon 着色为主要阅读路径。Ray/Path Tracing 的 transport fallback 采用物理传输近似，完整 Toon 分层的离线重现仍需进一步集成与验证。

## 代码阅读入口

1. [ToonProfileDefinitions.ush](Engine/Shaders/Private/ToonProfileDefinitions.ush) → [ToonProfileCommon.ush](Engine/Shaders/Private/ToonProfileCommon.ush)：共享定义与 Profile 读取。
2. [SubstrateToon.ush](Engine/Shaders/Private/Substrate/SubstrateToon.ush) → [SubstrateToonDirect.ush](Engine/Shaders/Private/Substrate/SubstrateToonDirect.ush)：BSDF 入口与直接光。
3. [Face](Engine/Shaders/Private/Substrate/SubstrateToonFace.ush)、[FaceFrame](Engine/Shaders/Private/Substrate/SubstrateToonFaceFrame.ush)、[Hair](Engine/Shaders/Private/Substrate/SubstrateToonHair.ush)：角色材质专用分支。
4. [其余 Substrate 文件](Engine/Shaders/Private/Substrate/)：Eye、Cloth、Indirect、Transport。

## 验证状态

公开 Shader 与定制 UE 5.7.4 对应文件一致，类型与布局常量已有源码核对记录。UE 构建、ShaderCompileWorker、Editor/PIE、GPU Capture、目标材质场景和性能测试尚待完成。

## 依赖与集成

目标配置为定制 UE 5.7.4、Substrate、SM6/D3D12 与 Adaptive GBuffer。集成需要引擎侧材质节点、Profile 数据与资源、BSDF 编码和光照接入。

这 11 个 `.ush` 文件用于阅读着色实现；独立运行需要完整引擎修改、构建入口和工程资产。

## 来源与许可

相关文件保留 Epic Games 版权声明，着色算法参考见上文。仓库暂未提供统一 LICENSE，完整来源与修改清单仍待补充。
