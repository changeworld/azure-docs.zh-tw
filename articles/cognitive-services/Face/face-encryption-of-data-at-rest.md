---
title: 待用資料的臉部服務加密
titleSuffix: Azure Cognitive Services
description: 待用資料的臉部服務加密。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 33495dd5b092cb51b3421e7204f3b529077d63b3
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/03/2020
ms.locfileid: "84309028"
---
# <a name="face-service-encryption-of-data-at-rest"></a>待用資料的臉部服務加密

臉部服務會在將資料保存到雲端時，自動將您的資料加密。 臉部服務加密可保護您的資料，並協助您符合組織的安全性和合規性承諾。

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 客戶管理的金鑰僅適用于 E0 定價層。 若要要求能夠使用客戶管理的金鑰，請填寫並提交「[臉部服務客戶管理的金鑰要求」表單](https://aka.ms/cogsvc-cmk)。 大約需要3-5 個工作天的時間，才會收到要求的狀態。 視需求而定，您可能會放在佇列中，並已核准為可用空間。 一旦核准搭配臉部服務使用 CMK，您將需要建立新的臉部資源，並選取 [E0] 作為 [定價層]。 建立具有 E0 定價層的臉部資源之後，您就可以使用 Azure Key Vault 來設定受控識別。

## <a name="regional-availability"></a>區域可用性

客戶管理的金鑰目前適用于下欄區域：

* 美國中南部
* 美國西部 2
* 美國東部
* US Gov 維吉尼亞州

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>後續步驟

* 如需支援 CMK 之服務的完整清單，請參閱[客戶管理的金鑰，以取得認知服務](../encryption/cognitive-services-encryption-keys-portal.md)
* [什麼是 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)？
* [認知服務客戶管理的金鑰要求表單](https://aka.ms/cogsvc-cmk)
