# PYNQnoob：FZ3B 上的 PYNQ 从零实践

**PYNQnoob** 是一个面向"零基础在校学生"的 PYNQ 实战系列：拿一块百度
EdgeBoard FZ3B（Zynq UltraScale+ ZU3EG），从**亲手编译 PYNQ 镜像**开始，
一步步走到刷写点亮、驱动 PL 硬件。

不做凉菜，只上热菜——每一步都有实测日志、真实报错和对应的解法。

## 篇目

| 篇 | 标题 | 内容 | 状态 |
|---|---|---|---|
| 移植篇(一) | [Windows 11 从头编译 PYNQ v3.1.2 镜像](01-build-image.md) | WSL2 + Docker 编译环境、AMD 工具链安装、FZ3B 板卡文件接入、七连坑实录 | ✅ 已发布 |
| 移植篇(二) | [刷写镜像与首次点亮](02-flash-and-boot.md) | balenaEtcher 刷卡、串口调试、ICS 组网、Jupyter 点亮 | ✅ 已发布 |
| 移植篇(三) | 加载第一个 overlay | MMIO 与 PL 侧硬件的第一次握手 | 🚧 规划中 |
| 移植篇(四) | HLS 内核初体验 | 让软件工程师写"硬件" | 🚧 规划中 |

## 软硬件环境

| 类别 | 项目 |
|---|---|
| 板卡 | 百度 EdgeBoard FZ3B（Zynq UltraScale+ ZU3EG，ALINX 制造） |
| 镜像 | 自编译 PYNQ v3.1.2（PetaLinux 2024.1，内核 6.6.10-xilinx） |
| 宿主 | Windows 11 + WSL2 Ubuntu 24.04 + Docker |
| 工具链 | Vivado/Vitis/PetaLinux 2024.1 |

## 致谢

FZ3B 的 PYNQ 移植由 [Haozhe Zhu（@zhutmost）](https://github.com/zhutmost)开创、
[Changhai（@changhai0109）](https://github.com/changhai0109)贡献 FZ3B 板卡文件
与编译流程，本系列基于 [zhutmost/edgeboard](https://github.com/zhutmost/edgeboard)
（文档 CC-BY 4.0 / 代码 MIT）展开。原作者的
[《EdgeBoard 的 PYNQ 移植》](https://zhutmost.com/post/pynq-compile)与
[《PYNQ v3.1 编译的 Debug 之旅》](https://zhutmost.com/post/pynq-compile-2)
两篇博文是本系列的重要参考。

> 声明：本系列为个人学习笔记，与百度官方、AMD 官方均无关系；项目采用
> [CC-BY 4.0](https://creativecommons.org/licenses/by/4.0/) 发布。
