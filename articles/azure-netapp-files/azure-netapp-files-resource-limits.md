---
title: Azure NetApp Files 的資源限制 | Microsoft Docs
description: 描述 Azure NetApp 檔資源的限制以及如何請求資源限制增加。
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
ms.date: 04/03/2020
ms.author: b-juche
ms.openlocfilehash: ac660b20d519e49e832e979603f763fa672757a5
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637407"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Azure NetApp Files 的資源限制

了解 Azure NetApp Files 的資源限制有助於管理磁碟區。

## <a name="resource-limits"></a>資源限制

下表描述了 Azure NetApp 檔案的資源限制:

|  資源  |  預設限制  |  可透過支援要求進行調節  |
|----------------|---------------------|--------------------------------------|
|  每個 Azure 區域的淨應用帳戶數   |  10    |  是   |
|  每個 NetApp 帳戶的容量池數   |    25     |   是   |
|  每個容量池的陰影     |    500   |    是     |
|  每個卷的快照數       |    255     |    否        |
|  每個 Azure 虛擬網路委派給 Azure NetApp 檔案(Microsoft.NetApp/卷)的子網數    |   1   |    否    |
|  具有 Azure NetApp 檔案的 VNet(包括立即對等 VNet)中的已使用 IP 數   |    1000   |    否   |
|  單個容量池的最小大小   |  4 TiB     |    否  |
|  單個容量池的最大大小    |  500 TiB   |   否   |
|  單個陰影的最小大小    |    100 GiB    |    否    |
|  單一一一的大小     |    100 TiB    |    否    |
|  單一檔案的最大大小     |    16 TiB    |    否    |    
|  單個目錄的最大大小      |    320 MB    |    否    |    
|  每個卷的最大檔案數 ([最大檔案](#maxfiles))     |    1 億    |    是    |    

有關詳細資訊,請參閱[容量管理常見問題解答](azure-netapp-files-faqs.md#capacity-management-faqs)。

## <a name="maxfiles-limits"></a>最大檔案限制<a name="maxfiles"></a> 

Azure NetApp 檔卷具有稱為*maxfile*的限制。 最大檔案限制是卷可以包含的文件數。 Azure NetApp 檔案卷的最大檔案限制根據卷的大小(配額)進行索引。 卷的最大檔案限制以預配卷大小的每個 TiB 2000 萬個檔的速率增加或減少。 

該服務根據其預配大小動態調整卷的最大檔限制。 例如,最初配置大小為 1 TiB 的卷的最大檔案限制為 2000 萬。 隨後對卷大小的更改將導致根據以下規則自動調整 maxfiles 限制: 

|    體積大小(配額)     |  最大檔案限制的自動調整    |
|----------------------------|-------------------|
|    < 1 TiB                 |    2000萬     |
|    >= 1 TiB,但 < 2 TiB    |    4000萬     |
|    >= 2 TiB,但 < 3 TiB    |    6000 萬     |
|    >= 3 TiB,但 < 4 TiB    |    8000萬     |
|    >= 4 TiB                |    1 億    |

對於任何卷大小,您可以啟動[支援請求](#limit_increase),將最大檔案限制增加到 1 億個。

## <a name="request-limit-increase"></a>要求限制增加<a name="limit_increase"></a> 

您可以創建 Azure 支援請求,以便從上表中增加可調整的限制。 

從 Azure 門戶導航平面: 

1. 按下 **「説明 + 支援**」。
2. 按下 **= 新的支援要求**。
3. 在 [基本概念] 索引標籤中提供下列資訊： 
    1. 問題類型:選擇**服務和訂閱限制(配額)。**
    2. 訂閱:為需要增加配額的資源選擇訂閱。
    3. 配額型態:選擇**儲存:Azure NetApp 檔案限制**。
    4. 按下 **「下一步:解決方案**」。
4. 在「詳細資訊」選項卡上:
    1. 在「描述」框中,為相應的資源類型提供以下資訊:

        |  資源  |    父資源      |    要求的新限制     |    增加配額的原因       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  帳戶 |  *訂閱識別碼*   |  *要求的新最大**帳號***    |  *是什麼方案或用例提示了請求?*  |
        |  集區    |  *訂閱 ID,帳戶 URI*  |  *要求的新最大**池**號*   |  *是什麼方案或用例提示了請求?*  |
        |  磁碟區  |  *訂閱 ID、帳戶 URI、池 URI*   |  *要求的新最大**卷**數*     |  *是什麼方案或用例提示了請求?*  |
        |  最大檔案  |  *訂閱 ID、帳戶 URI、池 URI、卷 URI*   |  *要求的新**最大檔案**編號*     |  *是什麼方案或用例提示了請求?*  |    

    2. 指定適當的支援方法並提供合同資訊。

    3. 按下 **「下一步:查看 + 創建**」以建立請求。 


## <a name="next-steps"></a>後續步驟  

- [了解 Azure NetApp Files 的儲存體階層](azure-netapp-files-understand-storage-hierarchy.md)
- [適用於<c0 />Azure NetApp Files 的成本模型](azure-netapp-files-cost-model.md)
