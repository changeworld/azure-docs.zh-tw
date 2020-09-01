---
title: 使用 .NET 進行 Azure Key Vault 的服務對服務驗證
description: 使用 Microsoft.Azure.Services.AppAuthentication 程式庫以利用 .NET 向 Azure Key Vault 進行驗證。
keywords: Azure Key Vault 驗證的本機認證
author: msmbaldwin
services: key-vault
ms.author: mbaldwin
ms.date: 08/08/2020
ms.topic: how-to
ms.service: key-vault
ms.subservice: general
ms.openlocfilehash: 860f9b0e49423b5d144d56ecd965153f7a362d87
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2020
ms.locfileid: "89180910"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>使用 .NET 進行 Azure Key Vault 的服務對服務驗證

> [!NOTE]
> 您不再建議使用新的 Key Vault SDK 來使用**AppAuthentication** 。 它會取代為適用于 .NET、JAVA、TypeScript 和 Python 的新 Azure 身分識別程式庫 **DefaultAzureCredentials** ，而且應該用於所有新的開發。 您可以在這裡找到詳細資訊： [驗證和 AZURE SDK](https://devblogs.microsoft.com/azure-sdk/authentication-and-the-azure-sdk/)。

若要驗證 Azure Key Vault，您需要 Azure Active Directory (Azure AD) 認證（共用密碼或憑證）。

管理這類認證可能很困難。 將認證包含在來源或設定檔中，很容易就能將認證組合到應用程式中。 .NET 程式庫的 `Microsoft.Azure.Services.AppAuthentication` 可簡化此問題。 它會使用開發人員的認證，在本機開發期間進行驗證。 當解決方案在稍後部署至 Azure 時，程式庫會自動切換至應用程式認證。 在本機開發期間使用開發人員認證比較安全，因為您不需要建立 Azure AD 認證或在開發人員之間共用認證。

此連結 `Microsoft.Azure.Services.AppAuthentication` 庫會自動管理驗證，進而讓您專注于解決方案，而不是您的認證。 它支援使用 Microsoft Visual Studio、Azure CLI 或 Azure AD 整合式驗證進行本機開發。 如果部署到支援受控執行個體的 Azure 資源，程式庫會自動使用 [Azure 資源的受控識別](../../active-directory/msi-overview.md)。 不需要任何程式碼或設定變更。 當受控識別無法使用，或在本機開發期間無法判斷開發人員的安全性內容時，程式庫也支援直接使用 Azure AD [用戶端認證](../../azure-resource-manager/resource-group-authenticate-service-principal.md) 。

## <a name="prerequisites"></a>先決條件

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) 或 [Visual Studio 2017 v 15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/)。

- Visual Studio 的應用程式驗證延伸模組，可作為 Visual Studio 2017 Update 5 的個別擴充功能，並隨附于 Update 6 和更新版本的產品。 使用 Update 6 或更新版本時，您可以從 Visual Studio 安裝程式內選取 [Azure 開發工具]，以確認應用程式驗證擴充功能的安裝。

## <a name="using-the-library"></a>使用程式庫

對於 .NET 應用程式，使用受控識別最簡單的方式是透過 `Microsoft.Azure.Services.AppAuthentication` 套件。 如何開始使用：

1. 選取 [**工具**]  >  **nuget 封裝管理員**  >  **管理解決方案的 nuget 套件**，以將[AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)和[KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) nuget 套件的參考新增至您的專案。

