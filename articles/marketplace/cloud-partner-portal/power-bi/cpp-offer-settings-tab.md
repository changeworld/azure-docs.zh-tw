---
title: 電源 BI 應用產品/服務提供設置 |Azure 應用商店
description: 為 Microsoft AppSource 市場配置 Power BI 應用產品/服務的產品/服務設置。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: dsindona
ms.openlocfilehash: f3a8d740d391edc09a290d3dba4307af7eec00b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286348"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Power BI 應用程式供應項目設定索引標籤

當您為服務應用打開 **"新產品/服務**"頁面時，首先可以看到"**產品/服務設置"** 選項卡。您可以在此選項卡上提供主要識別碼和產品/服務的名稱。星號 （*） 表示必要欄位。

![供應項目設定索引標籤](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>供應項目設定欄位 

在"**優惠設置"** 選項卡上，您需要在以下必要欄位中輸入資訊。 必要欄位由星號 (*) 標示。

|  欄位        |  描述                                                               |
|---------------|----------------------------------------------------------------------------|
| **供應項目識別碼\***  | 供應項目的唯一識別碼 (位於發行設定檔中)。 此識別碼會顯示在產品 URL、Azure Resource Manager 範本和計費報告中。 長度上限是 50 個字元。 它只能包含小寫字母數位字元和破折號 （-）。 它不能以破折號結束。 此識別碼在產品/服務生效後無法更改。 如果 Contoso 發佈報價 ID，`sample-SvcApp`則為產品/服務分配了`https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`AppSource URL。      |
| **出版商\*** | 您的組織在 [AppSource](https://appsource.microsoft.com) 中的唯一識別碼。 您的所有供應項目都需要連結到您的發行者識別碼。 儲存供應項目後，就無法再變更此值。                         |
| **名字\***      | 優惠的顯示名稱。 此名稱將顯示在 AppSource 和雲合作夥伴門戶上。 長度上限是 50 個字元。 使用產品可識別的品牌名稱。 不要在此處包含組織的名稱，除非應用使用該名稱進行銷售。 如果您在其他網站和出版物上提供此優惠，則在所有出版物中使用同一名稱。    <br/>如果在 Power BI 應用的預覽期內發佈產品/服務，請在`(Preview)`應用程式名稱的末尾添加字串，如下所示： `Sample Scv App (Preview)`。 |
|     |     |


## <a name="next-steps"></a>後續步驟

在下一個選項卡上，您將為產品/服務指定[技術資訊](./cpp-technical-info-tab.md)。
