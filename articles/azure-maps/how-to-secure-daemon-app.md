---
title: 如何保護精靈應用程式
titleSuffix: Azure Maps
description: 使用 [Azure 入口網站來管理驗證，以設定受信任的 daemon 應用程式。
author: philmea
ms.author: philmea
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 4fa3492b0cd71e61900dc4be150cd0f0169379ac
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84988347"
---
# <a name="secure-a-daemon-application"></a>保護 daemon 應用程式

下列指南適用于在受信任和受保護的環境中裝載的背景進程、計時器和作業。 範例包括 Azure Web 作業、Azure 函數應用程式、Windows 服務，以及任何其他可靠的背景服務。

> [!Tip]
> Microsoft 建議針對生產環境應用程式，實施 Azure Active Directory （Azure AD）和角色型存取控制（RBAC）。 如需概念的總覽，請參閱[Azure 地圖服務驗證](./azure-maps-authentication.md)。

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="scenario-shared-key-authentication"></a>案例：共用金鑰驗證

建立 Azure 地圖服務帳戶之後，就會產生主要和次要金鑰。 當您[使用共用金鑰驗證來呼叫 Azure 地圖服務](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication)時，建議您使用主要金鑰做為訂用帳戶金鑰。 您可以在像是輪流金鑰變更的情況下使用次要金鑰。 如需詳細資訊，請參閱[Azure 地圖服務中的驗證](https://aka.ms/amauth)。

### <a name="securely-store-shared-key"></a>安全地儲存共用金鑰

主要和次要金鑰允許授權給 Maps 帳戶的所有 Api。 應用程式應該將金鑰儲存在安全的存放區中，例如 Azure Key Vault。 應用程式必須以 Azure Key Vault 秘密的形式抓取共用金鑰，以避免將共用金鑰以純文字儲存在應用程式設定中。 若要瞭解如何設定 Azure Key Vault，請參閱[Azure Key Vault 開發人員指南](https://docs.microsoft.com/azure/key-vault/general/developers-guide)。

下列步驟概述此程式：

1. 建立 Azure 金鑰保存庫。
2. 藉由建立應用程式註冊或受控識別來建立 Azure AD 服務主體，建立的主體會負責存取 Azure Key Vault。
3. 將服務主體存取權指派給 Azure 金鑰秘密 `Get` 許可權。
4. `Set`為您的開發人員暫時指派密碼的存取權。
5. 在 [Key Vault 秘密] 中設定共用金鑰，並參考 [秘密識別碼] 作為 [背景程式應用程式] 的設定，並移除您的 [秘密] `Set` 許可權。
6. 在 daemon 應用程式中執行 Azure AD 驗證，以從 Azure Key Vault 抓取共用金鑰密碼。
7. 使用共用金鑰建立 Azure 地圖服務 REST API 要求。

> [!Tip]
> 如果應用程式裝載于 Azure 環境中，您應該執行受控識別，以降低管理密碼以驗證 Azure Key Vault 的成本和複雜度。 請參閱下列 Azure Key Vault[教學課程，以透過受控識別進行連接](https://docs.microsoft.com/azure/key-vault/general/tutorial-net-create-vault-azure-web-app)。

Daemon 應用程式會負責從安全的儲存區中抓取共用金鑰。 具有 Azure Key Vault 的執行需要透過 Azure AD 進行驗證，才能存取秘密。 取而代之的是，我們鼓勵直接 Azure AD RBAC 驗證，因為使用共用金鑰驗證的額外複雜性和操作需求而造成 Azure 地圖服務。

> [!IMPORTANT]
> 為了簡化金鑰重新產生，我們建議應用程式一次使用一個金鑰。 然後，應用程式可以重新產生未使用的金鑰，並將新重新產生的金鑰部署到安全的秘密存放區，例如 Azure Key Vault。

## <a name="scenario-azure-ad-role-based-access-control"></a>案例： Azure AD 以角色為基礎的存取控制

建立 Azure 地圖服務帳戶之後，Azure 地圖服務 `x-ms-client-id` 值會出現在 [Azure 入口網站 authentication 詳細資料] 頁面中。 此值代表將用於 REST API 要求的帳戶。 這個值應該儲存在應用程式設定中，並在提出 HTTP 要求之前先行抓取。 案例的目標是要讓 daemon 應用程式向 Azure AD 進行驗證，並呼叫 Azure 地圖服務 REST Api。

> [!Tip]
> 我們建議您在 Azure 虛擬機器、虛擬機器擴展集或應用程式服務上裝載，以啟用受控識別元件的優點。

### <a name="daemon-hosted-on-azure-resources"></a>裝載在 Azure 資源上的 Daemon

在 Azure 資源上執行時，請設定 Azure 受控識別，以實現低成本、最少的認證管理作業。 

請參閱[受控識別的總覽](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)，讓應用程式能夠存取受控識別。

受控識別優點：

* Azure 系統管理的 X509 憑證公用金鑰密碼編譯驗證。
* 使用 X509 憑證 Azure AD 安全性，而不是用戶端密碼。
* Azure 會管理及更新與受控識別資源相關聯的所有憑證。
* 藉由移除受保護的秘密存放區服務（如 Azure Key Vault）的需求，簡化了認證作業管理。 

### <a name="daemon-hosted-on-non-azure-resources"></a>裝載于非 Azure 資源上的 Daemon

在非 Azure 環境中執行時，無法使用受控識別。 因此，您必須透過適用于 daemon 應用程式的 Azure AD 應用程式註冊來設定服務主體。

1. 在 [Azure 入口網站] 的 [Azure 服務] 清單中，選取 [ **Azure Active Directory**  >  **應用程式註冊**  >  **新增註冊**]。  

    > [!div class="mx-imgBorder"]
    > ![應用程式註冊](./media/how-to-manage-authentication/app-registration.png)

2. 如果您已註冊您的應用程式，請繼續進行下一個步驟。 如果您尚未註冊您的應用程式，請輸入**名稱**，選擇**支援帳戶類型**，然後選取 [**註冊**]。  

    > [!div class="mx-imgBorder"]
    > ![應用程式註冊詳細資料](./media/how-to-manage-authentication/app-create.png)

3. 若要將委派的 API 許可權指派給 Azure 地圖服務，請移至應用程式。 然後在 [**應用程式註冊**] 底下，選取 [ **API 許可權**] [  >  **新增許可權**]。 在 [**我的組織使用的 api**] 底下，搜尋並選取 [ **Azure 地圖服務**]。

    > [!div class="mx-imgBorder"]
    > ![新增應用程式 API 許可權](./media/how-to-manage-authentication/app-permissions.png)

4. 選取 [**存取 Azure 地圖服務**] 旁的核取方塊，然後選取 [**新增許可權**]。

    > [!div class="mx-imgBorder"]
    > ![選取應用程式 API 許可權](./media/how-to-manage-authentication/select-app-permissions.png)

5. 完成下列步驟以建立用戶端密碼或設定憑證。

    * 如果您的應用程式使用伺服器或應用程式驗證，請在您的應用程式註冊頁面上，移至 [憑證] [ **& 密碼**]。 然後，藉由選取 [**新增用戶端密碼**] 來上傳公開金鑰憑證或建立密碼。

        > [!div class="mx-imgBorder"]
        > ![建立用戶端密碼](./media/how-to-manage-authentication/app-keys.png)

    * 選取 [**新增**] 之後，請複製並安全地將密碼儲存在服務中，例如 Azure Key Vault。 請參閱[Azure Key Vault 開發人員指南](https://docs.microsoft.com/azure/key-vault/general/developers-guide)，以安全地儲存憑證或密碼。 您將使用此秘密來取得 Azure AD 的權杖。

        > [!div class="mx-imgBorder"]
        > ![新增用戶端密碼](./media/how-to-manage-authentication/add-key.png)

### <a name="grant-role-based-access-for-the-daemon-application-to-azure-maps"></a>將 daemon 應用程式的角色型存取權授與 Azure 地圖服務

您可以將建立的受控識別或服務主體指派給一或多個 Azure 地圖服務存取控制角色定義，以授與*角色型存取控制*（RBAC）。 若要查看可用於 Azure 地圖服務的 RBAC 角色定義，請移至 **[存取控制（IAM）**]。 選取 [**角色**]，然後搜尋以*Azure 地圖服務*開頭的角色。 這些 Azure 地圖服務角色是您可以授與存取權的角色。

> [!div class="mx-imgBorder"]
> ![檢視可用的角色](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

1. 移至您的**Azure 地圖服務帳戶**。 選取 **[存取控制（IAM）**  >  **角色指派**]。

    > [!div class="mx-imgBorder"]
    > ![授與 RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. 在 [**角色指派**] 索引標籤上，**新增**角色指派。 
    
    > [!div class="mx-imgBorder"]
    > ![新增角色指派](./media/how-to-manage-authentication/add-role-assignment.png)

3. 選取內建的 Azure 地圖服務角色定義，例如**Azure 地圖服務資料讀取器**或**Azure 地圖服務資料參與者**。 在 [**指派存取**權] 底下，選取 [使用者**指派的受控識別**系統指派的受控識別] **Azure AD 使用者、群組或服務主體**或受控識別  /  ** **。 選取 [主體]。 接著，選取 [儲存]。

    > [!div class="mx-imgBorder"]
    > ![新增角色指派](./media/how-to-manage-authentication/how-to-add-role-assignment.png)

4. 您可以在 [角色指派] 索引標籤上確認已套用角色指派。

## <a name="request-token-with-managed-identity"></a>具有受控識別的要求權杖

一旦為裝載資源設定受控識別，請使用 Azure SDK 或 REST API 取得 Azure 地圖服務的權杖，請參閱[取得存取權杖](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token)的詳細資料。 遵循本指南，預期會傳回存取權杖，可用於 REST API 要求。

## <a name="request-token-with-application-registration"></a>應用程式註冊的要求權杖

註冊應用程式並將它與 Azure 地圖服務產生關聯之後，您就可以要求存取權杖。

* Azure AD 資源識別碼`https://atlas.microsoft.com/`
* Azure AD App 識別碼
* Azure AD 租用戶識別碼
* Azure AD App 註冊用戶端密碼

要求：

```http
POST /<Azure AD Tenant ID>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<Azure AD App ID>&resource=https://atlas.microsoft.com/&client_secret=<client secret>&grant_type=client_credentials
```

回應：

```json
{
    "token_type": "Bearer",
    "expires_in": "...",
    "ext_expires_in": "...",
    "expires_on": "...",
    "not_before": "...",
    "resource": "https://atlas.microsoft.com/",
    "access_token": "ey...gw"
}
```

如需更詳細的範例，請參閱[Azure AD 的驗證案例](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)。

## <a name="next-steps"></a>後續步驟

尋找 Azure 地圖服務帳戶的 API 使用計量：
> [!div class="nextstepaction"]
> [檢視使用計量](how-to-view-api-usage.md)

探索示範如何將 Azure AD 與 Azure 地圖服務整合的範例：
> [!div class="nextstepaction"]
> [Azure 地圖服務範例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