1. 新增下列程式碼：

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // Instantiate a new KeyVaultClient object, with an access token to Key Vault
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));

    // Optional: Request an access token to other Azure services
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider2.GetAccessTokenAsync("https://management.azure.com/").ConfigureAwait(false);
    ```

呼叫方法之前，您不需要檢查權杖的到期時間 `GetAccessTokenAsync` ，因為會 `AzureServiceTokenProvider` 在記憶體中快取權杖，並在到期前從 Azure AD 抓取權杖。 

`GetAccessTokenAsync` 方法需要資源識別碼。 若要深入瞭解 Microsoft Azure 服務，請參閱 [什麼是適用于 Azure 資源的受控](../../active-directory/msi-overview.md)識別。

## <a name="local-development-authentication"></a>本機開發驗證

針對本機開發，有兩個主要驗證案例： [向 Azure 服務進行驗證](#authenticating-to-azure-services)，以及 [對自訂服務進行驗證](#authenticating-to-custom-services)。

### <a name="authenticating-to-azure-services"></a>向 Azure 服務驗證

本機電腦不支援適用于 Azure 資源的受控識別。 因此，`Microsoft.Azure.Services.AppAuthentication` 程式庫會使用您的開發人員認證，以在本機開發環境中執行。 當解決方案部署至 Azure 時，程式庫會使用受控識別切換至 OAuth 2.0 用戶端的認證授與流程。 這種方法表示您可以在本機和遠端測試相同的程式碼，而不必擔心。

針對本機開發，`AzureServiceTokenProvider` 會使用 **Visual Studio**、**Azure 命令列介面** (CLI)，或 **Azure AD 整合式驗證**來擷取權杖。 會依序嘗試每個選項，而程式庫會使用第一個成功的選項。 如果沒有選項可用，`AzureServiceTokenProviderException` 會擲回包含詳細資訊的例外狀況。

#### <a name="authenticating-with-visual-studio"></a>使用 Visual Studio 進行驗證

若要使用 Visual Studio 進行驗證：

1. 登入 Visual Studio，並使用 [**工具**] &nbsp; > &nbsp; **選項**開啟 [**選項**]。

1. 選取 [ **Azure 服務驗證**]，選擇本機開發的帳戶，然後選取 **[確定]**。

如果您使用 Visual Studio 遇到問題，例如涉及權杖提供者檔案的錯誤，請仔細檢查先前的步驟。

您可能需要重新驗證您的開發人員權杖。 若要這樣做，請選取 [**工具** &nbsp; > &nbsp; **選項**]，然後選取 [ **Azure &nbsp; 服務 &nbsp; 驗證**]。 在選取的帳戶下尋找 **重新驗證** 連結。 選取該項目並進行驗證。

#### <a name="authenticating-with-azure-cli"></a>使用 Azure CLI 進行驗證

若要使用 Azure CLI 進行本機開發，請確定您有 [Azure CLI v 2.0.12](/cli/azure/install-azure-cli) 版或更新版本的版本。

若要使用 Azure CLI：

1. 在 Windows 工作列中搜尋 Azure CLI，以開啟 **Microsoft Azure 命令提示**字元。

1. 登入 Azure 入口網站： *az login 以登* 入 Azure。

1. 輸入 *az 帳戶存取權杖--resource HTTPs： \/ /vault.azure.net*，以確認存取權。 如果您收到錯誤，請檢查正確的 Azure CLI 版本是否正確安裝。

   如果 Azure CLI 未安裝到預設目錄，您可能會收到錯誤報表，指出找 `AzureServiceTokenProvider` 不到 Azure CLI 的路徑。 使用 **>azureclipath** 環境變數來定義 Azure CLI 安裝資料夾。 必要時，`AzureServiceTokenProvider` 會將 **AzureCLIPath** 環境變數中指定的目錄新增至**路徑**環境變數。

1. 如果您使用多個帳戶登入 Azure CLI，或您的帳戶有權存取多個訂用帳戶，您必須指定要使用的訂用帳戶。 輸入 *az account set--訂用帳戶 <訂用帳戶識別碼>*。

此命令只會在失敗時產生輸出。 若要確認目前的帳戶設定，請輸入命令 `az account list` 。

#### <a name="authenticating-with-azure-ad-authentication"></a>使用 Azure AD authentication 進行驗證

若要使用 Azure AD 驗證，請確認下列事項：

- 您的內部部署 Active Directory 會同步處理至 Azure AD。 如需詳細資訊，請參閱 [什麼是混合式身分識別與 Azure Active Directory？](../../active-directory/connect/active-directory-aadconnect.md)。

- 您的程式碼正在加入網域的電腦上執行。

### <a name="authenticating-to-custom-services"></a>向自訂服務驗證

當服務呼叫 Azure 服務時，上述步驟即會開始作用，因為 Azure 服務允許存取使用者和應用程式。

建立會呼叫自訂服務的服務時，請在本機開發驗證上使用 Azure AD 用戶端認證。 有兩個選項：

- 使用服務主體登入 Azure：

    1. 建立服務主體。 如需詳細資訊，請參閱[使用 Azure CLI 建立 Azure 服務主體](/cli/azure/create-an-azure-service-principal-azure-cli)。

    1. 使用 Azure CLI 以下列命令登入：

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        因為服務主體可能無法存取訂用帳戶，所以使用 `--allow-no-subscriptions` 引數。

- 使用環境變數來指定服務主體詳細資料。 如需詳細資訊，請參閱 [使用服務主體執行應用程式](#running-the-application-using-a-service-principal)。

登入 Azure 之後，會 `AzureServiceTokenProvider` 使用服務主體來取得本機開發的權杖。

這種方法僅適用于本機開發。 當您將解決方案部署至 Azure 時，程式庫會切換為以受控識別進行驗證。

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>使用受控識別或使用者指派的身分識別執行應用程式

當您在 Azure App Service 或啟用受控識別的 Azure VM 上執行程式碼時，程式庫會自動使用受控識別。 不需要變更任何程式碼，但受控識別必須具有金鑰保存庫的 *取得* 許可權。 您可以透過金鑰保存庫的*存取原則*，授與受控識別*取得*許可權。

或者，您也可以使用使用者指派的身分識別進行驗證。 如需使用者指派之身分識別的詳細資訊，請參閱 [關於 Azure 資源的受控](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)識別。 若要使用使用者指派的身分識別進行驗證，您必須在連接字串中指定使用者指派之身分識別的用戶端識別碼。 連接字串是在 [連接字串支援](#connection-string-support)中指定。

## <a name="running-the-application-using-a-service-principal"></a>使用服務主體執行應用程式

這可能需要建立 Azure AD 用戶端認證，才可進行驗證。 這種情況可能會發生在下列範例中：

- 您的程式碼在本機開發環境中執行，但不是在開發人員的身分識別下執行。 例如，Service Fabric 在本機開發上使用 [NetworkService 帳戶](../../service-fabric/service-fabric-application-secret-management.md)。

- 您的程式碼在本機開發環境中執行，而且向自訂服務進行驗證，因此您無法使用您的開發人員身分識別。

- 您的程式碼正在 Azure 計算資源上執行，該資源尚不支援 Azure 資源的受控識別，例如 Azure Batch。

使用服務主體來執行應用程式的主要方法有三種。 若要使用其中任何一項，您必須先建立服務主體。 如需詳細資訊，請參閱[使用 Azure CLI 建立 Azure 服務主體](/cli/azure/create-an-azure-service-principal-azure-cli)。

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>使用本機密鑰儲存區中的憑證來登入 Azure AD

1. 使用 Azure CLI [az ad sp 建立-rbac 命令來](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 建立服務主體憑證。

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    此命令會建立儲存在主目錄中 (私密金鑰) 的 pem 檔案。 請將此憑證部署至 *LocalMachine* 或 *CurrentUser* 存放區。

    > [!Important]
    > CLI 命令會產生 pem 檔案，但 Windows 只提供 PFX 憑證的原生支援。 若要改為產生 PFX 憑證，請使用此處所示的 PowerShell 命令：使用 [自我簽署憑證來建立服務主體](../../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate)。 這些命令也會自動部署憑證。

1. 將名為 **>azureservicesauthconnectionstring** 的環境變數設定為下列值：

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```

    使用步驟 1 產生的值取代 *{AppId}*、*{TenantId}* 和 *{Thumbprint}*。 根據您的部署計畫，以*LocalMachine*' 或*CurrentUser*取代 *{CertificateStore}* 。

