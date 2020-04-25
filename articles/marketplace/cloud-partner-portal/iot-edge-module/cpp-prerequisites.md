---
title: Azure IoT Edge 模組必要條件 |Azure Marketplace
description: 發佈 IoT Edge 模組的先決條件。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d1bfc29a0cb031421b4017454bc8a2cdd8be09f9
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82142375"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>IoT Edge 模組發佈先決條件

>[!Important]
>從2020年4月13日開始，我們會開始將您的 IoT Edge 課程模組的管理工作移至合作夥伴中心。 在遷移之後，您將在合作夥伴中心建立和管理您的供應專案。 請依照[建立 IoT Edge 模組供應](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation)專案中的指示來管理您遷移的供應專案。

本文將說明發佈 IoT Edge 模組供應項目所需先決條件。  如果您尚未這麼做，請參閱[IoT Edge 模組發佈指南](../..//iot-edge-module.md)。


## <a name="publishing-prerequisites"></a>發佈先決條件

若要將 IoT Edge 模組發佈至 Microsoft Azure Marketplace，您必須滿足下列先決條件：

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- 可存取 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。 如需詳細資訊，請參閱 [Azure Marketplace 和 AppSource 發行指南](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)。
- 同意 [Azure Marketplace 條款](https://azure.microsoft.com/support/legal/marketplace-terms/)
- 將您的 IoT Edge 模組技術資產裝載於 Azure Container Registry 中。  如需詳細資訊，請參閱[準備 IoT Edge 模組的技術資產](./cpp-create-technical-assets.md)
- 準備好 IoT Edge 模組的中繼資料以供使用。 例如，準備下列資產：
    - 標題
    - 描述 (HTML 格式)
    - 標誌影像 (PNG 格式且影像大小固定，包括 40x40px、90x90px、115x115px、255x115px)
    - 使用規定及隱私權原則
    - 預設的模組設定包括：路由、對應項所需屬性、createOptions 以及環境變數。
    - 模組檔
    - 支援連絡人


## <a name="next-steps"></a>後續步驟

[準備好 IoT Edge 模組技術資產](./cpp-create-technical-assets.md)之後，您就可以開始[建立您的 IoT Edge 模組供應](./cpp-create-offer.md)專案。 
