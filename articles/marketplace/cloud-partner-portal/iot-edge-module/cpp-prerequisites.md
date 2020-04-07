---
title: Azure IoT 邊緣模組先決條件 |Azure 應用商店
description: 發佈 IoT Edge 模組的先決條件。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 93e0418004ea65f93ad436eec1ce523424a194ba
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80744978"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>IoT Edge 模組發佈先決條件

>[!Important]
>從 2020 年 3 月 30 日開始,我們將開始將 IoT Edge 模組產品的管理轉移到合作夥伴中心。 遷移後,您將在合作夥伴中心創建和管理您的優惠。 按照[創建IoT Edge模組產品/服務](https://aka.ms/AzureCreateIoT)中的說明進行操作,以管理遷移的優惠。

本文將說明發佈 IoT Edge 模組供應項目所需先決條件。  如果尚未這樣做,請檢視[IoT Edge 模組發表指南](../..//iot-edge-module.md)。


## <a name="publishing-prerequisites"></a>發佈先決條件

若要將 IoT Edge 模組發佈至 Microsoft Azure Marketplace，您必須滿足下列先決條件：

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- 可存取 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。 如需詳細資訊，請參閱 [Azure Marketplace 和 AppSource 發行指南](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)。
- 同意 [Azure Marketplace 條款](https://azure.microsoft.com/support/legal/marketplace-terms/)
- 將您的 IoT Edge 模組技術資產裝載於 Azure Container Registry 中。  如需詳細資訊，請參閱[準備 IoT Edge 模組的技術資產](./cpp-create-technical-assets.md)
- 準備好 IoT Edge 模組的中繼資料以供使用。 例如,準備以下資產:
    - 標題
    - 描述 (HTML 格式)
    - 標誌影像 (PNG 格式且影像大小固定，包括 40x40px、90x90px、115x115px、255x115px)
    - 使用規定及隱私權原則
    - 預設的模組設定包括：路由、對應項所需屬性、createOptions 以及環境變數。
    - 模組文件
    - 支援連絡人


## <a name="next-steps"></a>後續步驟

一旦[您準備好 IoT Edge 模組技術資產](./cpp-create-technical-assets.md),您將準備好[創建 IoT Edge 模組產品](./cpp-create-offer.md)。 
