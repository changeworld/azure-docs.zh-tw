---
title: 使用 .NET 進行 Azure Key Vault 的服務對服務驗證
description: 使用 Microsoft.Azure.Services.AppAuthentication 程式庫以利用 .NET 向 Azure Key Vault 進行驗證。
keywords: Azure Key Vault 驗證的本機認證
author: msmbaldwin
manager: rkarlin
services: key-vault
ms.author: mbaldwin
ms.date: 08/28/2019
ms.topic: conceptual
ms.service: key-vault
ms.subservice: general
ms.openlocfilehash: 6377edf72651c9cddf570d0c6db9d5ceb40409f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270884"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>使用 .NET 進行 Azure Key Vault 的服務對服務驗證

要對 Azure 金鑰保存庫進行身份驗證，需要 Azure 活動目錄 （Azure AD） 憑據（共用金鑰或證書）。

管理此類憑據可能很困難。 通過將憑據包括在源或設定檔中，很容易將憑據捆綁到應用中。 .NET 程式庫的 `Microsoft.Azure.Services.AppAuthentication` 可簡化此問題。 它會使用開發人員的認證，在本機開發期間進行驗證。 當解決方案在稍後部署至 Azure 時，程式庫會自動切換至應用程式認證。 在本地開發期間使用開發人員憑據更安全，因為不需要在開發人員之間創建 Azure AD 憑據或共用憑據。

庫`Microsoft.Azure.Services.AppAuthentication`自動管理身份驗證，這反過來又允許您專注于解決方案，而不是憑據。 它支援使用 Microsoft 視覺化工作室、Azure CLI 或 Azure AD 集成身份驗證進行本地開發。 如果部署到支援受控執行個體的 Azure 資源，程式庫會自動使用 [Azure 資源的受控識別](../active-directory/msi-overview.md)。 不需要任何程式碼或設定變更。 當託管標識不可用或無法在本地開發期間確定開發人員的安全上下文時，該庫還支援直接使用 Azure AD[用戶端憑據](../azure-resource-manager/resource-group-authenticate-service-principal.md)。

## <a name="prerequisites"></a>Prerequisites

