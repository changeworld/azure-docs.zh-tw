---
title: 計量顧問服務加密
titleSuffix: Azure Cognitive Services
description: 計量審查程式服務待用資料加密。
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 5d41500a9c53e38cd36f0feba602e0e1baa5da2c
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92909738"
---
# <a name="metrics-advisor-service-encryption-of-data-at-rest"></a>計量顧問服務待用資料加密

當您將資料保存到雲端時，計量審查程式服務會自動將資料加密。 計量顧問服務加密可保護您的資料，並協助您符合組織的安全性和合規性承諾。

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 客戶管理的金鑰僅適用于 E0 定價層。 若要要求能夠使用客戶管理的金鑰，請填寫並提交計量建議程式 [服務 Customer-Managed 金鑰要求表單](https://aka.ms/cogsvc-cmk)。 大約需要3-5 個工作天的時間，才會收到要求的狀態。 視需求而定，您可能會被放入佇列中，並在可用的空間獲得核准。 一旦核准使用 CMK 搭配計量顧問服務，您將需要建立新的計量建議程式資源，並選取 E0 作為定價層。 一旦建立了具有 E0 定價層的計量建議程式資源之後，您就可以使用 Azure Key Vault 來設定受控識別。

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>下一步

* [計量顧問服務 Customer-Managed 金鑰要求表單](https://aka.ms/cogsvc-cmk)
* [深入瞭解 Azure Key Vault](../../key-vault/general/overview.md)