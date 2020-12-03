---
title: 使用客戶自控金鑰或 BYOK REST API
description: 在本教學課程中，搭配使用客戶自控金鑰或攜帶您自己的金鑰 (BYOK) 與 Azure 媒體服務儲存體帳戶。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: c8a5b682e2ac4879d2181bdb069cf554bad512d9
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498280"
---
# <a name="tutorial-use-customer-managed-keys-or-byok-with-media-services-rest-api"></a>教學課程：搭配使用客戶自控金鑰或 BYOK 與媒體服務 REST API

透過 2020-05-01 API，您可以搭配使用客戶自控的 RSA 金鑰與具有系統管理身分識別的 Azure 媒體服務帳戶。本教學課程包含 Postman 集合和環境，可將 REST 要求傳送至 Azure 服務。 使用的服務包括：

- 適用於 Postman 的 Azure Active Directory (Azure AD) 應用程式註冊
- Microsoft Graph API
- Azure 儲存體
- Azure 金鑰保存庫
- Azure 媒體服務

在本教學課程中，您將了解如何使用 Postman 來執行下列動作：

> [!div class="checklist"]
> - 取得權杖以搭配 Azure 服務使用。
> - 建立資源群組和儲存體帳戶。
> - 建立具有系統管理身分識別的媒體服務帳戶。
> - 建立金鑰保存庫以儲存客戶自控的 RSA 金鑰。
> - 更新媒體服務帳戶，以將 RSA 金鑰與儲存體帳戶搭配使用。
> - 在 Postman 中使用變數。

如果您沒有 Azure 訂用帳戶，請[建立免費試用帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

1. 以適當的權限註冊服務主體。
1. 安裝 [Postman](https://www.postman.com)。
1. 在 [Azure 範例：media-services-customer-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok) 上下載本教學課程的 Postman 集合。

### <a name="register-a-service-principal-with-the-needed-permissions"></a>以所需權限向服務主體註冊

1. [建立服務主體](../../active-directory/develop/howto-create-service-principal-portal.md)。
1. 移至 [[選項2：建立新的應用程式祕密](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options)] 以取得服務主體祕密。

   > [!IMPORTANT]
   >複製並儲存祕密以供稍後使用。 您在入口網站中離開 [秘密] 頁面後，即無法存取祕密。

1. 指派權限給服務主體，如下列螢幕擷取畫面所示：

   :::image type="complex" source="./media/tutorial-byok/service-principal-permissions-1.png" alt-text="顯示服務主體所需權限的螢幕擷取畫面。":::
   權限會依服務、權限名稱、類型和描述來列出。 Azure Key Vault：使用者模擬、委派、Azure Key Vault 的完整存取權。 Azure 服務管理：使用者模擬、委派、以組織使用者身分存取 Azure 服務管理。 Azure 儲存體：使用者模擬、委派、存取 Azure 儲存體。 媒體服務：使用者模擬、委派、存取媒體服務。 Microsoft Graph：user.read、委派、登入和讀取使用者設定檔。
   :::image-end:::

### <a name="install-postman"></a>安裝 Postman

如果您尚未安裝 Postman 來與 Azure 搭配使用，您可以在 [postman.com](https://www.postman.com/) 取得。

### <a name="download-and-import-the-collection"></a>下載並匯入集合

在 [Azure 範例：media-services-customer-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok) 上下載本教學課程的 Postman 集合。

## <a name="install-the-postman-collection-and-environment"></a>安裝 Postman 集合和環境

1. 執行 Postman。
1. 選取 [匯入]。
1. 選取 [上傳檔案]。
1. 移至您用來儲存集合和環境檔案的位置。
1. 選取集合和環境檔案。
1. 選取 [開啟]  。 此時會出現警告，指出檔案不會匯入為 API，而是會匯入為集合。 這個警告沒問題。 這正是您想要的。

集合現在會在您的集合中顯示為 BYOK。 此外，環境變數也會出現在您的環境中。

### <a name="understand-the-rest-api-requests-in-the-collection"></a>了解集合中的 REST API 要求

集合提供下列 REST API 要求。

> [!NOTE]
>
>- 要求必須以提供的順序傳送。
>- 大部分的要求都有測試指令碼，會以動態方式為序列中的下一個要求建立全域變數。
>- 您不需要手動建立全域變數。

在 Postman 中，您會看到這些變數包含在括弧內。 例如： `{{bearerToken}}`。

1. 取得 Azure AD 權杖：測試會設定全域變數 **bearerToken**。
2. 取得 Microsoft Graph 權杖：測試會設定全域變數 **graphToken**。
3. 取得服務主體詳細資料：測試會設定全域變數 **servicePrincipalObjectId**。
4. 建立儲存體帳戶：測試會設定全域變數 **storageAccountId**。
5. 建立具有系統管理身分識別的媒體服務帳戶：測試會設定全域變數 **principalId**。
6. 建立金鑰保存庫以授與服務主體的存取權：測試會設定全域變數 **keyVaultId**。
7. 取得金鑰保存庫權杖：測試會設定全域變數 **keyVaultToken**。
8. 在金鑰保存庫中建立 RSA 金鑰：測試會設定全域變數 **keyId**。
9. 更新媒體服務帳戶，以將金鑰與儲存體帳戶搭配使用：此要求沒有任何測試指令碼。

## <a name="define-environment-variables"></a>定義環境變數

1. 選取環境的下拉式清單，以切換至您所下載的環境。
1. 在 Postman 中建立您的環境變數。 這些變數也會作為包含在括弧內的變數。 例如： `{{tenantId}}`。

    - **tenantId**：您的租用戶識別碼。
    - **servicePrincipalId**：使用慣用方法 (例如入口網站或 CLI) 所建立服務主體的識別碼。
    - **servicePrincipalSecret**：為服務主體建立的秘密。
    - **訂用帳戶**：您的訂用帳戶識別碼。
    - **storageName**：您要提供給儲存體的名稱。
    - **accountName**：您想要使用的媒體服務帳戶名稱。
    - **keyVaultName**：您想要使用的金鑰保存庫名稱。
    - **resourceLocation**：**CentralUs** 位置，或您想要用來放置資源的位置。 此集合僅以 **CentralUs** 進行過測試。
    - **resourceGroup**：資源群組名稱。

    以下是可與 Azure 資源搭配運作的標準變數。 因此，您不需要加以變更。

    - **armResource**：`https://management.core.windows.net`
    - **graphResource**：`https://graph.windows.net/`
    - **keyVaultResource**：`https://vault.azure.net`
    - **armEndpoint**：`management.azure.com`
    - **graphEndpoint**：`graph.windows.net`
    - **aadEndpoint**：`login.microsoftonline.com`
    - **keyVaultDomainSuffix**：`vault.azure.net`

## <a name="send-the-requests"></a>傳送要求

定義環境變數之後，即可一次執行一個上一個序列中的要求。 或者，您也可以使用 Postman 的執行器來執行集合。

## <a name="change-the-key"></a>變更金鑰

金鑰發生變更時，媒體服務會自動偵測到。 請為相同的金鑰建立另一個金鑰版本，以測試此流程。 媒體服務應該會在 15 分鐘內偵測到此金鑰。

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用您所建立的資源，且 *不想繼續產生費用*，請將資源刪除。

## <a name="next-steps"></a>後續步驟

請前往下一篇文章以了解如何：
> [!div class="nextstepaction"]
> [使用 REST 編碼以 URL 為基礎的遠端檔案及串流處理影片](stream-files-tutorial-with-rest.md)