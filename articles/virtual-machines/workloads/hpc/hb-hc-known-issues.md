---
title: HB 系列和 HC 系列 VM 的已知問題 - Azure 虛擬機器 |微軟文檔
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707786"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>HB-系列和 HC 系列 VM 的已知問題

本文提供了使用 HB 系列和 HC 系列 VM 時最常見的問題和解決方案。

## <a name="dram-on-hb-series"></a>HB 系列的 DRAM

HB 系列 VM 此時只能向來賓 VM 公開 228 GB 的 RAM。 這是因為 Azure 虛擬機器管理程式的已知限制，以防止將頁面分配給為來賓 VM 保留的 AMD CCX （NUMA 域） 的本地 DRAM。

## <a name="accelerated-networking"></a>加速網路

此時未啟用 Azure 加速網路，但會隨著預覽期的進行而啟用。 當此功能受支援時，我們將通知客戶。

## <a name="qp0-access-restriction"></a>qp0 訪問限制

為了防止可能導致安全性漏洞的低級硬體訪問，來賓 VM 無法訪問佇列對 0。 這應僅影響通常與 ConnectX-5 NIC 的管理關聯的操作，並運行某些 InfiniBand 診斷（如 ibdiagnet）的操作，但不會影響最終使用者應用程式本身。

## <a name="ud-transport"></a>UD 運輸

在啟動時，HB 和 HC 系列不支援動態連接傳輸 （DCT）。 隨著時間的推移，將對 DCT 的支援實施。 支援可靠連接 （RC） 和不可靠的資料圖 （UD） 傳輸。

## <a name="gss-proxy"></a>GSS 代理

GSS 代理在 CentOS/RHEL 7.5 中存在已知 Bug，當與 NFS 一起使用時，該 Bug 可以表現為顯著的性能和回應性損失。 這可以通過：

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>緩存清理

在 HPC 系統上，在分配下一個使用者相同的節點之前，在作業完成後清理記憶體通常很有用。 在 Linux 中運行應用程式後，您可能會發現可用記憶體會減少，而緩衝區記憶體會增加，儘管沒有運行任何應用程式。

![命令提示符的螢幕截圖](./media/known-issues/cache-cleaning-1.png)

使用`numactl -H`將顯示使用（可能全部）緩衝記憶體的 NUMAnode。s。 在 Linux 中，使用者可以通過三種方式清理緩存，將緩衝或緩存的記憶體返回到"空閒"。 您需要是 root 或具有 sudo 許可權。

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![命令提示符的螢幕截圖](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>內核警告

在 Linux 下啟動 HB 系列 VM 時，您可能會看到以下內核警告訊息。

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

您可以忽略此警告。 這是由於 Azure 虛擬機器管理程式的已知限制，該限制將隨著時間的推移得到解決。

## <a name="next-steps"></a>後續步驟

瞭解有關 Azure[中高性能計算](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)的更多詳細資訊。
