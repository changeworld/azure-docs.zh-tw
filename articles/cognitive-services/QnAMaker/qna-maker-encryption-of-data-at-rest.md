---
title: 待用資料的加密 QnA Maker
titleSuffix: Azure Cognitive Services
description: 待用資料的加密 QnA Maker。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 99c21ece202f8d9867045d506574dd7718bd455e
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653656"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>待用資料的加密 QnA Maker

QnA Maker 會在將資料保存到雲端時自動加密，以協助符合您的組織安全性和合規性目標。

## <a name="about-encryption-key-management"></a>關於加密金鑰管理

根據預設，您的訂用帳戶會使用 Microsoft 管理的加密金鑰。 您也可以選擇使用自己的金鑰來管理您的訂用帳戶。 客戶管理的金鑰（CMK）提供更大的彈性來建立、輪替、停用及撤銷存取控制。 您也可以審核用來保護資料的加密金鑰。

QnA Maker 使用 Azure 搜尋服務的 CMK 支援。 您需要[使用 Azure Key Vault 在 Azure 搜尋服務中建立 CMK](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)。 此 Azure 實例應該與 QnA Maker 服務相關聯，讓它 CMK 啟用。

> [!IMPORTANT]
> 您的 Azure 搜尋服務服務資源必須在2019年1月之後建立，而且不能在免費（共用）層中。 不支援在 Azure 入口網站中設定客戶管理的金鑰。

## <a name="enable-customer-managed-keys"></a>啟用客戶管理的金鑰

QnA Maker 服務會使用來自 Azure 搜尋服務服務的 CMK。 請遵循下列步驟來啟用 Cmk：

1. 建立新的 Azure 搜尋服務實例，並啟用[Azure 認知搜尋的客戶管理的金鑰必要條件](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites)中所述的必要條件。

   ![查看加密設定](../media/cognitive-services-encryption/qna-encryption-1.png)

2. 當您建立 QnA Maker 資源時，它會自動與 Azure 搜尋服務實例相關聯。 這不能與 CMK 搭配使用。 若要使用 CMK，您必須將在步驟1建立的新建立 Azure 搜尋服務實例建立關聯。 具體而言，您必須 `AzureSearchAdminKey` `AzureSearchName` 在 QnA Maker 資源中更新和。

   ![查看加密設定](../media/cognitive-services-encryption/qna-encryption-2.png)

3. 接下來，建立新的應用程式設定：
   * **名稱**：將此設定為`CustomerManagedEncryptionKeyUrl`
   * **值**：這是您在建立 Azure 搜尋服務實例時，在步驟1中所得到的值。

   ![查看加密設定](../media/cognitive-services-encryption/qna-encryption-3.png)

4. 完成後，請重新開機執行時間。 現在您的 QnA Maker 服務已啟用 CMK。

## <a name="regional-availability"></a>區域可用性

客戶管理的金鑰會在所有 Azure 搜尋服務區域中提供。

## <a name="encryption-of-data-in-transit"></a>傳輸中資料加密

QnA Maker 入口網站會在使用者的瀏覽器中執行。 每個動作都會觸發對個別認知服務 API 的直接呼叫。 因此，QnA Maker 與傳輸中的資料相容。
不過，因為 QnA Maker portal 服務裝載于美國西部，所以它仍然不適合非美國客戶。 

## <a name="next-steps"></a>後續步驟

* [在 Azure Key Vault 中使用 Cmk 加密 Azure 搜尋服務](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [待用資料加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [深入瞭解 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
