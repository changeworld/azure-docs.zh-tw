---
title: 提供 Azure 容器映射的設置 |Azure 應用商店
description: 設定 Azure 容器的供應項目設定。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: dsindona
ms.openlocfilehash: e7e56f3562965a0a49082024c905d90ed989ccdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280026"
---
# <a name="container-offer-settings-tab"></a>容器供應項目設定索引標籤

[容器] > [新增供應項目]**** 頁面開啟後，會出現 [供應項目設定] **** 索引標籤。 

![供應項目身分識別](./media/containers-offer-settings.png)

## <a name="offer-identity-settings"></a>供應項目識別設定

在 **"優惠標識**"下，必須為下表中描述的欄位提供資訊。 欄位名稱上附加的星號 (*) 表示此為必填欄位。 

|  **領域**       |     **描述**                                                          |
|  ---------       |     ---------------                                                          |
| **供應項目識別碼\***       | 供應項目的唯一識別碼 (位於發行設定檔中)。 此識別碼會顯示在產品的 URL 與深入解析報告中。 識別碼長度上限為 50 個字元，可使用小寫英數字元和連字號 (-)。 （識別碼不能以破折號結尾。**注：** 此欄位在產品/服務生效後無法更改。 <br> 例如，若 Contoso 發佈了一個識別碼為 **sample-container** 的供應項目，其會收到指派的 Azure Marketplace URL `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview`。 |
| **發行者 ID\***     | 您的組織在 Microsoft Azure Marketplace 中的唯一識別碼。 您的所有供應項目都需要連結到您的發行者識別碼。 儲存供應項目之後，就無法再變更此值。 |
| **名字\***          | 您供應項目的顯示名稱。 此名稱會顯示在 Microsoft Azure Marketplace 和 Cloud Partner 入口網站中。 它最多不能超過 50 個字元。 建議在產品中加入明顯的品牌名稱。 不要包括組織的名稱，除非這是您的產品銷售方式。 如果您要透過其他網站和出版物行銷此供應項目，請確保名稱在所有出版物上都相同。 |
|  |  |

選擇 **"保存"** 以保存產品/服務設置。


## <a name="next-steps"></a>後續步驟

使用 [SKU](./cpp-skus-tab.md) 索引標籤來設定供應項目之 SKU。
