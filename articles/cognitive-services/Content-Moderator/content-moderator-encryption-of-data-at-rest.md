---
title: 靜態資料的內容管理員加密
titleSuffix: Azure Cognitive Services
description: 內容審閱人對靜態資料進行加密。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: b41d822791f61fce274f628b87c478c3a986f4c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372402"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>靜態資料的內容管理員加密

內容審閱人會在資料保存到雲中時自動加密資料，從而有助於實現組織安全性和合規性目標。

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 客戶管理的金鑰僅在 E0 定價層上可用。 要請求使用客戶管理的金鑰，請填寫並提交[內容審閱人客戶管理金鑰請求表](https://aka.ms/cogsvc-cmk)。 大約需要 3-5 個工作日才能回復您的請求狀態。 根據需求，您可能會被放置在佇列中，並在可用空間時獲得批准。 一旦批准將 CMK 與內容審閱人服務一起使用，您將需要創建新的內容審閱人資源，並選擇 E0 作為定價層。 創建具有 E0 定價層的內容審閱人資源後，可以使用 Azure 金鑰保存庫來設置託管標識。

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>為您的內容檢閱者團隊啟用資料加密

要為內容審閱人審閱團隊啟用資料加密，請參閱[快速入門：嘗試 Web 上的內容檢閱者](quick-start.md#create-a-review-team)。  

> [!NOTE]
> 您需要提供包含內容審閱人 E0 定價層_的資源識別碼。_


## <a name="next-steps"></a>後續步驟

* [內容審閱人客戶管理的關鍵請求表](https://aka.ms/cogsvc-cmk)
* [瞭解有關 Azure 金鑰保存庫的更多](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
