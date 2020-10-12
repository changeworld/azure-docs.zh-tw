---
title: QnA Maker 待用資料加密
titleSuffix: Azure Cognitive Services
description: Microsoft 提供 Microsoft 管理的加密金鑰，也可讓您使用自己的金鑰（稱為客戶管理的金鑰）來管理認知服務訂用帳戶， (CMK) 。 本文涵蓋 QnA Maker 的靜止資料加密，以及如何啟用和管理 CMK。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: e744423e00377ef763824f6e39865e6b3e8ee475
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89073534"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker 待用資料加密

QnA Maker 在資料保存到雲端時，會自動將您的資料加密，以協助滿足您組織的安全性和合規性目標。

## <a name="about-encryption-key-management"></a>關於加密金鑰管理

根據預設，您的訂用帳戶會使用由 Microsoft 管理的加密金鑰。 您也可以選擇使用您自己的金鑰來管理訂用帳戶，稱為客戶管理的金鑰 (CMK) 。 CMK 提供更大的彈性來建立、輪替、停用及撤銷存取控制。 您也可稽核用來保護資料的加密金鑰。 如果已針對您的訂用帳戶設定 CMK，則會提供雙重加密，以提供第二層保護，並可讓您透過 Azure Key Vault 控制加密金鑰。

QnA Maker 使用 Azure 搜尋服務的 CMK 支援。 您必須 [使用 Azure Key Vault 在 Azure 搜尋服務中建立 CMK](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)。 此 Azure 實例應與 QnA Maker 服務相關聯，使其 CMK 啟用。

> [!IMPORTANT]
> 您的 Azure 搜尋服務資源必須在2019年1月之後建立，且不能位於免費的 (共用) 層。 在 Azure 入口網站中，不支援設定客戶管理的金鑰。

## <a name="enable-customer-managed-keys"></a>啟用客戶管理的金鑰

QnA Maker 服務會使用來自 Azure 搜尋服務的 CMK。 遵循下列步驟以啟用 Cmk：

1. 建立新的 Azure 搜尋服務實例，並啟用 [Azure 認知搜尋客戶管理的重要必要條件](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites)中所述的必要條件。

   ![查看加密設定1](../media/cognitive-services-encryption/qna-encryption-1.png)

2. 當您建立 QnA Maker 資源時，它會自動與 Azure 搜尋服務實例相關聯。 這不能與 CMK 搭配使用。 若要使用 CMK，您必須將在步驟1中建立之新建立的 Azure 搜尋服務的實例產生關聯。 具體來說，您必須 `AzureSearchAdminKey` `AzureSearchName` 在 QnA Maker 資源中更新和。

   ![視圖加密設定2](../media/cognitive-services-encryption/qna-encryption-2.png)

3. 接著，建立新的應用程式設定：
   * **名稱**：將此設定為 `CustomerManagedEncryptionKeyUrl`
   * **值**：這是您在建立 Azure 搜尋服務實例時，于步驟1中取得的值。

   ![視圖加密設定3](../media/cognitive-services-encryption/qna-encryption-3.png)

4. 完成時，請重新開機執行時間。 現在您的 QnA Maker 服務已啟用 CMK。

## <a name="regional-availability"></a>區域可用性

客戶管理的金鑰可在所有 Azure 搜尋區域中使用。

## <a name="encryption-of-data-in-transit"></a>傳輸中資料加密

QnA Maker 入口網站會在使用者的瀏覽器中執行。 每個動作都會觸發對個別認知服務 API 的直接呼叫。 因此，QnA Maker 符合傳輸中的資料。
不過，因為 QnA Maker 入口網站服務是在美國西部託管，所以對於非 US 客戶而言，它仍然不是理想的作法。 

## <a name="next-steps"></a>後續步驟

* [在 Azure Key Vault 中使用 Cmk 在 Azure 搜尋服務中加密](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [待用資料加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [深入瞭解 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)