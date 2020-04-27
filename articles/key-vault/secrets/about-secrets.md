---
title: 關於 Azure Key Vault 秘密 - Azure Key Vault
description: 概述 Azure Key Vault REST 介面和開發人員在祕密方面的詳細資料。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: eabfa03aa70f54a967fe256f694ef59ad0fe7ebe
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685442"
---
# <a name="about-azure-key-vault-secrets"></a>關於 Azure Key Vault 秘密

Key Vault 提供儲存祕密 (例如密碼和資料庫連接字串) 的安全機制。

從開發人員的觀點來看，Key Vault API 會以字串的形式接受和傳回秘密值。 在內部，Key Vault 會以八位元序列 (8 位元的位元組) 的形式來儲存和管理祕密，大小上限為 25k 位元組。 Key Vault 服務不提供秘密的語意。 它只會接受資料並加以加密和儲存，然後傳回祕密識別碼 ("id")。 識別碼可用來在稍後擷取秘密。  

對於高度敏感的資料，用戶端應考慮為資料提供更多層的保護。 使用個別的保護金鑰為資料加密再儲存到 Key Vault 中，即是一例。  

Key Vault 也支援秘密的 contentType 欄位。 用戶端可以指定祕密的內容類型，以利在擷取秘密資料時進行解譯。 此欄位的長度上限為 255 個字元。 沒有任何預先定義的值。 建議的用法是作為解譯秘密資料的提示。 例如，實作可能會將密碼和憑證儲存為秘密，然後使用此欄位來區分。 沒有任何預先定義的值。  

## <a name="secret-attributes"></a>秘密屬性

除了秘密內容，您可以指定下列屬性：  

