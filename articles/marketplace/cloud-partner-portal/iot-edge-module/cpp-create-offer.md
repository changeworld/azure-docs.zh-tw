---
title: 建立 Azure IoT Edge 模組供應專案 |Azure Marketplace
description: 如何在 Marketplace 中發佈新的 IoT Edge 模組。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d8f4300f45fc626c104055f987d123301ba8f39d
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82148073"
---
# <a name="create-a-new-iot-edge-module-offer-with-the-cloud-partner-portal"></a>使用 Cloud Partner 入口網站建立新 IoT Edge 模組供應項目

>[!Important]
>從2020年4月13日開始，我們會開始將您的 IoT Edge 課程模組的管理工作移至合作夥伴中心。 在遷移之後，您將在合作夥伴中心建立和管理您的供應專案。 請依照[建立 IoT Edge 模組供應](https://docs.microsoft.com//azure/marketplace/partner-center-portal/azure-iot-edge-module-creation)專案中的指示來管理您遷移的供應專案。

本文說明如何建立及發佈 Microsoft Azure Marketplace 的 IoT Edge 模組供應項目。 每個供應項目在 Microsoft Azure Marketplace 中皆為獨立實體，會與一或多個 SKU 相關聯。  IoT Edge 模組供應項目是由下列資產群組和支援服務所組成：

|  **資產群組**   |  **描述**  |
|  ---------------   |  ---------------  |
|    SKU            |  供應項目的最小佈署單位。 單一供應項目 (產品類別) 可有多個相關聯的 SKU。 您可以使用 SKU 來區分不同支援功能和計費模式。 |
|  Marketplace       | 包含行銷、法律及潛在客戶管理的資產和規格。  <ul><li> 行銷資產包括供應項目名稱、說明和標誌</li> <li> 法律資產包括隱私權原則、使用條款及其他法律文件</li>  <li> 潛在客戶管理原則可指定於 Microsoft Azure Marketplace 使用者入口網站管理潛在客戶的方式。</li> </ul> |
| 支援            | 包含支援聯絡人與原則的相關資訊 |


## <a name="new-offer-form"></a>新增供應項目表單 

登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)後，選取左側的選單列上的 [+ 新增供應項目]****。 在 [新增供應項目] 選單中，選取 [IoT Edge 模組]**** 以顯示 [新增供應項目]**** 表單，並開始定義新 IoT Edge 模組供應項目資產。 

![選擇新 IoT Edge 模組供應項目使用者介面](./media/new-iot-edge-module-offer.png)

## <a name="next-steps"></a>後續步驟

IoT Edge 模組供應項目型別的**新增供應項目**頁面上，會提供一組索引標籤和表單欄位，方便您建立新供應項目。 下列文章將分別說明如何使用索引標籤，以便用於定義資產群組及新 IoT Edge 模組供應項目的支援服務。

- [供應項目設定索引標籤](./cpp-offer-settings-tab.md)
- [SKU 索引標籤](./cpp-skus-tab.md)
- [Marketplace 索引標籤](./cpp-marketplace-tab.md)
- [支援索引標籤](./cpp-support-tab.md)
