---
title: Azure Marketplace IoT Edge 模組供應項目
description: 了解如何在 Azure Marketplace 中發佈 IoT Edge 模組供應項目。
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 49f86a79eb5358d27c15d93004db396436c3e680
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83657966"
---
# <a name="iot-edge-modules"></a>IoT Edge 模組

[Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) 平台是 Microsoft Azure 所支持的平台。  此平台可讓使用者部署要直接在 IoT 裝置上執行的雲端工作負載。  IoT Edge 模組可以執行離線工作負載，並在本機進行資料分析。 此供應項目類型有助於節省頻寬、保護本機和敏感性資料，並可提供低延遲回應時間。  您現在可以選擇利用這些預先建置的工作負載。 到目前為止，只有來自 Microsoft 的少數第一方解決方案可供使用。  您必須投入時間和資源來建置自己的自訂 IoT 解決方案。

有了 [Azure Marketplace 中的 IoT Edge 模組](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1)，我們現在能提供一個可讓發行者向 IoT 對象展示及銷售其解決方案的單一目的地。 IoT 開發人員最終將能找到及購買可加快其解決方案開發速度的功能。  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Azure Marketplace 中 IoT Edge 模組的主要優勢

| **針對發行者**    | **針對客戶 (IoT 開發人員)**  |
| :------------------- | :-------------------|
| 觸達上百萬個想要建置及部署 IoT Edge 解決方案的開發人員。  | 信心十足地使用安全且經測試的元件來撰寫 IoT Edge 解決方案。 |
| 只需發佈一次，即可在支援容器的任何 IoT Edge 硬體上執行。 | 透過找出及部署符合特定需求的第一方和第三方 IoT Edge 模組，縮短上市時間。 |
| 運用彈性計費選項創造營收 <ul> <li> 免費和自備授權 (BYOL)。 </li> </ul> | 依據您選擇的計費模型進行購買。 <ul> <li> 免費和自備授權 (BYOL)。 </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>什麼是 IoT Edge 模組？

Azure IoT Edge 可讓您以模組形式，在邊緣上部署及管理商務邏輯。 Azure IoT Edge 模組是 IoT Edge 所管理的最小計算單位，並可包含 Microsoft 服務 (例如「Azure 串流分析」)、第三方服務，或您自己的解決方案特定程式碼。 若要深入了解 IoT Edge 模組，請參閱[了解 Azure IoT Edge 模組](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules)。

**容器供應項目類型與 IoT Edge 模組供應項目類型之間有何差異？**

IoT Edge 模組供應項目類型是一種在 IoT Edge 裝置上執行的特定類型容器。 它隨附用以在 IoT Edge 內容中執行的預設組態設定，並可選擇使用要與 IoT Edge 執行階段整合的 IoT Edge 模組 SDK。

## <a name="publishing-your-iot-edge-module"></a>發佈您的 IoT Edge 模組

**選取正確的店面**

IoT Edge 模組只會發佈至 Azure Marketplace，AppSource 則不適用。  如需有關各店面之差異和目標對象的詳細資訊，請參閱[決定您解決方案的發佈選項](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type)。
 
**帳單選項**

Marketplace 目前針對 IoT Edge 模組支援 [免費] 和 [自備授權 (BYOL)] 計費選項。
 
**發佈選項**

在所有情況下，IoT Edge 模組都應該選取 [交易] 發佈選項。  如需有關發佈選項的更多詳細資料，請參閱[選擇發佈選項](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type)。  

## <a name="eligibility-criteria"></a>適用性準則

所有 Microsoft Azure Marketplace 合約和原則的條款都適用於 IoT Edge 模組供應項目。  此外，還有適用於 IoT Edge 模組的先決條件和技術需求。  

**必要條件**

若要將 IoT Edge 模組發佈至 Azure Marketplace，您必須滿足下列先決條件：

- 存取合作夥伴中心。 如需詳細資訊，請參閱 [Azure Marketplace 和 AppSource 發行指南](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)。
- 將您的 IoT Edge 模組裝載在 Azure Container Registry 中。 
- 備妥您的 IoT Edge 模組中繼資料，例如 (非完整清單)： 
    - 標題
    - 描述 (HTML 格式)
    - 標誌影像 (PNG 格式且影像大小固定，包括 40x40px、90x90px、115x115px、255x115px)
    - 使用規定及隱私權原則
    - 預設的模組設定 (路由、對應項所需屬性、createOptions、環境變數)
    - 文件
    - 支援連絡人

**技術需求**

[準備 IoT Edge 模組技術資產](./partner-center-portal/create-iot-edge-module-asset.md)詳細說明了 IoT Edge 模組的主要技術需求，以便讓該模組通過認證並發佈於 Azure Marketplace 中。

## <a name="documentation-and-resources"></a>文件和資源

[建立 IoT Edge 模組供應項目](./partner-center-portal/azure-iot-edge-module-creation.md) - 在合作夥伴中心發佈新 IoT Edge 模組供應項目的步驟。

## <a name="next-steps"></a>後續步驟

如果您還沒有這麼做，請

- [了解](https://azuremarketplace.microsoft.com/sell)市集。

若要在合作夥伴中心註冊，並開始建立新供應項目或使用現有供應項目，

- 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)，以建立或完成您的供應項目。
- 如需如何發佈 IoT Edge 模組供應項目的詳細資訊，請參閱[建立 IoT Edge 模組供應項目](./partner-center-portal/azure-iot-edge-module-creation.md)。
