---
title: 創建 Azure 庫伯奈斯服務群集資源
titleSuffix: Azure Cognitive Services
description: 瞭解如何創建 Azure 庫伯奈斯服務 （AKS） 資源。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 4e3102912e88ef904fed3e680f8cdd23242b1f17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74383471"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>創建 Azure 庫伯奈斯服務群集資源

1. 轉到[Azure 庫伯奈斯服務](https://ms.portal.azure.com/#create/microsoft.aks)，然後選擇 **"創建**"。

1. 在 **"基礎知識"** 選項卡上，輸入以下資訊：

    |設定|值|
    |--|--|
    |訂用帳戶|選取適當的訂用帳戶。|
    |資源群組|選擇可用資源組。|
    |庫伯內斯群集名稱|輸入名稱（小寫）。|
    |區域|選擇附近的位置。|
    |庫伯內斯版本|任何值都標記為 **（預設）**。|
    |DNS 名稱首碼|已自動創建，但您可以覆蓋。|
    |節點大小|標準 DS2 v2：<br>`2 vCPUs`, `7 GB`|
    |節點計數|將滑塊保留為預設值。|

1. 在 **"縮放"** 選項卡上，將**虛擬節點**和**VM 縮放集**設置為其預設值。
1. 在 **"身份驗證**"選項卡上，將**服務主體**和**啟用 RBAC**設置為其預設值。
1. 在 **"網路"** 選項卡上，輸入以下選擇：

    |設定|值|
    |--|--|
    |HTTP 應用程式路由|否|
    |網路設定|基本|

1. 在 **"監視"** 選項卡上，請確保**啟用容器監視**設置為 **"是**"，並將**日誌分析工作區**保留為預設值。
1. 在 **"標記"** 選項卡上，立即將名稱/值對留空。
1. 選擇 **"審閱並創建**"。
1. 驗證通過後，選擇 **"創建**"。

> [!NOTE]
> 如果驗證失敗，可能是因為"服務主體"錯誤。 返回 **"身份驗證"** 選項卡，然後返回 **"審閱 + 創建**"，其中驗證應運行，然後通過。
