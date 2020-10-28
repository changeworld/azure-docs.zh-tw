---
title: 搭配媒體服務使用客戶自控金鑰或攜帶您自己的金鑰 (BYOK)
description: 本教學課程說明如何搭配媒體服務儲存體帳戶使用客戶自控金鑰
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: c26da9d888bbcdf72c052fa4bd7fcdf443a2d8f7
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92325843"
---
# <a name="tutorial-use-customer-managed-keys-or-bring-your-own-key-byok-with-media-services"></a>教學課程：搭配媒體服務使用客戶自控金鑰或攜帶您自己的金鑰 (BYOK)

透過 2020-05-01 API，您可以搭配使用客戶自控的 RSA 金鑰與具有系統管理身分識別的媒體服務帳戶。  本教學課程包含 Postman 集合和環境，可將 REST 要求傳送至 Azure 服務。  使用的服務：

- 適用於 Postman 的 Azure Active Directory 應用程式註冊
- Microsoft Graph API
- Azure 儲存體
- Azure 金鑰保存庫
- 媒體服務

在本教學課程中，您將了解如何使用 Postman 來執行下列動作：

> [!div class="checklist"]
> * 取得權杖以搭配 Azure 服務使用。
> * 建立資源群組和儲存體帳戶。
> * 建立具有系統管理身分識別的媒體服務帳戶
> * 建立用來儲存 RSA (客戶自控) 金鑰的 Key Vault，以搭配儲存體帳戶使用。
> * 更新媒體服務帳戶，以將 RSA 金鑰與儲存體帳戶搭配使用。
> * 在 Postman 中使用變數。

如果您沒有 Azure 訂用帳戶，請[建立免費試用帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

- 以適當的權限註冊服務主體。
- 安裝 [Postman](https://www.postman.com)。
- 在 [Azure 範例：media-services-customer-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok) 上下載本教學課程的 Postman 集合

### <a name="register-a-service-principal-with-the-needed-permissions"></a>以所需權限向服務主體註冊

[建立服務主體](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。  請參閱[選項 2](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#authentication-two-options) 以取得服務主體祕密。  請記下祕密，因為一旦您離開入口網站中的秘密頁面，就無法再進行存取。

服務主體需要下列權限，才能執行本教學課程中的工作。

![服務主體所需的權限](./media/tutorial-byok/service-principal-permissions-1.png)

### <a name="install-postman"></a>安裝 Postman

如果您尚未安裝 Postman 來與 Azure 搭配使用，您可以在 [postman.com](https://www.postman.com/) 取得。

### <a name="download-and-import-the-collection"></a>下載並匯入集合

在 [Azure 範例：media-services-customer-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok) 上下載本教學課程的 Postman 集合

## <a name="installation-of-collection-and-environment"></a>安裝集合和環境

1. 執行 Postman。
1. 選取 [匯入]。
1. 選取 [上傳檔案]。
1. 瀏覽至您已儲存集合和環境檔案的位置。
1. 選取集合和環境檔案。
1. 選取 [開啟]  。  (您會看到一則警告，表示檔案不會匯入為 API，而是匯入為集合。  此警告沒什麼問題。  這正是您想要的。)
1. 此集合現在會在您的集合中顯示為 BYOK。
1. 環境變數現在會出現在您的環境中。

### <a name="understand-the-rest-api-requests-in-the-collection"></a>了解集合中的 REST API 要求

集合提供下列 REST API 要求。 要求必須以所提供的順序傳送，因為其大部分的測試指令碼都會針對序列中的下一個 (或後續) 要求動態建立全域變數。 您不需要手動建立全域變數。

在 Postman 中，您會看到這些變數包含在 `{{ }}` 的括弧內。  例如： `{{bearerToken}}` 。

1. 取得 AAD 權杖 - 測試會設定全域變數 *bearerToken*
2. 取得 Graph 權杖 - 測試會設定全域變數 *graphToken*
3. 取得服務主體詳細資料 - 測試會設定全域變數 *servicePrincipalObjectId*
4. 建立儲存體帳戶 - 測試會設定全域變數 *storageAccountId*
5. 建立具有系統管理身分識別的媒體服務帳戶 - 測試會設定全域變數 *principalId*
6. 建立 Key Vault，授與服務主體的存取權 - 測試會設定全域變數 *keyVaultId*
7. 取得 Key Vault 權杖 - 測試會設定全域變數 *keyVaultToken*
8. 在金鑰保存庫中建立 RSA 金鑰 - 測試會設定全域變數 *keyId*
9. 更新媒體服務帳戶以搭配儲存體帳戶使用金鑰 - 此要求沒有測試指令碼。

## <a name="define-environment-variables"></a>定義環境變數

1. 選取環境下拉式清單來切換至您所下載的環境。
1. 在 Postman 中建立您的環境變數。 這些變數也可用來當做包含在 `{{ }}` 括弧中的變數。  例如： `{{tenantId}}` 。

    * *tenantId* = 您的租用戶識別碼
    * *servicePrincipalId* = 以您慣用方法 (入口網站、CLI 等) 建立的服務主體識別碼。
    * *servicePrincipalSecret* = 為服務主體建立的祕密
    * *subscription* = 您的訂用帳戶識別碼
    * *storageName* = 您要提供給儲存體的名稱
    * *accountName* = 您要使用的媒體服務帳戶名稱
    * *keyVaultName* = 您要使用的 Key Vault 名稱
    * *resourceLocation* = centralus (或您想要放置資源的位置。  此集合僅以 centralus 進行測試過。)
    * *resourceGroup* = 資源群組名稱

    下列變數是使用 Azure 資源的標準變數，因此不需要加以變更。

    * *armResource* = `https://management.core.windows.net`
    * *graphResource* = `https://graph.windows.net/`
    * *keyVaultResource* = `https://vault.azure.net`
    * *armEndpoint* = `management.azure.com`
    * *graphEndpoint* = `graph.windows.net`
    * *aadEndpoint* = `login.microsoftonline.com`
    * *keyVaultDomainSuffix* = `vault.azure.net`

## <a name="send-the-requests"></a>傳送要求

定義您的環境變數之後，您可以在上述序列中一次執行一個要求，或使用 Postman 的執行器來執行集合。

## <a name="change-the-key"></a>變更金鑰

媒體服務將會自動偵測金鑰何時變更。  若要對此進行測試，請為相同的金鑰建立另一個金鑰版本。 媒體服務應該會在 15 分鐘內偵測到此金鑰。

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用您所建立的資源，且 **不想繼續產生費用** ，請將資源刪除。

## <a name="next-steps"></a>後續步驟

請前往下一篇文章以了解如何...
> [!div class="nextstepaction"]
> [使用 REST 編碼以 URL 為基礎的遠端檔案及串流處理影片](stream-files-tutorial-with-rest.md)
