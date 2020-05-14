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
ms.openlocfilehash: 1e0275c91b2243132650be7af256071589091c4b
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201936"
---
# <a name="face-service-encryption-of-data-at-rest"></a>待用資料的臉部服務加密

臉部服務會在將資料保存到雲端時，自動將您的資料加密。 臉部服務加密可保護您的資料，並協助您符合組織的安全性和合規性承諾。

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 客戶管理的金鑰僅適用于 E0 定價層。 若要要求能夠使用客戶管理的金鑰，請填寫並提交「[臉部服務客戶管理的金鑰要求」表單](https://aka.ms/cogsvc-cmk)。 大約需要3-5 個工作天的時間，才會收到要求的狀態。 視需求而定，您可能會放在佇列中，並已核准為可用空間。 一旦核准搭配臉部服務使用 CMK，您將需要建立新的臉部資源，並選取 [E0] 作為 [定價層]。 建立具有 E0 定價層的臉部資源之後，您就可以使用 Azure Key Vault 來設定受控識別。

### <a name="regional-availability"></a>區域可用性

客戶管理的金鑰目前適用于下欄區域：

* 美國中南部
* 美國西部 2
* 美國東部
* US Gov 維吉尼亞州

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>後續步驟

* [臉部服務客戶管理的金鑰要求表單](https://aka.ms/cogsvc-cmk)
* [深入瞭解 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


