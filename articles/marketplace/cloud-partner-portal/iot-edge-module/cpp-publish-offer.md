---
title: 發佈 Azure IoT 邊緣模組產品 /服務 |Azure 應用商店
description: 如何發佈 IoT Edge 模組供應項目。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 2220352f817b488b3992b88920428063edde006e
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745007"
---
# <a name="publish-iot-edge-module-offer"></a>發佈 IoT Edge 模組供應項目

>[!Important]
>從 2020 年 3 月 30 日開始,我們將開始將 IoT Edge 模組產品的管理轉移到合作夥伴中心。 遷移後,您將在合作夥伴中心創建和管理您的優惠。 按照[創建IoT Edge模組產品/服務](https://aka.ms/AzureCreateIoT)中的說明進行操作,以管理遷移的優惠。

 在 [新增供應項目]**** 頁面提供資訊並建立新供應項目後，即可發佈供應項目。 選取 [發佈]**** 即可啟動發佈程序。

下圖將展示供應項目「上線」發佈程序中主要的步驟。

![IoT Edge 模組供應項目發佈步驟](./media/iot-edge-module-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>發佈步驟的詳細說明

下列表格將說明各個發佈步驟，以及完成該步驟估計所需時間 (最大值)。
<!-- P2: we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **發行步驟**           | **時間**    | **說明**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| 檢查必要條件         | 15 分鐘   | 檢查供應項目資訊和供應項目設定。                        |
| 認證                  | 2 週 | Azure 認證團隊會分析供應項目。 此步驟會掃描病毒、惡意程式碼，並檢查安全合規性和安全性問題。 它也會確認此 IoT Edge 模組供應項目是否符合所有的資格條件 (請參閱[先決條件](./cpp-prerequisites.md)及[準備您的技術資產](./cpp-create-technical-assets.md))。 若發現問題，將提供意見反應。 |
| 包裝 | 1 小時  | 產品/服務的技術資產打包供客戶使用,並配置和設置引線系統。 |
|  發行者簽核             |  -        | 供應項目上線前發行者最終檢閱與確認。 您可以在所選訂用帳戶部署供應項目 (供應項目資訊步驟)，確認是否符合您所有要求。  選取 [Go Live]**** 讓供應項目進行下一個步驟。 |
| 包裝                 | 1 小時 | 最終完成的供應項目會在市集生產系統和磁碟區域產生複本。 | 
| 即時                           | 4 天 |供應項目推出、複寫到必要磁碟區域，並開放給大眾使用。 |

完成發佈程序並推出供應項目最多需要 10 個工作天。 完成發佈程序之後，您的 IoT Edge 模組供應項目將會列在[Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)。

## <a name="next-steps"></a>後續步驟

- [更新 Microsoft Azure Marketplace 中之現有 IoT Edge 模組供應項目](./cpp-update-existing-offer.md)