1. 執行應用程式。

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>使用共用密碼認證登入 Azure AD

1. 使用 Azure CLI [az ad sp create-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 命令搭配--sdk-auth 參數來建立具有密碼的服務主體憑證。

    ```azurecli
    az ad sp create-for-rbac --sdk-auth
    ```

1. 將名為 **>azureservicesauthconnectionstring** 的環境變數設定為下列值：

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}
    ```

    使用步驟 1 產生的值取代 _{AppId}_、_{TenantId}_ 和 _{ClientSecret}_。

1. 執行應用程式。

當所有項目都正確地設定之後，便不必再變更任何程式碼。 `AzureServiceTokenProvider` 會使用環境變數和憑證來向 Azure AD 進行驗證。

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>使用 Key Vault 中的憑證登入 Azure AD

此選項可讓您在 Key Vault 中儲存服務主體的用戶端憑證，並將其用於服務主體驗證。 您可以針對下列案例使用此選項：

- 本機驗證，您想要使用明確的服務主體來進行驗證，並想要將服務主體認證安全地保留在金鑰保存庫中。 開發人員帳戶必須有金鑰保存庫的存取權。

- 從 Azure 驗證，您可以在其中使用明確的認證，並想要將服務主體認證安全地保留在金鑰保存庫中。 您可以針對跨租使用者案例使用此選項。 受控識別必須有金鑰保存庫的存取權。

受控識別或您的開發人員身分識別必須具有從 Key Vault 取得用戶端憑證的許可權。 AppAuthentication 程式庫會使用抓取的憑證作為服務主體的用戶端認證。

若要使用用戶端憑證進行服務主體驗證：

1. 建立服務主體憑證，並自動將它儲存在您的 Key Vault 中。 使用 Azure CLI [az ad sp keyvault \<keyvaultname> -- \<certificatename> ](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) --------------------------------：

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```

    憑證識別碼將會是格式的 URL。 `https://<keyvaultname>.vault.azure.net/secrets/<certificatename>`

