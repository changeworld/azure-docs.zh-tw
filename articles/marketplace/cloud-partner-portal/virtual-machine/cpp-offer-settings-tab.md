---
title: Azure 應用商店雲合作夥伴門戶中的虛擬機器提供設置選項卡
description: 說明用來建立 Microsoft Azure Marketplace 虛擬機器供應項目的供應項目設定索引標籤。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: e5353797aaa7d40e1b6a95685b79cb3fe99f5ecd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277645"
---
# <a name="virtual-machine-offer-settings-tab"></a>虛擬機器供應項目設定索引標籤

虛擬機器的**新增供應項目**頁面會在名為**供應項目設定**的第一個索引標籤開啟。  

![虛擬機器的新供應項目頁面](./media/publishvm_004.png)


## <a name="offer-settings-fields"></a>供應項目設定欄位

在"**產品/服務設置"** 選項卡中，必須提供以下欄位。  欄位名稱上附加星號 (*) 表示為必填欄位。 

|  **領域**       |     **描述**                                                          |
|  ---------       |     ---------------                                                          |
| **供應項目識別碼\***   | 供應項目的唯一識別碼 (位於發行設定檔中)。 此識別碼會顯示在產品 URL、Azure Resource Manager 範本和計費報告中。 識別碼長度上限為 50 個字元，只能包含小寫英數字元和連字號 (-)，但不能以連字號結尾。 供應項目上線之後即無法變更此欄位。 <br> 例如，若 Contoso 發佈了一個識別碼為 **sample-vm** 的供應項目，其會收到指派的 Azure Marketplace URL`https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview`。 |
| **出版商\***  | 您的組織在 Microsoft Azure Marketplace 中的唯一識別碼。 您的所有供應項目都需要連結到您的發行者識別碼。 供應項目儲存後，不可再修改此欄位內容。 |
| **名字\***       | 您的供應項目顯示名稱。 此名稱會顯示在 Microsoft Azure Marketplace 和 Cloud Partner 入口網站中。 它最多不能超過 50 個字元。 建議在產品中包含明顯的品牌名稱。 不要在此處包含組織的名稱，除非這是銷售方式。 如果您要透過其他網站和出版物行銷此供應項目，請確保名稱在所有出版物上都相同。 |
|   |   |
 
在提供所有欄位後按一下"**保存**"。 


## <a name="next-steps"></a>後續步驟

在下一個索引標籤中，您將新增 [SKU](./cpp-skus-tab.md) 至供應項目中。
