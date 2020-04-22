---
title: 將 Azure NetApp 檔與 Oracle 資料庫結合使用的好處 |微軟文件
description: 描述該技術,並提供 Oracle 直接 NFS (dNFS) 與傳統 NFS 用戶端之間的性能比較。 顯示了將 dNFS 與 Azure NetApp 檔一起使用的優點。
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
ms.date: 04/20/2020
ms.author: b-juche
ms.openlocfilehash: d28f5469174a2659869ebb01ef01653b7190e30e
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81772076"
---
# <a name="benefits-of-using-azure-netapp-files-with-oracle-database"></a>將 Azure NetApp 檔案與 Oracle 資料庫一起使用的好處

Oracle 直接 NFS (dNFS) 使得其性能高於作業系統自己的 NFS 驅動程式。 本文介紹了該技術,並提供了dNFS與傳統NFS用戶端(Kernel NFS)之間的性能比較。 它還顯示了使用 Azure NetApp 文件的優勢和易用性。  

## <a name="how-oracle-direct-nfs-works"></a>甲骨文直接 NFS 的工作原理

Oracle 直接 NFS (dNFS) 繞過作業系統的緩衝區緩存。 數據僅在用戶空間中緩存一次,從而消除了記憶體副本的開銷。  

傳統的 NFS 用戶端使用單個網路流,如下例所示: 

![使用單一網路串流的傳統 NFS 用戶端](../media/azure-netapp-files/solutions-traditional-nfs-client-using-single-network-flow.png)

相比之下,Oracle dNFS 通過跨多個網路流的負載平衡網路流量來提高性能。 此功能使 Oracle 資料庫能夠動態建立大量 650 個不同的網路連接,如下例所示:  

![Oracle 直接 NFS 提高效能](../media/azure-netapp-files/solutions-oracle-direct-nfs-performance-load-balancing.png)

[直接 NFS 的 Oracle 常見問題解答](http://www.orafaq.com/wiki/Direct_NFS)顯示,Oracle dNFS 是一個優化的 NFS 用戶端。 它提供了對位於 NAS 儲存裝置上的 NFS 儲存的快速和可擴展存取(可透過 TCP/IP 存取)。 dNFS 內置於資料庫內核中,就像 ASM 一樣,它主要用於 DAS 或 SAN 儲存。 因此 *,準則是在實現 NAS 儲存時使用 dNFS,並在實現 SAN 儲存時使用 ASM。*

dNFS 是 Oracle 18c 中的預設選項,也是 RAC 的預設選項。

dNFS 從 Oracle 資料庫 11g 開始可用。 下圖將 dNFS 與本機 NFS 進行比較。 使用 dNFS 時,在 Azure 虛擬機器上運行的 Oracle 資料庫可以驅動比本機 NFS 用戶端更多的 I/O。

![Oracle 和 Azure NetApp 檔案 dNFS 與本機 NFS 的比較](../media/azure-netapp-files/solutions-oracle-azure-netapp-files-comparing-dnfs-native-nfs.png)

您可以透過執行兩個指令並重新啟動資料庫來啟用或禁用 dNFS。

要開啟:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_on`

要關閉:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_off`

## <a name="azure-netapp-files-combined-with-oracle-direct-nfs"></a>Azure NetApp 檔案與 Oracle 直接 NFS 結合使用

您可以使用 Azure NetApp 檔案服務提高 Oracle dNFS 的性能。 該服務使您能夠完全控制應用程式性能。 它可以滿足極其苛刻的應用。 Oracle dNFS 與 Azure NetApp 檔案的組合為您的工作負載提供了極大的優勢。

## <a name="next-steps"></a>後續步驟

- [使用 Azure NetApp Files 的方案架構](azure-netapp-files-solution-architectures.md)
- [Azure 上的 Oracle 應用程式和解決方案概述](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-overview)