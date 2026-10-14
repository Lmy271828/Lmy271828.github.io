# 移植篇(二)：刷写镜像与首次点亮

上一篇我们从源码编译出了 `edgeboard-fz3b-3.1.2.img`（约 9.1GB）。这一篇把它灌进
TF 卡，让板子真正跑起来——串口连上、网络打通、浏览器打开属于你自己的 Jupyter。

## 0. 开工前检查清单

- [ ] 篇(一) 的产物：`~/pynq/sdbuild/output/edgeboard-fz3b-3.1.2.img`（约 9.1GB）
- [ ] TF 卡（16GB 及以上）+ 读卡器
- [ ] 网线一根（板载网口 ↔ PC 网口）
- [ ] USB 转串口线（CP2102，接板上调试口）
- [ ] Windows 侧已装 [balenaEtcher](https://etcher.balena.io/) 和一个串口终端
      （MobaXterm / PuTTY 均可）

## 1. 把镜像从 WSL 拷到 Windows

Etcher 这类工具直接读 `\\wsl$` 路径容易出幺蛾子，先落到 Windows 本地盘：

```bash
cp ~/pynq/sdbuild/output/edgeboard-fz3b-3.1.2.img /mnt/d/
```

拷完核对一下大小（约 9.67 GB 字节数），避免拿着半截文件刷半天：

```powershell
(Get-Item D:\edgeboard-fz3b-3.1.2.img).Length
```

## 2. 刷写 TF 卡

balenaEtcher → **Flash from file** 选 `D:\edgeboard-fz3b-3.1.2.img` → 目标选你的
TF 卡（认准容量，别把移动硬盘刷了）→ **Flash**。

三个必知：

- 写完 Windows 会弹"需要格式化驱动器"——镜像里的 rootfs 是 ext4，Windows 不认识，
  **一律点取消，千万别格式化**；
- Etcher 结束时会自动做一遍 **Validate**（读回比对），等它走完再拔卡；
- 想人肉复核的话：卡插回读卡器，Windows 能看到的那个 FAT 分区里有个 `REVISION`
  文件，打开应看到 `Release 3.1.2 (Carlisle) 2026_10_13 3c77124 ...`——这就是你
  这次构建的身份信息。

## 3. 连线与上电

| 连接 | 说明 |
|---|---|
| TF 卡 → 板卡卡槽 | 断电状态插入 |
| 网线 | 板载网口 ↔ PC 网口（ICS 组网，见篇(一) §8.1） |
| USB 转串口线 | 板上调试口 ↔ PC（CP2102） |
| 电源 | 最后接通 |

上电后等 1~2 分钟，板载 LED 和串口会有动静。

## 4. 串口：第一次看见板子的内心

**step1**：设备管理器 → 端口(COM 和 LPT) → 记下 `Silicon Labs CP210x` 的 COM 号。

**step2**：MobaXterm → Session → **Serial** → 选 COM 号，Speed `115200`，
**Flow control 改为 None**（软件流控会莫名冻住输出），OK。

**step3**：按几下回车唤出登录提示，登录 `xilinx / xilinx`。

进去之后先看一眼网络接口：

```bash
ip -br addr
```

预期看到 `eth0`（板载网口）处于 UP 状态，且已有 ICS 网段的 IPv4 地址。
此时如果想看完整开机日志，按板子复位键即可——串口全程可见 U-Boot 横幅与
内核日志（FSBL/PMU 的输出走了另一颗未接线的 UART，看不到属正常，见篇(一) §10 坑 7）。

## 5. 网络：ICS 已就位，找 IP

篇(一) §8.1 已在 Windows 侧开启 ICS。板卡开机后会通过 DHCP 自动拿到
`192.168.137.x` 网段的地址：

```powershell
arp -a | findstr 192.168.137
```

`dynamic` 条目（排除 `.1` 和 `.255`）就是板卡。实测本次拿到了 **192.168.137.153**。

浏览器打开：

```
http://192.168.137.153:9090
```

密码 `xilinx`——**你的 Jupyter 就绪**。

!!! tip "IP 又变了？"
    ICS 是 DHCP 动态发 IP，每次重启可能变化。嫌麻烦可以在板子里把 eth0 配成静态
    地址，或每次开工先跑一遍 `arp -a`。

## 6. 终极验证：import pynq

Jupyter 里新建一个 Notebook：

```python
import pynq
print(pynq.__version__)     # → 3.1.2
```

显示 **3.1.2** 说明：编译链 → 镜像 → TF 卡 → 板卡 → Python 全链贯通，
且**板上跑的正是你亲手编译的版本**（原作者预编译镜像为 3.0.1）。

顺手看一眼板上的家底：`/home/xilinx/` 下有 `jupyter_notebooks/`（官方入门
notebook）和 `pynq/`（PYNQ 源码树），后续篇目的实验都在这套环境里进行。

## 7. 首启排错速查

| 现象 | 去处 |
|---|---|
| 串口只有三行 BL31 后静默 | 篇(一) §10 坑 7（串口配置分裂） |
| 内核日志可见但 `Unable to mount root fs on unknown-block(179,10)` | 篇(一) §10 坑 6（root= 指向 eMMC，§6.3 已预防） |
| Windows 提示格式化 TF 卡 | 正常现象，点取消（§2） |
| arp 里没有板卡 | 篇(一) §8.1 检查 ICS 绑定的适配器方向 |

## 8. 收尾与预告

到这里，"从零编译 → 刷写 → 点亮"全链完成。板上的 eMMC（7.28GB）还闲着，
后续可以探讨把系统装进 eMMC、彻底解放 TF 卡槽；下一篇我们加载第一个
overlay，开始真正驱动 PL 侧的硬件。
