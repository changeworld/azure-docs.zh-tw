---
title: 如何保護精靈應用程式
titleSuffix: Azure Maps
description: 使用 Azure 入口網站來管理驗證，以設定信任的 daemon 應用程式。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 5af7645db662a238099e013f84b0dc0fee2af62c
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91355851"
---
# <a name="secure-a-daemon-application"></a>保護 daemon 應用程式

下列指南適用于在受信任且受保護的環境中裝載的背景進程、計時器和工作。 範例包括 Azure Web 作業、Azure 函數應用程式、Windows 服務，以及任何其他可靠的背景服務。

> [!Tip]
> Microsoft 建議您針對生產應用程式，將 Azure Active Directory (Azure AD) 和 Azure 角色型存取控制 (Azure RBAC) 。 如需概念的總覽，請參閱 [Azure 地圖服務 Authentication](./azure-maps-authentication.md)。

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="scenario-shared-key-authentication"></a>案例：共用金鑰驗證

建立 Azure 地圖服務帳戶之後，就會產生主要和次要金鑰。 當您 [使用共用金鑰驗證來呼叫 Azure 地圖服務](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication)時，建議您使用主要金鑰作為訂用帳戶金鑰。 您可以在迴圈金鑰變更之類的案例中使用次要金鑰。 如需詳細資訊，請參閱 [Azure 地圖服務中的驗證](https://aka.ms/amauth)。

### <a name="securely-store-shared-key"></a>安全地儲存共用金鑰

主要和次要金鑰允許對 Maps 帳戶的所有 Api 進行授權。 應用程式應該將金鑰儲存在安全的存放區（例如 Azure Key Vault）。 應用程式必須將共用金鑰取出為 Azure Key Vault 秘密，以避免在應用程式設定中將共用金鑰儲存為純文字。 若要瞭解如何設定 Azure Key Vault，請參閱 [Azure Key Vault 開發人員指南](https://docs.microsoft.com/azure/key-vault/general/developers-guide)。

下列步驟概述此程式：

1. 建立 Azure 金鑰保存庫。
2. 建立應用程式註冊或受控識別來建立 Azure AD 的服務主體，而建立的主體負責存取 Azure Key Vault。
3. 將服務主體存取權指派給 Azure 金鑰秘密 `Get` 許可權。
4. 為 `Set` 您的開發人員暫時指派秘密許可權給您。
5. 在 Key Vault 秘密中設定共用金鑰，並參考秘密識別碼作為背景程式應用程式的設定，並移除秘密 `Set` 許可權。
6. 在背景程式應用程式中執行 Azure AD 驗證，以從 Azure Key Vault 取出共用金鑰秘密。
7. 使用共用金鑰建立 Azure 地圖服務 REST API 要求。

> [!Tip]
> 如果應用程式裝載于 Azure 環境中，您應該執行受控識別，以降低管理秘密以驗證 Azure Key Vault 的成本和複雜度。 請參閱下列 Azure Key Vault [教學課程，以透過受控識別進行](https://docs.microsoft.com/azure/key-vault/general/tutorial-net-create-vault-azure-web-app)連線。

背景程式應用程式會負責從安全的儲存體中取得共用金鑰。 使用 Azure Key Vault 的執行需要透過 Azure AD 進行驗證才能存取秘密。 相反地，我們建議您直接 Azure AD 驗證，以因使用共用金鑰驗證的額外複雜性和操作需求而 Azure 地圖服務。

> [!IMPORTANT]
> 為了簡化金鑰重新產生，我們建議應用程式一次使用一個金鑰。 然後，應用程式可以重新產生未使用的金鑰，並將新重新產生的金鑰部署到安全的秘密存放區（例如 Azure Key Vault）。

## <a name="scenario-azure-ad-role-based-access-control"></a>案例： Azure AD 角色型存取控制

一旦建立 Azure 地圖服務帳戶，Azure 地圖服務 `x-ms-client-id` 值就會出現在 [Azure 入口網站驗證詳細資料] 頁面中。 此值代表將用於 REST API 要求的帳戶。 此值應儲存在應用程式設定中，並在提出 HTTP 要求之前先行取出。 此案例的目標是要讓背景程式應用程式向 Azure AD 進行驗證，並呼叫 Azure 地圖服務 REST Api。

> [!Tip]
> 建議您在 Azure 虛擬機器、虛擬機器擴展集或應用程式服務上裝載，以啟用受控識別元件的優點。

### <a name="daemon-hosted-on-azure-resources"></a>裝載于 Azure 資源的 Daemon

在 Azure 資源上執行時，請設定 Azure 受控識別，以啟用低成本且最少的認證管理工作。 

請參閱 [受控識別的總覽](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) ，讓應用程式能夠存取受控識別。

受控識別權益：

* Azure 系統管理的 X509 憑證公開金鑰加密驗證。
* 使用 X509 憑證（而非用戶端密碼）來 Azure AD 安全性。
* Azure 會管理和更新與受控識別資源相關聯的所有憑證。
* 藉由移除任何對安全秘密存放區服務的需求（例如 Azure Key Vault）來簡化認證作業管理。 

### <a name="daemon-hosted-on-non-azure-resources"></a>非 Azure 資源上託管的 Daemon

在非 Azure 環境受控身分識別上執行時無法使用。 因此，您必須透過應用程式的 Azure AD 應用程式註冊來設定服務主體。

1. 在 Azure 入口網站的 Azure 服務清單中，選取 [ **Azure Active Directory**  >  **應用程式註冊**  >  **新註冊**]。  

    > [!div class="mx-imgBorder"]
    > ![應用程式註冊](./media/how-to-manage-authentication/app-registration.png)

2. 如果您已註冊您的應用程式，請繼續進行下一個步驟。 如果您尚未註冊您的應用程式，請輸入 **名稱**，選擇 **支援帳戶類型**，然後選取 [ **註冊**]。  

    > [!div class="mx-imgBorder"]
    > ![應用程式註冊詳細資料](./media/how-to-manage-authentication/app-create.png)

3. 若要將委派的 API 許可權指派給 Azure 地圖服務，請移至應用程式。 然後，在 [**應用程式註冊**] 下，選取 [ **API 許可權**]  >  **新增許可權**。 在 [ **我的組織使用的 api**] 下，搜尋並選取 [ **Azure 地圖服務**]。

    > [!div class="mx-imgBorder"]
    > ![新增應用程式 API 許可權](./media/how-to-manage-authentication/app-permissions.png)

4. 選取 [ **存取 Azure 地圖服務**旁的核取方塊，然後選取 [ **新增許可權**]。

    > [!div class="mx-imgBorder"]
    > ![選取應用程式 API 許可權](./media/how-to-manage-authentication/select-app-permissions.png)

5. 請完成下列步驟，以建立用戶端密碼或設定憑證。

    * 如果您的應用程式使用伺服器或應用程式驗證，則在您的應用程式註冊頁面上，移至 [ **憑證 & 秘密**]。 然後上傳公開金鑰憑證或選取 [ **新增用戶端**密碼] 來建立密碼。

        > [!div class="mx-imgBorder"]
        > ![建立用戶端密碼](./media/how-to-manage-authentication/app-keys.png)

    * 選取 [ **新增**] 之後，複製秘密並安全地儲存在服務中，例如 Azure Key Vault。 請參閱 [Azure Key Vault 開發人員指南](https://docs.microsoft.com/azure/key-vault/general/developers-guide) ，以安全地儲存憑證或秘密。 您將使用此秘密從 Azure AD 取得權杖。

        > [!div class="mx-imgBorder"]
        > ![新增用戶端密碼](./media/how-to-manage-authentication/add-key.png)

### <a name="grant-role-based-access-for-the-daemon-application-to-azure-maps"></a>將 daemon 應用程式的角色型存取權授與 Azure 地圖服務

您會將建立的受控識別或服務主體指派給一或多個 Azure 地圖服務角色定義，以授與 *azure 角色型存取控制 (AZURE RBAC) * 。 若要查看可用於 Azure 地圖服務的 Azure 角色定義，請移至 [ **存取控制] (IAM) **。 選取 [ **角色**]，然後搜尋以 *Azure 地圖服務*開頭的角色。 這些 Azure 地圖服務角色是您可以授與存取權的角色。

> [!div class="mx-imgBorder"]
> ![檢視可用的角色](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

1. 移至您的 **Azure 地圖服務帳戶**。 選取 [存取控制 (IAM)] > [角色指派]。

    > [!div class="mx-imgBorder"]
    > ![使用 Azure RBAC 授與存取權](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. 在 [ **角色指派** ] 索引標籤上， **新增** 角色指派。 

    > [!div class="mx-imgBorder"]
    > ![螢幕擷取畫面顯示已選取 [新增] 的滾動指派。](./media/how-to-manage-authentication/add-role-assignment.png)

3. 選取內建的 Azure 地圖服務角色定義，例如 **Azure 地圖服務資料讀取器** 或 **Azure 地圖服務資料參與者**。 在 [**指派存取權**] 底下，選取 [使用**使用者指派的受控識別**系統指派的受控識別**Azure AD 使用者、群組或服務主體**] 或 [受控識別]  /  ** **。 選取主體。 然後選取 [儲存]。

    > [!div class="mx-imgBorder"]
    > ![如何新增角色指派](./media/how-to-manage-authentication/how-to-add-role-assignment.png)

4. 您可以在 [角色指派] 索引標籤上確認已套用角色指派。

## <a name="request-token-with-managed-identity"></a>具有受控識別的要求權杖

針對裝載資源設定受控識別之後，請使用 Azure SDK 或 REST API 取得 Azure 地圖服務的權杖，請參閱 [取得存取權杖](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token)的詳細資料。 遵循本指南之後，預期會傳回存取權杖，可用於 REST API 要求。

## <a name="request-token-with-application-registration"></a>使用應用程式註冊要求權杖

註冊您的應用程式並將其與 Azure 地圖服務建立關聯之後，您可以要求存取權杖。

* Azure AD 資源識別碼 `https://atlas.microsoft.com/`
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

如需詳細的範例，請參閱 [Azure AD 的驗證案例](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)。

## <a name="next-steps"></a>後續步驟

尋找您 Azure 地圖服務帳戶的 API 使用計量：
> [!div class="nextstepaction"]
> [檢視使用計量](how-to-view-api-usage.md)

探索示範如何整合 Azure AD 與 Azure 地圖服務的範例：
> [!div class="nextstepaction"]
> [Azure 地圖服務範例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
