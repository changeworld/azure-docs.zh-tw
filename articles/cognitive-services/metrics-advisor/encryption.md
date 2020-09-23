---
title: 計量顧問服務加密
titleSuffix: Azure Cognitive Services
description: 計量審查程式服務待用資料加密。
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 9d6a2f8a69d41d29e635b03425ed738484d6f408
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934298"
---
# <a name="metrics-advisor-service-encryption-of-data-at-rest"></a>計量顧問服務待用資料加密

當您將資料保存到雲端時，計量審查程式服務會自動將資料加密。 計量顧問服務加密可保護您的資料，並協助您符合組織的安全性和合規性承諾。

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 客戶管理的金鑰僅適用于 E0 定價層。 若要要求能夠使用客戶管理的金鑰，請填寫並提交「 [計量顧問服務客戶管理的金鑰要求」表單](https://aka.ms/cogsvc-cmk)。 大約需要3-5 個工作天的時間，才會收到要求的狀態。 視需求而定，您可能會被放入佇列中，並在可用的空間獲得核准。 一旦核准使用 CMK 搭配計量顧問服務，您將需要建立新的計量建議程式資源，並選取 E0 作為定價層。 一旦建立了具有 E0 定價層的計量建議程式資源之後，您就可以使用 Azure Key Vault 來設定受控識別。

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>下一步

* [計量顧問服務客戶管理的金鑰要求表單](https://aka.ms/cogsvc-cmk)
* [深入瞭解 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
