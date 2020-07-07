---
title: HB 系列和 HC 系列 Vm 的已知問題-Azure 虛擬機器 |Microsoft Docs
description: 瞭解 Azure 中 HB 系列 VM 大小的已知問題。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: 8d4b57fb2fee3849e102868c86fe3cab465fc70d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "67707786"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>HB-系列和 HC 系列 VM 的已知問題

本文提供使用 HB 系列和 HC 系列 Vm 時最常見的問題和解決方案。

## <a name="dram-on-hb-series"></a>HB 系列上的 DRAM

HB 系列 Vm 目前只能對來賓 Vm 公開 228 GB 的 RAM。 這是因為 Azure 虛擬機器的已知限制，導致無法將頁面指派給為來賓 VM 保留的 AMD CCX （NUMA 網域）的本機 DRAM。

## <a name="accelerated-networking"></a>加速網路

目前未啟用 Azure 加速網路，但我們會在預覽期間進行。 當支援這項功能時，我們會通知客戶。

## <a name="qp0-access-restriction"></a>qp0 存取限制

為防止可能導致安全性弱點的低層級硬體存取，來賓 Vm 無法存取佇列配對0。 這只會影響通常與 ConnectX-5 NIC 的管理相關的動作，並執行一些不受 ibdiagnet 的診斷（例如，而不是終端使用者應用程式本身）。

## <a name="ud-transport"></a>UD 傳輸

在啟動時，HB 和 HC 系列不支援動態連線的傳輸（DCT）。 DCT 的支援將會在一段時間後執行。 支援可靠連接（RC）和不可靠的資料包（UD）傳輸。

## <a name="gss-proxy"></a>GSS Proxy

GSS Proxy 在 CentOS/RHEL 7.5 中有已知的錯誤，可在搭配 NFS 使用時，將其資訊清單為顯著的效能和回應性的影響。 這可以透過下列方式減輕：

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>快取清除

在 HPC 系統上，在下一次使用者指派相同節點之前，在作業完成後清除記憶體通常會很有用。 在 Linux 中執行應用程式之後，您可能會發現您的可用記憶體會在緩衝區記憶體增加時減少，但不會執行任何應用程式。

![命令提示字元的螢幕擷取畫面](./media/known-issues/cache-cleaning-1.png)

使用 `numactl -H` 將會顯示記憶體緩衝處理的 NUMAnode （可能全部）。 在 Linux 中，使用者可以用三種方式清除快取，以將緩衝或快取的記憶體傳回「免費」。 您必須是 root 或具有 sudo 許可權。

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![命令提示字元的螢幕擷取畫面](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>核心警告

在 Linux 底下啟動 HB 系列 VM 時，您可能會看到下列內核警告訊息。

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

您可以忽略此警告。 這是因為 Azure 虛擬機器的已知限制，將會在一段時間後解決。

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure 中的[高效](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)能運算。
