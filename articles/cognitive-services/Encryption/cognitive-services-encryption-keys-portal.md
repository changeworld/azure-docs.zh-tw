---
title: 使用 Azure 門戶配置客戶管理的金鑰
titleSuffix: Cognitive Services
description: 瞭解如何使用 Azure 金鑰保存庫使用 Azure 門戶配置客戶管理的金鑰。 客戶管理的金鑰使您能夠創建、旋轉、禁用和撤銷存取控制。
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 22bd3afcf30b8b8ebce18b22d5419d49ec8c3b4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053611"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>使用 Azure 金鑰保存庫配置客戶管理的金鑰

您必須使用 Azure 金鑰保存庫來存儲客戶管理的金鑰。 您可以創建自己的金鑰並將其存儲在金鑰保存庫中，也可以使用 Azure 金鑰保存庫 API 生成金鑰。 認知服務資源和金鑰保存庫必須位於同一區域和同一 Azure 活動目錄 （Azure AD） 租戶中，但它們可以位於不同的訂閱中。 有關 Azure 金鑰保存庫的詳細資訊，請參閱[什麼是 Azure 金鑰保存庫？](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

本文演示如何使用[Azure 門戶](https://portal.azure.com/)使用客戶管理的金鑰配置 Azure 金鑰保存庫。 若要瞭解如何使用 Azure 門戶創建金鑰保存庫，請參閱[快速入門：使用 Azure 門戶從 Azure 金鑰保存庫設置和檢索機密](../../key-vault/quick-create-portal.md)。

## <a name="configure-azure-key-vault"></a>設定 Azure Key Vault

使用客戶管理的金鑰需要在金鑰保存庫上設置兩個屬性，**即"虛刪除**"和 **"不清除**"。 預設情況下，這些屬性不會啟用，但可以使用 PowerShell 或 Azure CLI 在新金鑰保存庫或現有金鑰保存庫上啟用。

> [!IMPORTANT]
> 如果未啟用 **"虛刪除**"和 **"不清除"** 屬性，並且刪除了金鑰，則將無法恢復認知服務資源中的資料。

要瞭解如何在現有金鑰保存庫中啟用這些屬性，請參閱以下文章中標題為 **"啟用虛刪除**和**啟用清除保護**"的部分：

- [如何使用虛刪除與PowerShell。](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)
- [如何使用虛刪除與CLI。](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli)

Azure 存儲加密僅支援大小為 2048 的 RSA 金鑰。 有關金鑰的詳細資訊，請參閱["關於 Azure 金鑰保存庫金鑰、機密和證書"](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)中的**金鑰保存庫金鑰**。

## <a name="enable-customer-managed-keys"></a>啟用客戶管理的金鑰

要在 Azure 門戶中啟用客戶管理的金鑰，請按照以下步驟操作：

1. 導航到認知服務資源。
1. 在認知服務資源的 **"設置"** 邊欄選項卡上，按一下 **"加密**"。 選擇 **"客戶託管金鑰"** 選項，如下圖所示。

    ![顯示如何選擇客戶託管金鑰的螢幕截圖](../media/cognitive-services-encryption/selectcmk.png)

## <a name="specify-a-key"></a>指定鍵

啟用客戶管理的金鑰後，您將有機會指定要與認知服務資源關聯的金鑰。

### <a name="specify-a-key-as-a-uri"></a>以 URI 形式指定金鑰

要將鍵指定為 URI，請按照以下步驟操作：

1. 要在 Azure 門戶中查找金鑰 URI，請導航到金鑰保存庫，然後選擇 **"金鑰**"設置。 選擇所需的鍵，然後按一下該鍵以查看其版本。 選擇金鑰版本以查看該版本的設置。
1. 複製提供 URI 的 **"金鑰識別碼"** 欄位的值。

    ![顯示金鑰保存庫金鑰 URI 的螢幕截圖](../media/cognitive-services-encryption/key-uri-portal.png)

1. 在存儲帳戶的**加密**設置中，選擇 **"輸入金鑰 URI"** 選項。
1. 將複製的 URI 粘貼到 **"金鑰 URI"** 欄位中。

   ![顯示如何輸入金鑰 URI 的螢幕截圖](../media/cognitive-services-encryption/ssecmk2.png)

1. 指定包含金鑰保存庫的訂閱。
1. 儲存您的變更。

### <a name="specify-a-key-from-a-key-vault"></a>從金鑰保存庫指定金鑰

要從金鑰保存庫指定金鑰，首先請確保具有包含金鑰的金鑰保存庫。 要從金鑰保存庫指定金鑰，請按照以下步驟操作：

1. 選擇 [從 Key Vault 選取]**** 選項。
1. 選擇包含要使用的金鑰的金鑰保存庫。
1. 從金鑰保存庫中選擇金鑰。

   ![顯示客戶託管金鑰選項的螢幕截圖](../media/cognitive-services-encryption/ssecmk3.png)

1. 儲存您的變更。

## <a name="update-the-key-version"></a>更新金鑰版本

創建金鑰的新版本時，請更新認知服務資源以使用新版本。 請遵循下列步驟：

1. 導航到認知服務資源並顯示**加密**設置。
1. 輸入新金鑰版本的 URI。 或者，您可以選擇金鑰保存庫和金鑰以再次更新版本。
1. 儲存您的變更。

## <a name="use-a-different-key"></a>使用其他鍵

要更改用於加密的金鑰，請按照以下步驟操作：

1. 導航到認知服務資源並顯示**加密**設置。
1. 輸入新金鑰的 URI。 或者，您可以選擇金鑰保存庫並選擇新金鑰。
1. 儲存您的變更。

## <a name="disable-customer-managed-keys"></a>禁用客戶管理的金鑰

禁用客戶管理的金鑰時，您的認知服務資源將使用 Microsoft 管理的金鑰進行加密。 要禁用客戶管理的金鑰，請按照以下步驟操作：

1. 導航到認知服務資源並顯示**加密**設置。
1. 取消選擇"**使用您自己的金鑰**設置"旁邊的核取方塊。

## <a name="next-steps"></a>後續步驟

* [什麼是 Azure 金鑰保存庫](https://docs.microsoft.com/azure/key-vault/key-vault-overview)？
* [認知服務客戶管理的關鍵請求表](https://aka.ms/cogsvc-cmk)
* [靜態資料人臉服務加密](../Face/face-encryption-of-data-at-rest.md)
* [QnA 製造商靜態資料加密](../QnAMaker/qna-maker-encryption-of-data-at-rest.md)
* [靜態資料的語言理解服務加密](../LUIS/luis-encryption-of-data-at-rest.md)
* [靜態資料的內容管理員加密](../Content-Moderator/content-moderator-encryption-of-data-at-rest.md)
* [靜態資料的轉換器加密](../translator/translator-encryption-of-data-at-rest.md)
* [靜態資料的個人化加密](../personalizer/personalizer-encryption-of-data-at-rest.md)
