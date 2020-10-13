---
title: 使用 Azure NetApp Files 搭配 Oracle Database 的優點 |Microsoft Docs
description: 描述技術並提供 Oracle Direct NFS (dNFS) 與傳統 NFS 用戶端之間的效能比較。 顯示搭配使用 dNFS 與 Azure NetApp Files 的優點。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: b-juche
ms.openlocfilehash: 0f431015caf5bb3364fe5628a599f760c6318c47
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91932493"
---
# <a name="benefits-of-using-azure-netapp-files-with-oracle-database"></a>使用 Azure NetApp Files 搭配 Oracle Database 的優點

Oracle Direct NFS (dNFS) 可讓您比作業系統本身的 NFS 驅動程式更能提高效能。 本文說明這項技術，並提供 dNFS 與傳統 NFS 用戶端 (Kernel NFS) 之間的效能比較。 它也會顯示使用 dNFS 搭配 Azure NetApp Files 的優點和便利性。  

## <a name="how-oracle-direct-nfs-works"></a>Oracle Direct NFS 的運作方式

下列摘要說明 Oracle Direct NFS 如何以高層級運作：

* Oracle Direct NFS 會略過作業系統緩衝區快取。 資料只會在使用者空間中快取一次，消除記憶體複本的額外負荷。  

* 傳統 NFS 用戶端會使用單一網路流程，如下所示：    

    ![使用單一網路流程的傳統 NFS 用戶端](../media/azure-netapp-files/solutions-traditional-nfs-client-using-single-network-flow.png)

    Oracle Direct NFS 可透過將網路流量負載平衡到多個網路流程，進一步改善效能。 如下所示，650不同的網路連線是由 Oracle Database 動態建立：  

    ![Oracle Direct NFS 改善效能](../media/azure-netapp-files/solutions-oracle-direct-nfs-performance-load-balancing.png)

[DIRECT NFS 的 ORACLE 常見問題](http://www.orafaq.com/wiki/Direct_NFS)顯示 oracle dNFS 是優化的 nfs 用戶端。 它可快速且可擴充地存取位於 NAS 存放裝置上的 NFS 儲存體， (可透過 TCP/IP) 存取。 dNFS 是內建在資料庫核心中，就像 ASM 一樣，其主要用於 DAS 或 SAN 儲存體。 因此，在執行 NAS 儲存體時， *指導方針是使用 dNFS，並在執行 SAN 儲存體時使用 ASM。*

dNFS 是 Oracle 18c 中的預設選項。

從 Oracle Database 11g 開始提供 dNFS。 下圖比較 dNFS 與原生 NFS。 當您使用 dNFS 時，在 Azure 虛擬機器上執行的 Oracle 資料庫可驅動比原生 NFS 用戶端更多的 i/o。

![Oracle 和 Azure NetApp Files 與 dNFS 與 native NFS 的比較](../media/azure-netapp-files/solutions-oracle-azure-netapp-files-comparing-dnfs-native-nfs.png)

您可以執行兩個命令並重新啟動資料庫，以啟用或停用 dNFS。

若要啟用：  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_on`

若要停用：  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_off`

## <a name="azure-netapp-files-combined-with-oracle-direct-nfs"></a>與 Oracle Direct NFS 結合的 Azure NetApp Files

您可以使用 Azure NetApp Files 服務增強 Oracle dNFS 的效能。 服務可讓您完全掌控您的應用程式效能。 它可以滿足極嚴苛的應用程式。 Oracle dNFS 與 Azure NetApp Files 的組合可為您的工作負載提供絕佳的優勢。

## <a name="next-steps"></a>後續步驟

- [使用 Azure NetApp Files 的方案架構](azure-netapp-files-solution-architectures.md)
- [Azure 上的 Oracle 應用程式和解決方案概觀](../virtual-machines/workloads/oracle/oracle-overview.md)