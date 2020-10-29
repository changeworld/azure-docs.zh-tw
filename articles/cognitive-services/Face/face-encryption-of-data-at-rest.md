---
title: 靜止資料的臉部服務加密
titleSuffix: Azure Cognitive Services
description: Microsoft 提供 Microsoft 管理的加密金鑰，也可讓您使用自己的金鑰（稱為客戶管理的金鑰）來管理認知服務訂用帳戶， (CMK) 。 本文涵蓋臉部的靜止資料加密，以及如何啟用和管理 CMK。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 8c6f4f7db312355b719deb434bf6a46fa55eec9d
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92912781"
---
# <a name="face-service-encryption-of-data-at-rest"></a>靜止資料的臉部服務加密

當您將資料保存到雲端時，臉部辨識服務會自動將您的資料加密。 臉部服務加密可保護您的資料，並協助您符合組織的安全性和合規性承諾。

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 客戶管理的金鑰僅適用于 E0 定價層。 若要要求使用客戶管理金鑰的能力，請填寫並提交臉部辨識 [服務 Customer-Managed 金鑰要求表單](https://aka.ms/cogsvc-cmk)。 大約需要3-5 個工作天的時間，才會收到要求的狀態。 視需求而定，您可能會被放入佇列中，並在可用的空間獲得核准。 核准使用 CMK 與臉部辨識服務之後，您將需要建立新的臉部資源，並選取 E0 作為定價層。 一旦建立了具有 E0 定價層的臉部資源之後，您就可以使用 Azure Key Vault 來設定受控識別。

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>下一步

* 如需支援 CMK 的服務完整清單，請參閱 [客戶管理的認知服務金鑰](../encryption/cognitive-services-encryption-keys-portal.md)
* [什麼是 Azure Key Vault](../../key-vault/general/overview.md)？
* [認知服務 Customer-Managed 金鑰要求表單](https://aka.ms/cogsvc-cmk)