---
title: Azure Key Vault 的常見錯誤碼 |Microsoft Docs
description: Azure Key Vault 的常見錯誤碼
services: key-vault
author: sebansal
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 09/29/2020
ms.author: mbaldwin
ms.openlocfilehash: 25c79229a09db912903fba825f0d48f571880745
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876902"
---
# <a name="common-error-codes-for-azure-key-vault"></a>Azure Key Vault 的常見錯誤碼

Azure key vault 上的作業可能會傳回下表所列的錯誤碼

| 錯誤碼 | 使用者訊息 |
|--|--|
| VaultAlreadyExists |  指定的金鑰保存庫已存在於虛刪除狀態中，或在另一個訂用帳戶) 中 (。 |
| VaultNameNotValid |  保存庫名稱應為24個字元、英數位元，並以字母開頭 |
| AccessDenied |  您可能缺少存取原則的許可權，無法進行該作業。 |
| ForbiddenByFirewall |  用戶端位址未獲授權，呼叫端不是受信任的服務。 |
| ConflictError |  您正在對相同專案要求多項作業。  |
| RegionNotSupported |  此資源不支援指定的 azure 區域。 |
| SkuNotSupported |  此資源不支援指定的 SKU 類型。 |
| ResourceNotFound |  找不到指定的 azure 資源。 |
| CertificateExpired |  檢查憑證的到期日和有效期間。 |


## <a name="next-steps"></a>後續步驟

- 參閱 [Azure Key Vault 開發人員指南](developers-guide.md)
- 深入了 [向金鑰保存庫進行驗證](authentication.md)