1. `{KeyVaultCertificateSecretIdentifier}`將此連接字串中的取代為憑證識別碼：

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
    ```

    比方說，如果您的金鑰保存庫稱為 *myKeyVault* ，而且您已建立名為 *mycert.cer*的憑證，則憑證識別碼會是：

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert
    ```

## <a name="connection-string-support"></a>連接字串支援

依預設，會依 `AzureServiceTokenProvider` 序嘗試下列驗證方法來取出權杖：

- [適用于 Azure 資源的受控識別](../..//active-directory/managed-identities-azure-resources/overview.md)
- Visual Studio authentication
- [Azure CLI authentication](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)
- [整合式 Windows 驗證](/aspnet/web-api/overview/security/integrated-windows-authentication)

若要控制處理程序，請使用傳遞至 `AzureServiceTokenProvider` 建構函式或 *AzureServicesAuthConnectionString* 環境變數中指定的連接字串。  支援下列選項：

| 連接字串選項 | 案例 | 註解|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | 本機開發 | `AzureServiceTokenProvider` 使用 AzureCli 取得權杖。 |
| `RunAs=Developer; DeveloperTool=VisualStudio` | 本機開發 | `AzureServiceTokenProvider` 使用 Visual Studio 取得權杖。 |
| `RunAs=CurrentUser` | 本機開發 | 不支援 .NET Core。 `AzureServiceTokenProvider` 使用 Azure AD 整合式驗證來取得權杖。 |
| `RunAs=App` | [適用於 Azure 資源的受控識別](../../active-directory/managed-identities-azure-resources/index.yml) | `AzureServiceTokenProvider` 使用受控識別來取得權杖。 |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [適用于 Azure 資源的使用者指派身分識別](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) | `AzureServiceTokenProvider` 使用使用者指派的身分識別來取得權杖。 |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | 自訂服務驗證 | `KeyVaultCertificateSecretIdentifier` 這是憑證的秘密識別碼。 |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| 服務主體 | `AzureServiceTokenProvider` 會使用憑證從 Azure AD 取得權杖。 |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | 服務主體 | `AzureServiceTokenProvider` 會使用憑證從 Azure AD 取得權杖|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | 服務主體 |`AzureServiceTokenProvider` 會使用密碼從 Azure AD 取得權杖。 |

## <a name="samples"></a>範例

若要查看 `Microsoft.Azure.Services.AppAuthentication` 作用中的程式庫，請參閱下列程式碼範例。

- [在執行階段使用受控識別從 Azure Key Vault 擷取秘密](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

- [使用受控識別以程式設計方式從 Azure VM 部署 Azure Resource Manager 範本](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet)。

- [使用 .NET Core 範例和受控識別從 Azure Linux VM 呼叫 Azure 服務](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/)。

## <a name="appauthentication-troubleshooting"></a>AppAuthentication 疑難排解

### <a name="common-issues-during-local-development"></a>在本機開發期間常見的問題

#### <a name="azure-cli-is-not-installed-youre-not-logged-in-or-you-dont-have-the-latest-version"></a>未安裝 Azure CLI、您未登入，或您沒有最新版本

執行 *az 帳戶取得存取權杖* ，以查看 Azure CLI 是否為您顯示權杖。 如果 **找不到這類程式**，請安裝 [最新版本的 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。 系統會提示您登入。

#### <a name="azureservicetokenprovider-cant-find-the-path-for-azure-cli"></a>Azureservicetokenprovider 會找不到 Azure CLI 的路徑

Azureservicetokenprovider 會會尋找 Azure CLI 的預設安裝位置。 如果找不到 Azure CLI，請將環境變數 **>azureclipath** 設定為 Azure CLI 安裝資料夾。 Azureservicetokenprovider 會會將環境變數新增至 Path 環境變數。

#### <a name="youre-logged-into-azure-cli-using-multiple-accounts-the-same-account-has-access-to-subscriptions-in-multiple-tenants-or-you-get-an-access-denied-error-when-trying-to-make-calls-during-local-development"></a>您已使用多個帳戶登入 Azure CLI、相同的帳戶可以存取多個租使用者中的訂用帳戶，或您在本機開發期間收到呼叫拒絕存取的錯誤

使用 Azure CLI，將預設訂用帳戶設定為具有您想要使用之帳戶的訂用帳戶。 訂用帳戶必須與您想要存取的資源位於相同的租使用者： **az account set--訂用帳戶 [訂用帳戶識別碼]**。 如果未看到任何輸出，則會成功。 使用 **az account list**確認正確的帳戶現在是預設值。

### <a name="common-issues-across-environments"></a>跨環境的常見問題

#### <a name="unauthorized-access-access-denied-forbidden-or-similar-error"></a>未經授權的存取、拒絕存取、禁止或類似的錯誤

使用的主體無法存取其嘗試存取的資源。 將資源的存取權授與您的使用者帳戶或 App Service 的 MSI 「參與者」存取權。 哪一個取決於您是在本機電腦上執行範例，還是在 Azure 中部署到您的 App Service。 有些資源（例如金鑰保存庫）也有自己的 [存取](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#data-plane-and-access-policies) 原則，您可以使用這些原則來授與存取權給主體，例如使用者、應用程式和群組。

### <a name="common-issues-when-deployed-to-azure-app-service"></a>部署至 Azure App Service 時常見的問題

#### <a name="managed-identity-isnt-set-up-on-the-app-service"></a>未在 App Service 上設定受控識別

使用 [Kudu debug 主控台](https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/)來檢查環境變數 MSI_ENDPOINT 和 MSI_SECRET 存在。 如果這些環境變數不存在，App Service 上就不會啟用受控識別。

### <a name="common-issues-when-deployed-locally-with-iis"></a>使用 IIS 在本機部署的常見問題

#### <a name="cant-retrieve-tokens-when-debugging-app-in-iis"></a>在 IIS 中偵錯工具時無法取出權杖

根據預設，AppAuth 會在 IIS 的不同使用者內容中執行。 這就是為什麼它沒有存取權，無法使用您的開發人員身分識別來取得存取權杖。 您可以使用下列兩個步驟，將 IIS 設定為以您的使用者內容執行：
- 將 web 應用程式的應用程式集區設定為以您目前的使用者帳戶執行。 請在[這裡](https://docs.microsoft.com/iis/manage/configuring-security/application-pool-identities#configuring-iis-application-pool-identities)參閱詳細資訊
- 將 "setProfileEnvironment" 設定為 "True"。 請參閱 [這裡](https://docs.microsoft.com/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)的詳細資訊。 

    - 移至% windir% \System32\inetsrv\config\applicationHost.config
    - 搜尋 "setProfileEnvironment"。 如果設定為 "False"，請將它變更為 "True"。 如果不存在，請將它新增為 processModel 專案的屬性（ (/configuration/system.applicationHost/applicationPools/applicationPoolDefaults/processModel/@setProfileEnvironment) ），並將它設定為 "True"。

- 深入瞭解 [Azure 資源的受控](../../active-directory/managed-identities-azure-resources/index.yml)識別。
- 深入了解 [Azure AD 驗證案例](../../active-directory/develop/active-directory-authentication-scenarios.md)。
