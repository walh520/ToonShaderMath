# ToonShaderMath

定制 UE 5.7.4 · Toon / Substrate Shader 节选

> 11 个公开 Shader 与当前本地 UE 引擎对应文件一致；完整引擎接入未公开，来源授权仍需核对。

[GitHub 仓库](https://github.com/walh520/ToonShaderMath)

## 简介与公开范围

当前仓库公开 11 个 USH 文件，位于 Engine/Shaders/Private 及 Substrate 子目录。内容涉及 Toon Profile 定义、Substrate Toon 数据、直接/间接光照和脸部、头发、眼睛、布料相关 Shader 路径。原快照没有 README，本页是依据文件目录与必要源码阅读建立的说明草稿。

这是局部 Shader 快照，不是完整 UE Toon 工程、独立插件或可直接编译的完整渲染分支。

## 本地工程与公开快照

已定位实际定制 UE 引擎，`Engine/Build/Build.version` 为 5.7.4。公开仓库的 11 个 Shader 与此引擎对应文件逐字节相同，也与本地 Toon-Source-Release 参考包相同。参考包用于辅助说明，不代替实际 Engine 的核对。

实际引擎还含 ToonProfile C++ 类型、Profile atlas 更新与材质/渲染集成。C++ 与 Shader 均声明 layout version 7、五种 Toon 类型及八个材质区域；这些引擎侧部分没有随当前 11 文件仓库完整公开。源码对应性不是本轮编译或画面验证。

## 演示

[Toon 作品演示](https://www.bilibili.com/video/BV11Sej6kEGi/) · [作品总集](https://www.bilibili.com/video/BV1MVak6jEPv/)

演示可包含完整工程的材质、模型、Profile 资源和引擎集成；不能用当前 11 个文件代表或复现视频的全部内容。

## 实现与贡献

本地归属文档把项目工作定位为定制 UE 集成：Toon Substrate 数据契约、Profile atlas 布局、五种类型分发、直接光顺序、间接光/transport helper、Face frame 与 Hair/Eye/Cloth 专用逻辑。实际引擎的类型定义与公开 Shader 可对应这些结构；本次不据此认定全部文件或基础算法均为个人原创。

GGX/Schlick、Kajiya–Kay、Charlie/Neubelt、八面体编码等保留既有方法来源。当前公开仓库没有随附这份本地归属/集成说明；仍需整理明确的公开来源与修改清单，并核对授权。

## 核心功能

以下为已核对的实现结构，未作完整运行验收声明：

- ToonProfileDefinitions/Common 定义并读取 Profile 与区域样式；与本地 C++ 的布局常量对应。
- SubstrateToon 建立 typed payload；Default Cel、Face、Hair、Eye、Cloth 是材质编译状态下的五种算法类型，不是“五类静态网格”。
- Direct 路径处理分层过渡、导数抗锯齿、阴影组合与调试输出。
- Face/FaceFrame 处理脸部坐标/SDF；Hair、Eye、Cloth 各有专用求值，不能只当目录占位。
- Indirect 与 Transport 分离间接光和物理传输近似；不能把栅格 Toon 色带效果直接宣称为 Ray/Path Tracing 完整艺术风格支持。

## 方案与取舍

| 实际组织 | 目的与边界 |
| --- | --- |
| Profile/区域样式与逐像素材质数据分开 | 类型、参数和资源归属明确；公开节选未包含全部 C++ 绑定。 |
| 五种静态算法类型与专用 payload | 适配脸、发、眼、布料的不同数据；不是任意多 closure 或所有材质域的通用兼容层。 |
| 栅格直接光风格化与 transport 分开 | 保留不同路径的求值语义；物理 transport fallback 不代表离线光追重现全部 Toon 分层。 |
| 深度接入匹配 UE Substrate | 依赖修改过的引擎代码；11 份 Shader 不能直接安装到官方 UE 获得完整功能。 |

## 代码阅读入口

1. [ToonProfileDefinitions.ush](Engine/Shaders/Private/ToonProfileDefinitions.ush) → [ToonProfileCommon.ush](Engine/Shaders/Private/ToonProfileCommon.ush)：先看共享约定。
2. [SubstrateToon.ush](Engine/Shaders/Private/Substrate/SubstrateToon.ush) → [SubstrateToonDirect.ush](Engine/Shaders/Private/Substrate/SubstrateToonDirect.ush)：再看 BSDF 入口和直接光。
3. [Face](Engine/Shaders/Private/Substrate/SubstrateToonFace.ush)、[FaceFrame](Engine/Shaders/Private/Substrate/SubstrateToonFaceFrame.ush)、[Hair](Engine/Shaders/Private/Substrate/SubstrateToonHair.ush)：阅读专用分支。
4. [其余 Substrate 文件](Engine/Shaders/Private/Substrate/)：Eye、Cloth、Indirect、Transport。

本说明只链接原仓库，不复制这些 Shader 源码。

## 验证与性能

本轮确认 11 个公开 Shader 与当前 UE 5.7.4 实际 Engine 文件相同，并核对了 C++/Shader 的类型与布局常量。未运行 UE 构建、ShaderCompileWorker、Editor/PIE、GPU Capture、目标材质场景或性能测试。

本地集成包说明也不宣称这些运行验收已完成。源码一致性、类型数量和支持路径清单均不等于视觉稳定性、兼容性或性能证据。

## 依赖与运行方式

实际来源对应定制 UE 5.7.4；本地集成说明以 Substrate、SM6/D3D12 与 Adaptive GBuffer 为目标，并依赖引擎侧材质节点、Profile 数据/资源、BSDF 编码及光照接入。

公开仓库只有 11 个 `.ush` 文件，缺少完整引擎修改、构建入口、工程资产和运行说明，不能从它独立构建或按普通插件安装。本地版本对应已核验；公开包完整性与运行验收仍需分别补齐。

## 限制与来源许可

已检查的关键文件带有 Epic Games 版权头。当前仓库未附 README、统一许可证或来源/修改清单；来源边界与公开授权仍需核对。本说明不作法律结论，也不把该快照标为开源。

在资料补齐前，保留原版权声明与原仓库链接，不把引擎内容包装为全部个人原创；交付包不包含该仓库源码副本。
