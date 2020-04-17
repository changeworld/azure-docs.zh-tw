---
title: 使用 Azure HPC 快取進行區域冗餘和故障移轉
description: 使用 Azure HPC 快取為災難復原提供故障轉移功能的技術
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 21074ae6bc4959da031bc7065cd7d0639ec2a14f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537265"
---
# <a name="use-multiple-caches-for-regional-failover-recovery"></a>使用多個快取區域故障移轉

每個 Azure HPC 緩存實例都在特定訂閱中運行,並在一個區域中運行。 這意味著,如果區域完全中斷,您的緩存工作流可能會中斷。

本文介紹了一種通過使用第二個區域進行緩存故障轉移來降低工作中斷風險的策略。

關鍵是使用可從多個區域訪問的後端存儲。 此存儲可以是具有適當 DNS 支援的本地 NAS 系統,也可以是駐留在與緩存不同的區域中的 Azure Blob 儲存。

當工作流在主區域中進行時,數據將保存在區域外部的長期存儲中。 如果緩存區域不可用,則可以在輔助區域中創建重複的 Azure HPC 緩存實例,連接到同一存儲,並從新緩存中恢復工作。

## <a name="planning-for-regional-failover"></a>區域故障移轉的規劃

要設置為可能的故障轉移準備的緩存,請按照以下步驟操作:

1. 確保後端存儲在第二個區域中可訪問。
1. 在計劃創建主緩存實例時,還應準備在第二個區域中複製此設置過程。 包括以下專案:

   1. 虛擬網路和子網結構
   1. 快取容量
   1. 儲存目標詳細資訊、名稱和命名空間路徑
   1. 有關用戶端電腦的詳細資訊(如果它們與快取位於同一區域)
   1. 用於快取客戶端的載入指令

   > [!NOTE]
   > Azure HPC 緩存可以通過[Azure 資源管理器範本](../azure-resource-manager/templates/overview.md)或直接存取其 API 以程式設計方式創建。 有關詳細資訊,請與 Azure HPC 緩存團隊聯繫。

## <a name="failover-example"></a>容錯移轉範例

例如,假設您要在 Azure 的東美國區域中定位 Azure HPC 緩存。 它將存取儲存在本地資料中心的數據。

您可以將美國西部 2 區域中的緩存用作故障轉移備份。

在美國東部創建緩存時,準備第二個緩存以在西部美國2中部署。 您可以使用腳本或範本來自動執行此準備工作。

如果美國東部發生區域範圍故障,請創建您在美國西部 2 區域準備的緩存。

創建快取後,添加指向同一本地資料儲存的儲存目標,並使用與舊快取的儲存目標相同的聚合命名空間路徑。

如果原始用戶端受到影響,請在美國西部 2 區域創建新用戶端以與新緩存一起使用。

所有用戶端都需要裝載新緩存,即使用戶端不受區域中斷的影響。 新緩存具有與舊緩存不同的裝載位址。

## <a name="learn-more"></a>深入了解

Azure 應用程式體系結構指南包含有關如何[從區域範圍的服務中斷中恢復](<https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region>)的詳細資訊。