- [視覺工作室 2019](https://www.visualstudio.com/downloads/)或[視覺工作室 2017 v15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/).

- Visual Studio 的應用身份驗證擴展，作為 Visual Studio 2017 更新 5 的單獨擴展提供，並在更新 6 及更高版本中與產品捆綁在一起。 使用 Update 6 或更高版本，您可以通過在 Visual Studio 安裝程式中選擇 Azure 開發工具來驗證應用身份驗證擴展的安裝。

## <a name="using-the-library"></a>使用程式庫

對於 .NET 應用程式，使用受控識別最簡單的方式是透過 `Microsoft.Azure.Services.AppAuthentication` 套件。 如何開始使用：

1. 選擇**工具** > **NuGet 包管理器** > **管理 NuGet 包的解決方案**，以添加對[Microsoft.Azure.服務.App 身份驗證](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)和[Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet 包到您的專案的引用。

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

`AzureServiceTokenProvider` 類別會快取記憶體中的權杖，並在到期之前從 Azure AD 擷取權杖。 因此，在調用`GetAccessTokenAsync`方法之前，您不再需要檢查過期。 當您想要使用權杖時，可直接呼叫方法。

`GetAccessTokenAsync` 方法需要資源識別碼。 要瞭解有關 Microsoft Azure 服務的更多內容，請參閱[Azure 資源的託管標識](../active-directory/msi-overview.md)是什麼。

## <a name="local-development-authentication"></a>本機開發驗證

對於本地開發，有兩種主要的身份驗證方案：[對 Azure 服務進行身份驗證](#authenticating-to-azure-services)，另一[種是身份驗證到自訂服務](#authenticating-to-custom-services)。

### <a name="authenticating-to-azure-services"></a>向 Azure 服務驗證

本地電腦不支援 Azure 資源的託管標識。 因此，`Microsoft.Azure.Services.AppAuthentication` 程式庫會使用您的開發人員認證，以在本機開發環境中執行。 當解決方案部署至 Azure 時，程式庫會使用受控識別切換至 OAuth 2.0 用戶端的認證授與流程。 此方法意味著您可以在本地和遠端測試相同的代碼，而不必擔心。

針對本機開發，`AzureServiceTokenProvider` 會使用 **Visual Studio**、**Azure 命令列介面** (CLI)，或 **Azure AD 整合式驗證**來擷取權杖。 會依序嘗試每個選項，而程式庫會使用第一個成功的選項。 如果沒有選項可用，`AzureServiceTokenProviderException` 會擲回包含詳細資訊的例外狀況。

#### <a name="authenticating-with-visual-studio"></a>使用 Visual Studio 進行驗證

要使用視覺化工作室進行身份驗證：

1. 登錄到視覺化工作室並使用**工具**&nbsp;>&nbsp;**選項**打開**選項**。

1. 選擇**Azure 服務身份驗證**，選擇本地開發帳戶，然後選擇 **"確定**"。

如果使用 Visual Studio 遇到問題（例如涉及權杖提供程式檔的錯誤），請仔細查看上述步驟。

您可能需要重新驗證開發人員權杖。 為此，請選擇 **"工具**&nbsp;>&nbsp;**選項**"，然後選擇**Azure&nbsp;&nbsp;服務身份驗證**。 在所選帳戶下查找**重新身份驗證**連結。 選取該項目並進行驗證。

#### <a name="authenticating-with-azure-cli"></a>使用 Azure CLI 進行驗證

要將 Azure CLI 用於本地開發，請確保具有[Azure CLI v2.0.12](/cli/azure/install-azure-cli)版本或更高版本。

要使用 Azure CLI：

1. 在 Windows 工作列中搜索 Azure CLI 以打開**Microsoft Azure 命令提示符**。

1. 登錄到 Azure 門戶 *：az 登錄*以登錄到 Azure。

1. 通過輸入*az 帳戶獲取訪問權杖https://vault.azure.net-資源 來*驗證訪問。 如果收到錯誤，請檢查正確安裝了正確的 Azure CLI 版本。

   如果未將 Azure CLI 安裝到預設目錄，則可能會收到`AzureServiceTokenProvider`找不到 Azure CLI 路徑的錯誤報表。 使用**AzureCLIPath**環境變數定義 Azure CLI 安裝資料夾。 必要時，`AzureServiceTokenProvider` 會將 **AzureCLIPath** 環境變數中指定的目錄新增至**路徑**環境變數。

1. 如果您使用多個帳戶登錄到 Azure CLI，或者您的帳戶有權訪問多個訂閱，則需要指定要使用的訂閱。 輸入命令*az 帳戶集 -- 訂閱<訂閱-id>*。

此命令只會在失敗時產生輸出。 要驗證當前帳戶設置，請輸入命令`az account list`。

#### <a name="authenticating-with-azure-ad-authentication"></a>使用 Azure AD 身份驗證進行身份驗證

若要使用 Azure AD 驗證，請確認下列事項：

- 本地活動目錄同步到 Azure AD。 有關詳細資訊，請參閱什麼是[Azure 活動目錄的混合標識？](../active-directory/connect/active-directory-aadconnect.md)

- 您的代碼在加入域的電腦上運行。

### <a name="authenticating-to-custom-services"></a>向自訂服務驗證

當服務呼叫 Azure 服務時，上述步驟即會開始作用，因為 Azure 服務允許存取使用者和應用程式。

建立會呼叫自訂服務的服務時，請在本機開發驗證上使用 Azure AD 用戶端認證。 有兩個選項：

- 使用服務主體登入 Azure：

    1. 建立服務主體。 如需詳細資訊，請參閱[使用 Azure CLI 建立 Azure 服務主體](/cli/azure/create-an-azure-service-principal-azure-cli)。

    1. 使用 Azure CLI 使用以下命令登錄：

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        因為服務主體可能無法存取訂用帳戶，所以使用 `--allow-no-subscriptions` 引數。

- 使用環境變數來指定服務主體詳細資料。 有關詳細資訊，請參閱[使用服務主體運行應用程式](#running-the-application-using-a-service-principal)。

登錄到 Azure 後，`AzureServiceTokenProvider`使用服務主體檢索本地開發權杖。

這種方法僅適用于當地發展。 當您將解決方案部署至 Azure 時，程式庫會切換為以受控識別進行驗證。

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>使用託管標識或使用者分配的標識運行應用程式

當您在 Azure App Service 或啟用受控識別的 Azure VM 上執行程式碼時，程式庫會自動使用受控識別。 不需要更改代碼，但託管標識必須*具有*金鑰保存庫的許可權。 您可以通過金鑰保存庫的訪問*策略*向託管標識*授予獲取*許可權。

或者，您可以使用使用者分配的標識進行身份驗證。 有關使用者分配標識的詳細資訊，請參閱有關 Azure[資源的託管標識](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)。 要使用使用者分配的標識進行身份驗證，需要在連接字串中指定使用者分配標識的用戶端 ID。 連接字串在[連接字串支援](#connection-string-support)中指定。

## <a name="running-the-application-using-a-service-principal"></a>使用服務主體執行應用程式

這可能需要建立 Azure AD 用戶端認證，才可進行驗證。 這種情況可能發生在以下示例中：

- 您的程式碼在本機開發環境中執行，但不是在開發人員的身分識別下執行。 例如，Service Fabric 在本機開發上使用 [NetworkService 帳戶](../service-fabric/service-fabric-application-secret-management.md)。

- 您的程式碼在本機開發環境中執行，而且向自訂服務進行驗證，因此您無法使用您的開發人員身分識別。

- 代碼在 Azure 計算資源上運行，該資源尚未支援 Azure 資源的託管標識，例如 Azure 批次處理。

使用服務主體運行應用程式有三種主要方法。 要使用其中任何一個，必須首先創建一個服務主體。 如需詳細資訊，請參閱[使用 Azure CLI 建立 Azure 服務主體](/cli/azure/create-an-azure-service-principal-azure-cli)。

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>使用本地金鑰存儲中的證書登錄到 Azure AD

1. 使用 Azure CLI [az ad sp 創建 rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)命令創建服務主體證書。

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    此命令創建存儲在主目錄中的 .pem 檔（私密金鑰）。 將此證書部署到*本地電腦*或*當前使用者*存儲。

    > [!Important]
    > CLI 命令生成 .pem 檔，但 Windows 僅提供對 PFX 證書的本機支援。 要生成 PFX 證書，請使用此處顯示的 PowerShell 命令：[使用自簽章憑證創建服務主體](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate)。 這些命令也自動部署證書。

1. 將名為 Azure**服務Auth連接字串**的環境變數設置為以下值：

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```

    使用步驟 1 產生的值取代 *{AppId}*、*{TenantId}* 和 *{Thumbprint}*。 根據您的部署計畫將 *[憑證存放區]* 替換為*本地電腦*或 *"當前使用者*"。

1. 執行應用程式。

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>使用共用金鑰憑據登錄到 Azure AD

1. 使用具有 --sdk-auth 參數的 Azure CLI [az ad sp 創建 rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)命令創建具有密碼的服務主體證書。

    ```azurecli
    az ad sp create-for-rbac --sdk-auth
    ```

1. 將名為 Azure**服務Auth連接字串**的環境變數設置為以下值：

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}
    ```

    使用步驟 1 產生的值取代 _{AppId}_、_{TenantId}_ 和 _{ClientSecret}_。

1. 執行應用程式。

當所有項目都正確地設定之後，便不必再變更任何程式碼。 `AzureServiceTokenProvider` 會使用環境變數和憑證來向 Azure AD 進行驗證。

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>使用金鑰保存庫中的證書登錄到 Azure AD

此選項允許您在金鑰保存庫中存儲服務主體的用戶端憑證，並將其用於服務主體身份驗證。 對於以下方案，可以使用此選項：

- 本地身份驗證，您希望使用顯式服務主體進行身份驗證，並希望將服務主體憑據安全地保存在金鑰保存庫中。 開發人員帳戶必須有權訪問金鑰保存庫。

- Azure 中的身份驗證，您希望使用顯式憑據，並希望將服務主體憑據安全地保存在金鑰保存庫中。 對於跨租戶方案，可以使用此選項。 託管標識必須有權訪問金鑰保存庫。

託管標識或開發人員標識必須具有從金鑰保存庫檢索用戶端憑證的許可權。 應用身份驗證庫使用檢索到的證書作為服務主體的用戶端憑據。

要將用戶端憑證用於服務主體身份驗證，請使用：

1. 創建服務主體證書並自動將其存儲在金鑰保存庫中。 使用 Azure CLI [az ad sp 創建 rbac \<- keyvault 金鑰\<庫名稱> -- 證書證書名稱> -- 創建-證書 - 跳過分配](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)命令：

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```

    證書識別碼將是格式中的 URL`https://<keyvaultname>.vault.azure.net/secrets/<certificatename>`

1. 在此`{KeyVaultCertificateSecretIdentifier}`連接中用證書識別碼替換字串：

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
    ```

    例如，如果您的金鑰保存庫稱為*myKeyVault，* 並且您創建了名為*myCert 的*證書，則證書識別碼將是：

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert
    ```

## <a name="connection-string-support"></a>連接字串支援

依預設，`AzureServiceTokenProvider` 會使用多個方法來擷取權杖。

若要控制處理程序，請使用傳遞至 `AzureServiceTokenProvider` 建構函式或 *AzureServicesAuthConnectionString* 環境變數中指定的連接字串。

支援下列選項：

| 連接字串選項 | 狀況 | 註解|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | 本機開發 | `AzureServiceTokenProvider`使用 AzureCli 獲取權杖。 |
| `RunAs=Developer; DeveloperTool=VisualStudio` | 本機開發 | `AzureServiceTokenProvider`使用 Visual Studio 獲取權杖。 |
| `RunAs=CurrentUser` | 本機開發 | `AzureServiceTokenProvider`使用 Azure AD 集成身份驗證獲取權杖。 |
| `RunAs=App` | [適用於 Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/index.yml) | `AzureServiceTokenProvider`使用託管標識獲取權杖。 |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Azure 資源的使用者分配標識](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) | `AzureServiceTokenProvider`使用使用者分配的標識獲取權杖。 |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | 自訂服務身份驗證 | `KeyVaultCertificateSecretIdentifier`是證書的秘密識別碼。 |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| 服務主體 | `AzureServiceTokenProvider` 會使用憑證從 Azure AD 取得權杖。 |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | 服務主體 | `AzureServiceTokenProvider` 會使用憑證從 Azure AD 取得權杖|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | 服務主體 |`AzureServiceTokenProvider` 會使用密碼從 Azure AD 取得權杖。 |

## <a name="samples"></a>範例

要查看庫`Microsoft.Azure.Services.AppAuthentication`的操作，請參閱以下代碼示例。

- [在執行階段使用受控識別從 Azure Key Vault 擷取秘密](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

- [使用受控識別以程式設計方式從 Azure VM 部署 Azure Resource Manager 範本](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet)。

- [使用 .NET Core 範例和受控識別從 Azure Linux VM 呼叫 Azure 服務](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/)。

## <a name="appauthentication-troubleshooting"></a>應用身份驗證故障排除

### <a name="common-issues-during-local-development"></a>地方發展過程中的常見問題

#### <a name="azure-cli-is-not-installed-youre-not-logged-in-or-you-dont-have-the-latest-version"></a>未安裝 Azure CLI，您未登錄，或者沒有最新版本

運行*az 帳戶獲取訪問權杖*以查看 Azure CLI 是否為您顯示權杖。 如果說**找不到此類程式**，請安裝[最新版本的 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。 系統會提示您登入。

#### <a name="azureservicetokenprovider-cant-find-the-path-for-azure-cli"></a>AzureServiceToken 提供程式找不到 Azure CLI 的路徑

AzureServiceToken 提供程式在其預設安裝位置查找 Azure CLI。 如果找不到 Azure CLI，則將環境變數**AzureCLIPath**設置為 Azure CLI 安裝資料夾。 AzureServiceToken 提供程式將環境變數添加到路徑環境變數。

#### <a name="youre-logged-into-azure-cli-using-multiple-accounts-the-same-account-has-access-to-subscriptions-in-multiple-tenants-or-you-get-an-access-denied-error-when-trying-to-make-calls-during-local-development"></a>您使用多個帳戶登錄到 Azure CLI，同一帳戶有權訪問多個租戶中的訂閱，或者在本地開發期間嘗試進行呼叫時收到"訪問拒絕"錯誤

使用 Azure CLI，將預設訂閱設置為具有要使用的帳戶的訂閱。 訂閱必須與要訪問的資源位於同一租戶 **：az 帳戶集 -- 訂閱 [訂閱-id]**。 如果未看到輸出，則成功。 驗證正確的帳戶現在是預設使用 az**帳戶清單**。

### <a name="common-issues-across-environments"></a>跨環境的常見問題

#### <a name="unauthorized-access-access-denied-forbidden-or-similar-error"></a>未經授權的訪問、拒絕訪問、禁止或類似錯誤

使用的主體無法存取其嘗試存取的資源。 授予您的使用者帳戶或應用服務的 MSI"貢獻者"對資源的存取權限。 哪個取決於是在本地電腦上運行示例，還是在 Azure 中部署到應用服務。 某些資源（如金鑰保存庫）也有其自己的[訪問策略](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault#data-plane-and-access-policies)，您可以使用授予對主體（如使用者、應用和組）的存取權限。

### <a name="common-issues-when-deployed-to-azure-app-service"></a>部署到 Azure 應用服務時的常見問題

#### <a name="managed-identity-isnt-set-up-on-the-app-service"></a>未在應用服務上設置託管標識

使用[Kudu 調試主控台](https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/)檢查MSI_ENDPOINT和MSI_SECRET存在的環境變數。 如果這些環境變數不存在，則應用服務上未啟用託管標識。

### <a name="common-issues-when-deployed-locally-with-iis"></a>使用 IIS 在本地部署時的常見問題

#### <a name="cant-retrieve-tokens-when-debugging-app-in-iis"></a>在 IIS 中調試應用時無法檢索權杖

預設情況下，AppAuth 在 IIS 中的其他使用者上下文中運行。 這就是為什麼它沒有使用開發人員標識檢索訪問權杖的存取權限。 您可以通過以下兩個步驟將 IIS 配置為與使用者上下文一起運行：
- 將 Web 應用的應用程式池配置為當前使用者帳戶運行。 請在[這裡](https://docs.microsoft.com/iis/manage/configuring-security/application-pool-identities#configuring-iis-application-pool-identities)參閱詳細資訊
- 將"設置設定檔環境"配置為"真實"。 [在此處](https://docs.microsoft.com/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)查看更多資訊。 

    - 轉到 %windir%\System32_inetsrv_應用Host.config
    - 搜索"設置設定檔環境"。 如果設置為"False"，則將其更改為"True"。 如果不存在，則將其作為屬性添加到進程模型元素 （），/configuration/system.applicationHost/applicationPools/applicationPoolDefaults/processModel/@setProfileEnvironment並將其設置為"True"。

- 深入了解 [Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/index.yml)。
- 深入了解 [Azure AD 驗證案例](../active-directory/develop/active-directory-authentication-scenarios.md)。