- *exp*：選擇性的 IntDate，預設值為永久 (**forever**)。 Exp  (到期時間) 屬性會識別到期時間，在此時間點或之後「不應」擷取秘密資料，除非在[特定情況下](#date-time-controlled-operations)。 此欄位僅供**參考**用，因為它告知金鑰保存庫的使用者服，特定祕密可能無法使用。 其值必須是包含 IntDate 值的數字。   
- *nbf*：選擇性的 IntDate，預設值為現在 (**now**)。 nbf  (不早於) 屬性會識別一個時間，在此時間之前「不應」擷取秘密資料，除非在[特定情況下](#date-time-controlled-operations)。 此欄位僅供**參考**用。 其值必須是包含 IntDate 值的數字。 
- enabled  ：選擇性的布林值，預設值是 **true**。 此屬性會指定是否可以擷取秘密資料。 當作業發生於 *nbf* 和 *exp* 之間時，enabled 屬性會用來搭配 *nbf* 和 *exp* 使用，只有在 enabled 設定為 **true** 時，才能允許此作業。 發生於 nbf  和 exp  範圍外的作業將自動禁止，除了在[特定情況下](#date-time-controlled-operations)。  

任何包含秘密屬性的回應中，可包含其他唯讀屬性：  

- *created*：選擇性的 IntDate。 created 屬性會指出建立此秘密版本的時間。 若秘密是在新增此屬性之前建立的，則此值為 Null。 其值必須是包含 IntDate 值的數字。  
- *updated*：選擇性的 IntDate。 updated 屬性會指出更新此秘密版本的時間。 若秘密是在新增此屬性之前進行最後一次更新，則此值為 Null。 其值必須是包含 IntDate 值的數字。

### <a name="date-time-controlled-operations"></a>受到日期時間控制的作業

秘密的**取得**作業將會用於尚未生效和過期的秘密 (在 nbf   / exp  範圍外)。 呼叫祕密的**取得**作業來取得尚未生效的秘密，可以用於測試。 擷取 (**取得**) 過期的密碼，可用於復原作業。

## <a name="secret-access-control"></a>秘密存取控制

對於在 Key Vault 中管理的秘密，其存取控制會在 Key Vault 的層級上提供，也就是包含這些秘密的 Key Vault。 在相同 Key Vault 中，秘密的存取控制原則與金鑰的存取控制原則並不相同。 使用者可建立一個或多個保存庫來保存秘密，而且必須維護適當區分和管理秘密的案例。   

下列權限可以在保存庫上的祕密存取控制項目中使用 (以每個主體為基礎)，且密切地對映秘密物件上所允許的作業：  

- 秘密管理作業的權限
  - *get*：讀取祕密  
  - *list*：列出儲存在 Key Vault 中的祕密或祕密版本  
  - *set*：建立祕密  
  - *delete*：刪除祕密  
  - *recover*：復原已刪除的祕密
  - *backup*：備份金鑰保存庫中的祕密
  - *restore*：將備份的祕密還原至金鑰保存庫

- 特殊權限作業的權限
  - *purge*：清除 (永久刪除) 已刪除的祕密

如需使用秘密的詳細資訊，請參閱 [Key Vault REST API 參考中的秘密作業](/rest/api/keyvault)。 如需建立權限的相關資訊，請參閱[保存庫 - 建立或更新](/rest/api/keyvault/vaults/createorupdate)和[保存庫 - 更新存取原則](/rest/api/keyvault/vaults/updateaccesspolicy)。 

## <a name="secret-tags"></a>秘密標記  
您可以將其他應用程式專屬的中繼資料指定為標記形式。 Key Vault 支援最多 15 個標記，各標記可以有 256 個字元的名稱和 256 個字元的值。  

>[!Note]
>如果呼叫者具有*列出*或*取得*權限，其便可讀取標籤。

## <a name="azure-storage-account-key-management"></a>Azure 儲存體帳戶金鑰管理

Key Vault 可管理 Azure 儲存體帳戶金鑰：

- 在內部，Key Vault 可列出 (同步) Azure 儲存體帳戶金鑰。 
- Key Vault 會定期重新產生 (輪替) 金鑰。
- 永遠不會傳回金鑰值以回應呼叫者。
- Key Vault 可管理儲存體帳戶和傳統儲存體帳戶的金鑰。

如需詳細資訊，請參閱 [Azure Key Vault 儲存體帳戶金鑰](../secrets/overview-storage-keys.md))

## <a name="storage-account-access-control"></a>儲存體帳戶存取控制

為使用者或應用程式主體授與在受控儲存體帳戶上執行作業的權限時，可以使用下列權限：  

- 受控儲存體帳戶和 SaS 定義作業的權限
  - *get*：取得儲存體帳戶的相關資訊 
  - *list*：列出由 Key Vault 管理的儲存體帳戶
  - *update*：更新儲存體帳戶
  - *delete*：刪除儲存體帳戶  
  - *recover*：復原已刪除的儲存體帳戶
  - *backup*：備份儲存體帳戶
  - *restore*：將備份的儲存體帳戶還原至 Key Vault
  - *set*：建立或更新儲存體帳戶
  - *regeneratekey*：為儲存體帳戶重新產生指定的金鑰值
  - *getsas*：取得與儲存體帳戶的 SAS 定義有關的資訊
  - *listsas*：列出儲存體帳戶的儲存體 SAS 定義
  - *deletesas*：從儲存體帳戶中刪除 SAS 定義
  - *setsas*：為儲存體帳戶建立或更新新的 SAS 定義/屬性

- 特殊權限作業的權限
  - *purge*：清除 (永久刪除) 受控儲存體帳戶

如需詳細資訊，請參閱 [Key Vault REST API 參考中的儲存體帳戶作業](/rest/api/keyvault)。 如需建立權限的相關資訊，請參閱[保存庫 - 建立或更新](/rest/api/keyvault/vaults/createorupdate)和[保存庫 - 更新存取原則](/rest/api/keyvault/vaults/updateaccesspolicy)。

## <a name="next-steps"></a>後續步驟

- [關於 Key Vault](../general/overview.md)
- [關於金鑰、密碼與憑證](../general/about-keys-secrets-certificates.md)
- [關於金鑰](../keys/about-keys.md)
- [關於憑證](../certificates/about-certificates.md)
- [驗證、要求和回應](../general/authentication-requests-and-responses.md)
- [Key Vault 開發人員指南](../general/developers-guide.md)
