---
title: Azure 進階儲存體：專為 Windows VM 的效能而設計 | Microsoft Docs
description: 使用 Azure premium SSD 受控磁片設計高效能應用程式。 「進階儲存體」可針對在「Azure 虛擬機器」上執行且需要大量 I/O 的工作負載，提供高效能、低延遲的磁碟支援。
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 12fb94bb4f98bde5c70343f18762cefe1ab120f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75371323"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Azure 進階儲存體：專為高效能而設計
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> 有時候，看似磁碟效能問題的情況，其實是網路瓶頸。 在這些情況下，您應該將您的[網路效能](../../virtual-network/virtual-network-optimize-network-bandwidth.md)最佳化。
>
> 如果您想要對磁片進行基準測試，請參閱關於[磁片的](disks-benchmarks.md)效能評定文章。
>
> 如果您的 VM 支援加速網路，您應確實加以啟用。 如果未啟用，您可以對 [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) 和 [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms) 上已部署的 VM 加以啟用。

開始之前，如果您不熟悉進階儲存體，請先閱讀針對[IaaS Vm 選取 Azure 磁片類型](disks-types.md)和[Premium 分頁 blob 儲存體帳戶的擴充性目標](../../storage/blobs/scalability-targets-premium-page-blobs.md)。

[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

如果您想要對磁片進行基準測試，請參閱關於[磁片的](disks-benchmarks.md)效能評定文章。

深入瞭解可用的磁片類型：[選取磁片類型](disks-types.md)  

若為 SQL Server 使用者，請參閱「SQL Server 的效能最佳作法」文章：

* [Azure 虛擬機器中 SQL Server 的效能最佳作法](sql/virtual-machines-windows-sql-performance.md)
* [Azure 進階儲存體為 Azure VM 中的 SQL Server 提供最高效能](https://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)