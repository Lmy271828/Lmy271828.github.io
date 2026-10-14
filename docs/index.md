# 欢迎 👋

这里是 Lmy271828 的个人站点,记录 FPGA / PYNQ / 机器视觉方向的学习与实验。

## 在写什么

- **FZ3B × PYNQ**:EdgeBoard FZ3B 开发板的系统移植、overlay 开发方法论与
  Jupyter notebook 教程(参见 [edgeboard 仓库](https://github.com/Lmy271828/edgeboard));
- **FPGA 图像处理**:HLS 内核、AXI DMA 流水线,以及视觉伺服(Visual Servoing)
  课程实践——ISP 算子逐级进 PL,伺服控制律留在 PS;
- **踩坑备忘**:硬件、网络、工具链的各种已解决问题存档。

!!! note "本站如何运作"
    站点由 [MkDocs Material](https://squidfunk.github.io/mkdocs-material/) 构建:
    源码在 GitHub 仓库 `main` 分支,push 后 Actions 自动构建发布,无需手工部署。

## 目录规划

| 栏目 | 内容 | 状态 |
|---|---|---|
| **PYNQnoob**（FZ3B 移植笔记） | 从零编译 PYNQ 镜像、刷写点亮、overlay 方法论、MMIO 配对 | **已开更**（[系列总览](fz3b/index.md)） |
| ISP 与视觉伺服 | D415 取流、HLS 算子、IBVS/PBVS | 规划中 |
| 备忘与工具 | 环境配置、板卡网络、调试技巧 | 规划中 |

写新文章时,在 `docs/` 下新建 Markdown 文件并加入 `mkdocs.yml` 的 `nav`
即可,Actions 会在 push 后自动发布。
