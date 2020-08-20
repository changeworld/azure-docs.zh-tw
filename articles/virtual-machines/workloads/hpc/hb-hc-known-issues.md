---
title: HB 系列和 HC 系列 Vm 的已知問題-Azure 虛擬機器 |Microsoft Docs
description: 瞭解 Azure 中的 HB 系列 VM 大小已知問題。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/19/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 6316bcc91bb381facb4f77b2d8dbd8b22f9ed387
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88660090"
---
# <a name="known-issues-with-h-series-and-n-series-vms"></a>H 系列和 N 系列 VM 的已知問題

本文提供使用 [H 系列](../../sizes-hpc.md) 和 [N 系列](../../sizes-gpu.md) vm 時最常見的問題和解決方案。

## <a name="dram-on-hb-series"></a>HB 系列上的 DRAM

HB 系列 Vm 目前只能將 228 GB 的 RAM 公開給來賓 Vm。 這是因為 Azure 虛擬程式的已知限制，可防止將頁面指派給 AMD CCX (NUMA 網域的本機 DRAM，) 保留給來賓 VM。

## <a name="accelerated-networking"></a>加速網路

Azure 加速網路目前未啟用，但我們會在預覽期間進行。 當支援這項功能時，我們會通知客戶。

## <a name="qp0-access-restriction"></a>qp0 存取限制

為了防止可能導致安全性弱點的低層級硬體存取，來賓 Vm 無法存取佇列配對0。 這應該只會影響與 ConnectX-5 NIC 的系統管理相關的動作，以及執行一些像是 ibdiagnet，但不是終端使用者應用程式本身的非等量診斷。

## <a name="gss-proxy"></a>GSS Proxy

GSS Proxy 在 CentOS/RHEL 7.5 中有已知的錯誤，可在與 NFS 搭配使用時，將其視為顯著的效能和回應性。 這可透過下列方式降低：

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>快取清除

在 HPC 系統上，在作業完成後清除記憶體，直到下一個使用者被指派相同的節點時，通常會很有用。 在 Linux 中執行應用程式之後，您可能會發現您的可用記憶體會在緩衝區記憶體增加時減少，儘管不會執行任何應用程式。

![命令提示字元的螢幕擷取畫面](./media/known-issues/cache-cleaning-1.png)

使用 `numactl -H` 將會顯示哪些 NUMAnode (s) 記憶體會 (可能的所有) 進行緩衝處理。 在 Linux 中，使用者可以用三種方式清除快取，以將緩衝或快取的記憶體傳回「free」。 您必須是 root 或具有 sudo 許可權。

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![命令提示字元的螢幕擷取畫面](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>核心警告

當您在 Linux 下啟動 HB 系列 VM 時，可能會看到下列核心警告訊息。

```console
[  0.004000] WARNING: CPU: 4 PID: 0 at arch/x86/kernel/smpboot.c:376 topology_sane.isra.3+0x80/0x90
[  0.004000] sched: CPU #4's llc-sibling CPU #0 is not on the same node! [node: 1 != 0]. Ignoring dependency.
[  0.004000] Modules linked in:
[  0.004000] CPU: 4 PID: 0 Comm: swapper/4 Not tainted 3.10.0-957.el7.x86_64 #1
[  0.004000] Hardware name: Microsoft Corporation Virtual Machine/Virtual Machine, BIOS 090007 05/18/2018
[  0.004000] Call Trace:
[  0.004000] [<ffffffffb8361dc1>] dump_stack+0x19/0x1b
[  0.004000] [<ffffffffb7c97648>] __warn+0xd8/0x100
[  0.004000] [<ffffffffb7c976cf>] warn_slowpath_fmt+0x5f/0x80
[  0.004000] [<ffffffffb7c02b34>] ? calibrate_delay+0x3e4/0x8b0
[  0.004000] [<ffffffffb7c574c0>] topology_sane.isra.3+0x80/0x90
[  0.004000] [<ffffffffb7c57782>] set_cpu_sibling_map+0x172/0x5b0
[  0.004000] [<ffffffffb7c57ce1>] start_secondary+0x121/0x270
[  0.004000] [<ffffffffb7c000d5>] start_cpu+0x5/0x14
[  0.004000] ---[ end trace 73fc0e0825d4ca1f ]---
```

您可以忽略此警告。 這是因為 Azure 虛擬程式在一段時間內將會解決的已知限制。


## <a name="infiniband-driver-installation-on-infiniband-enabled-n-series-vm-sizes"></a>在已啟用的已啟用 N 系列 VM 大小上安裝的無駕駛驅動程式

NC24r_v3 和 ND40r_v2 會在 NC24r 且 NC24r_v2 未啟用 SR-IOV 的情況下啟用 SR-IOV。 [以下](../../sizes-hpc.md#rdma-capable-instances)是有關分叉的詳細資料。
使用 OFED 驅動程式時，您可以在啟用 SR-IOV 的 VM 大小上設定自動調整 (IB) ，而非 SR-IOV VM 的大小則需要 ND 驅動程式。 此 IB 支援已在 [CentOS-HPC VMIs](configure.md)上適當地提供。 針對 Ubuntu，請參閱[此處的指示](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351)，以安裝 OFED 和 ND 驅動程式，如檔中所[述。](enable-infiniband.md#vm-images-with-infiniband-drivers)


## <a name="next-steps"></a>後續步驟

- 請檢閱 [HB 系列概觀](hb-series-overview.md)和 [HC 系列概觀](hc-series-overview.md)，了解如何以最佳方式設定工作負載以獲得效能和可擴縮性。
- 請參閱 [Azure 運算技術社群部落格](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)的最新公告和一些 HPC 範例和結果。
- 如需執行中 HPC 工作負載較高階的架構檢視，請參閱 [Azure 上的高效能運算 (HPC)](/azure/architecture/topics/high-performance-computing/)。
