---
title: 提供 Azure IoT 邊緣模組的設定 |Azure 應用商店
description: 調整 IoT Edge 模組的供應項目設定。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: e7b7d04cd3d1ed9792f8ad3c7bc302e4a2c2180f
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745035"
---
# <a name="iot-edge-module-offer-settings-tab"></a>IoT Edge 模組供應項目索引標籤

>[!Important]
>從 2020 年 3 月 30 日開始,我們將開始將 IoT Edge 模組產品的管理轉移到合作夥伴中心。 遷移後,您將在合作夥伴中心創建和管理您的優惠。 按照[創建IoT Edge模組產品/服務](https://aka.ms/AzureCreateIoT)中的說明進行操作,以管理遷移的優惠。

[IoT Edge 模組] > [新增供應項目]**** 頁面開啟後，會出現 [供應項目設定] **** 索引標籤。 

![IoT Edge 模組的新增供應項目頁面](./media/iot-edge-module-offer-settings-tab.png)


## <a name="offer-identity-settings"></a>供應項目識別設定

在 **「優惠標識**」下,必須為下表中描述的欄位提供資訊。 欄位名稱上附加的星號 (*) 表示此為必填欄位。 

|  **欄位**       |     **說明**                                                          |
|  ---------       |     ---------------                                                          |
| **供應項目識別碼\***       | 供應項目的唯一識別碼 (位於發行設定檔中)。 此識別碼會顯示在產品的 URL 與深入解析報告中。 識別碼長度上限為 50 個字元，可使用小寫英數字元和連字號 (-)。 (標識符不能以破折號結尾。**註:** 此欄位在產品/服務生效後無法更改。 <br> 例如，若 Contoso 發佈了一個識別碼為 **sample-iot-edge-module** 的供應項目，其會收到指派的 Azure Marketplace URL`https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-iot-edge-module?tab=Overview`。 |
| **出版商\***     | 您的組織在 Microsoft Azure Marketplace 中的唯一識別碼。 您的所有供應項目都需要連結到您的發行者識別碼。 儲存供應項目之後，就無法再變更此值。 |
| **名稱\***          | 您供應項目的顯示名稱。 此名稱會顯示在 Microsoft Azure Marketplace 和 Cloud Partner 入口網站中。 它最多不能超過 50 個字元。 建議在產品中加入明顯的品牌名稱。 不要包括組織的名稱,除非這是您的產品銷售方式。 如果您要透過其他網站和出版物行銷此供應項目，請確保名稱在所有出版物上都相同。 |
|  |  |


選擇 **「保存」** 以保存產品/服務設定。


## <a name="next-steps"></a>後續步驟

使用 [SKU](./cpp-skus-tab.md) 索引標籤來設定供應項目之 SKU。
