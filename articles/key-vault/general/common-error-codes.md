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
ms.openlocfilehash: 9ae13b88d767e43c425ceb86d0be455cebc0e6ac
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462517"
---
# <a name="common-error-codes-for-azure-key-vault"></a>Azure Key Vault 的常見錯誤碼

Azure key vault 上的作業可能會傳回下表所列的錯誤碼

| 錯誤碼 | 使用者訊息 |
|--|--|
| VaultAlreadyExists |  您嘗試以指定的名稱建立新的金鑰保存庫失敗，因為該名稱已在使用中。 如果您最近刪除了具有此名稱的金鑰保存庫，它可能仍處於已虛刪除的狀態。 您可以在[此處](./key-vault-recovery.md?tabs=azure-portal#list-recover-or-purge-a-soft-deleted-key-vault)確認它是否 existis 為虛刪除狀態 |
| VaultNameNotValid |  保存庫名稱應為24個字元、英數位元，並以字母開頭 |
| AccessDenied |  您可能缺少存取原則的許可權，無法進行該作業。 |
| ForbiddenByFirewall |  用戶端位址未獲授權，呼叫端不是受信任的服務。 |
| ConflictError |  您正在對相同專案要求多項作業。  |
| RegionNotSupported |  此資源不支援指定的 azure 區域。 |
| SkuNotSupported |  此資源不支援指定的 SKU 類型。 |
| ResourceNotFound |  找不到指定的 azure 資源。 |
| ResourceGroupNotFound | 找不到指定的 azure 資源群組。 |
| CertificateExpired |  檢查憑證的到期日和有效期間。 |


## <a name="next-steps"></a>後續步驟

- 參閱 [Azure Key Vault 開發人員指南](developers-guide.md)
- 深入了 [向金鑰保存庫進行驗證](authentication.md)