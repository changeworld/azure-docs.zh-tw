---
title: QnA 製造商靜態資料加密
titleSuffix: Azure Cognitive Services
description: QnA 製造商對靜態資料進行加密。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: bd64321a6a7afbac61a63365c77a75120f837e83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372390"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA 製造商靜態資料加密

QnA Maker 會在資料保存到雲中時自動加密資料，從而有助於實現組織安全性和合規性目標。

## <a name="about-encryption-key-management"></a>關於加密金鑰管理

預設情況下，您的訂閱使用 Microsoft 管理的加密金鑰。 還有一個選項可以用自己的金鑰管理訂閱。 客戶管理的金鑰 （CMK） 提供了創建、旋轉、禁用和撤銷存取控制的更大靈活性。 您還可以審核用於保護資料的加密金鑰。

QnA 製造商使用 Azure 搜索的 CMK 支援。 您需要[使用 Azure 金鑰保存庫在 Azure 搜索中創建 CMK。](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys) 此 Azure 實例應與 QnA Maker 服務關聯，使其啟用 CMK。

> [!IMPORTANT]
> Azure 搜索服務資源必須在 2019 年 1 月之後創建，並且不能位於免費（共用）層中。 不支援在 Azure 門戶中配置客戶管理的金鑰。

## <a name="enable-customer-managed-keys"></a>啟用客戶管理的金鑰

QnA Maker 服務使用 Azure 搜索服務的 CMK。 按照以下步驟啟用 CMK：

1. 創建新的 Azure 搜索實例，並啟用[客戶管理 Azure 認知搜索的關鍵先決條件中提到的先決條件](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites)。

   ![查看加密設定](../media/cognitive-services-encryption/qna-encryption-1.png)

2. 創建 QnA Maker 資源時，它會自動與 Azure 搜索實例關聯。 這不能與 CMK 一起使用。 要使用 CMK，您需要關聯步驟 1 中創建的新創建的 Azure 搜索實例。 具體來說，您需要更新 QnA `AzureSearchAdminKey` Maker`AzureSearchName`資源中的 和。

   ![查看加密設定](../media/cognitive-services-encryption/qna-encryption-2.png)

3. 接下來，創建新的應用程式設定：
   * **名稱**： 將此設置為`CustomerManagedEncryptionKeyUrl`
   * **值**：這是創建 Azure 搜索實例時在步驟 1 中獲得的值。

   ![查看加密設定](../media/cognitive-services-encryption/qna-encryption-3.png)

4. 完成後，重新開機運行時。 現在，您的 QnA 製造商服務已啟用 CMK。

## <a name="regional-availability"></a>區域可用性

客戶管理的金鑰在所有 Azure 搜索區域都可用。

## <a name="next-steps"></a>後續步驟

* [使用 Azure 金鑰保存庫中的 CMK 在 Azure 搜索中進行加密](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [靜態資料加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [瞭解有關 Azure 金鑰保存庫的更多](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
