---
title: Azure IoT Edge 模組的供應專案設定 |Azure Marketplace
description: 調整 IoT Edge 模組的供應項目設定。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d6d2306dfd44bad6e1f903c3cef3eeb4993f4cd9
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82148029"
---
# <a name="iot-edge-module-offer-settings-tab"></a>IoT Edge 模組供應項目索引標籤

>[!Important]
>從2020年4月13日開始，我們會開始將您的 IoT Edge 課程模組的管理工作移至合作夥伴中心。 在遷移之後，您將在合作夥伴中心建立和管理您的供應專案。 請依照[建立 IoT Edge 模組供應](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation)專案中的指示來管理您遷移的供應專案。

[IoT Edge 模組] > [新增供應項目]**** 頁面開啟後，會出現 [供應項目設定] **** 索引標籤。

![IoT Edge 模組的新增供應項目頁面](./media/iot-edge-module-offer-settings-tab.png)


## <a name="offer-identity-settings"></a>供應項目識別設定

在 [**供應專案識別**] 底下，您必須提供下表所述欄位的資訊。 欄位名稱上附加的星號 (*) 表示此為必填欄位。 

|  **欄位**       |     **描述**                                                          |
|  ---------       |     ---------------                                                          |
| **供應項目識別碼\***       | 供應項目的唯一識別碼 (位於發行設定檔中)。 此識別碼會顯示在產品的 URL 與深入解析報告中。 識別碼長度上限為 50 個字元，可使用小寫英數字元和連字號 (-)。 （識別碼的結尾不能是破折號）。**注意：** 供應專案上線之後，即無法變更此欄位。 <br> 例如，若 Contoso 發佈了一個識別碼為 **sample-iot-edge-module** 的供應項目，其會收到指派的 Azure Marketplace URL`https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-iot-edge-module?tab=Overview`。 |
| **發行者\***     | 您的組織在 Microsoft Azure Marketplace 中的唯一識別碼。 您的所有供應項目都需要連結到您的發行者識別碼。 儲存供應項目之後，就無法再變更此值。 |
| **Name\***          | 您供應項目的顯示名稱。 此名稱會顯示在 Microsoft Azure Marketplace 和 Cloud Partner 入口網站中。 它最多不能超過 50 個字元。 建議在產品中加入明顯的品牌名稱。 請勿包含您的組織名稱，除非這是您的產品行銷方式。 如果您要透過其他網站和出版物行銷此供應項目，請確保名稱在所有出版物上都相同。 |
|  |  |


選取 [**儲存**] 以儲存供應專案設定。


## <a name="next-steps"></a>後續步驟

使用 [SKU](./cpp-skus-tab.md) 索引標籤來設定供應項目之 SKU。
