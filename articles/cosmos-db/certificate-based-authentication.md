---
title: 使用 Azure Cosmos DB 和活動目錄進行基於證書的身份驗證
description: 瞭解如何為基於證書的身份驗證配置 Azure AD 標識，以便從 Azure Cosmos DB 訪問金鑰。
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: 085280a8064e4d12ac63939ada7cdb296d47dc70
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365780"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-db-account"></a>Azure AD 標識的基於證書的身份驗證，用於從 Azure Cosmos DB 帳戶訪問金鑰

憑證式驗證可讓您使用 Azure Active Directory (Azure AD) 搭配用戶端憑證，驗證您的用戶端應用程式。 您可以在需要身分識別的電腦 (例如 Azure 中的內部部署機器或虛擬機器) 上執行憑證式驗證。 然後，應用程式可以讀取 Azure Cosmos DB 金鑰，而無需直接在應用程式中使用金鑰。 本文介紹如何創建示例 Azure AD 應用程式，將其配置為基於證書的身份驗證，使用新的應用程式標識登錄到 Azure，然後從 Azure Cosmos 帳戶檢索金鑰。 本文使用 Azure PowerShell 設置標識，並提供一個 C# 示例應用，用於驗證和訪問 Azure Cosmos 帳戶中的金鑰。  

## <a name="prerequisites"></a>Prerequisites

* 安裝[最新版本](/powershell/azure/install-az-ps)的 Azure PowerShell。

* 如果沒有[Azure 訂閱](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，請先創建[一個免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。然後開始。

## <a name="register-an-app-in-azure-ad"></a>在 Azure AD 中註冊應用程式

在此步驟中，您將在 Azure AD 帳戶中註冊示例 Web 應用程式。 此應用程式後來用於從 Azure Cosmos DB 帳戶讀取金鑰。 使用以下步驟註冊應用程式： 

1. 登錄到[Azure 門戶](https://portal.azure.com/)。

1. 打開 Azure**活動目錄**窗格，轉到**應用註冊**窗格，然後選擇 **"新建註冊**"。 

   ![活動目錄中的新應用程式註冊](./media/certificate-based-authentication/new-app-registration.png)

1. 填寫**申請**表，填寫以下詳細資訊：  

   * **名稱**= 為應用程式提供名稱，可以是任何名稱，如"示例應用"。
   * **支援的帳戶類型**–**僅選擇此組織目錄中的帳戶（預設目錄），** 以允許目前的目錄中的資源訪問此應用程式。 
   * **重定向 URL** - 選擇**Web**類型的應用程式並提供託管應用程式的 URL，它可以是任何 URL。 在此示例中，您可以提供測試 URL，例如`https://sampleApp.com`即使應用不存在也沒關係。

   ![註冊示例 Web 應用程式](./media/certificate-based-authentication/register-sample-web-app.png)

1. 在填寫表單後選擇 **"註冊**"。

1. 註冊應用後，記下**應用程式（用戶端）ID**和**物件識別碼，** 您將在後續步驟中使用這些詳細資訊。 

   ![獲取應用程式和物件指示](./media/certificate-based-authentication/get-app-object-ids.png)

## <a name="install-the-azuread-module"></a>安裝 AzureAD 模組

在此步驟中，您將安裝 Azure AD PowerShell 模組。 此模組需要獲取您在上一步中註冊的應用程式的 ID，並將自簽章憑證關聯到該應用程式。 

1. 使用管理員許可權打開 Windows PowerShell ISE。 如果您尚未完成，請安裝 AZ PowerShell 模組並連接到您的訂閱。 如果您有多個訂閱，則可以設置當前訂閱的上下文，如以下命令所示：

   ```powershell
   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. 安裝並導入[AzureAD](/powershell/module/azuread/?view=azureadps-2.0)模組

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>登錄到 Azure AD

在註冊應用程式的位置登錄到 Azure AD。 使用連接 AzureAD 命令登錄到您的帳戶，請在快顯視窗中輸入 Azure 帳戶憑據。 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>建立自我簽署憑證

打開 Windows PowerShell ISE 的另一個實例，並運行以下命令以創建自簽章憑證並讀取與證書關聯的金鑰：

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>創建基於證書的憑據 

接下來運行以下命令以獲取應用程式的物件識別碼 並創建基於證書的憑據。 在此示例中，我們將證書設置為一年後過期，您可以將其設置為任何必需的結束日期。

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

上述命令的輸出類似于下面的螢幕截圖：

![基於證書的憑據創建輸出](./media/certificate-based-authentication/certificate-based-credential-output.png)

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>將 Azure Cosmos 帳戶配置為使用新標識

1. 登錄到[Azure 門戶](https://portal.azure.com/)。

1. 導航到 Azure Cosmos 帳戶，打開**存取控制 （IAM）** 邊欄選項卡。

1. 選擇 **"添加**和**添加角色指派**"。 添加您在上一步中創建的示例應用與**參與者**角色，如以下螢幕截圖所示：

   ![將 Azure 宇宙帳戶配置為使用新標識](./media/certificate-based-authentication/configure-cosmos-account-with-identify.png)

1. 填寫表單後選擇 **"保存"**

## <a name="register-your-certificate-with-azure-ad"></a>使用 Azure AD 註冊您的憑證

可以從 Azure 門戶將基於證書的憑據與 Azure AD 中的用戶端應用程式相關聯。 要關聯憑據，必須上載證書檔，並執行以下步驟：

在用戶端應用程式的 Azure 應用程式註冊中：

1. 登錄到[Azure 門戶](https://portal.azure.com/)。

1. 打開 Azure**活動目錄**窗格，轉到**應用註冊**窗格，然後打開在上一步中創建的示例應用。 

1. 選擇**證書&機密**，然後**上載證書**。 流覽您在上一步中創建的要上載的證書檔。

1. 選取 [加入]****。 上載證書後，將顯示指紋、開始日期和過期值。

## <a name="access-the-keys-from-powershell"></a>從 PowerShell 訪問金鑰

在此步驟中，您將使用創建的應用程式和證書登錄到 Azure 帳戶的金鑰。 

1. 最初清除用於登錄帳戶的 Azure 帳戶憑據。 可以使用以下命令清除憑據：

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. 接下來驗證是否可以使用應用程式的憑據登錄到 Azure 門戶並訪問 Azure Cosmos 資料庫鍵：

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Get-AzCosmosDBAccountKey `
      -ResourceGroupName "<Resource_Group_Name_of_your_Azure_Cosmos_account>" `
      -Name "<Your_Azure_Cosmos_Account_Name>" `
      -Type "Keys"
   ```

前面的命令將顯示 Azure Cosmos 帳戶的主主金鑰和輔助主金鑰。 您可以查看 Azure Cosmos 帳戶的活動日誌，以驗證獲取金鑰請求是否成功，並且事件是否由"示例應用"應用程式啟動。

![驗證 Azure AD 中的獲取金鑰調用](./media/certificate-based-authentication/activity-log-validate-results.png)

## <a name="access-the-keys-from-a-c-application"></a>從 C# 應用程式訪問金鑰 

還可以通過從 C# 應用程式訪問金鑰來驗證此方案。 以下 C# 主控台應用程式，該應用程式可以使用在 Active Directory 中註冊的應用程式訪問 Azure Cosmos DB 金鑰。 在運行代碼之前，請確保更新租戶 Id、用戶端 ID、證書名稱、資源組名稱、訂閱 ID、Azure Cosmos 帳戶名稱詳細資訊。 

```csharp
using System;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Linq;
using System.Net.Http;
using System.Security.Cryptography.X509Certificates;
using System.Threading;
using System.Threading.Tasks;
 
namespace TodoListDaemonWithCert
{
    class Program
    {
        private static string aadInstance = "https://login.windows.net/";
        private static string tenantId = "<Your_Tenant_ID>";
        private static string clientId = "<Your_Client_ID>";
        private static string certName = "<Your_Certificate_Name>";
 
        private static int errorCode = 0;
        static int Main(string[] args)
        {
            MainAync().Wait();
            Console.ReadKey();
 
            return 0;
        } 
 
        static async Task MainAync()
        {
            string authContextURL = aadInstance + tenantId;
            AuthenticationContext authContext = new AuthenticationContext(authContextURL);
            X509Certificate2 cert = ReadCertificateFromStore(certName);
 
            ClientAssertionCertificate credential = new ClientAssertionCertificate(clientId, cert);
            AuthenticationResult result = await authContext.AcquireTokenAsync("https://management.azure.com/", credential);
            if (result == null)
            {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }
 
            string token = result.AccessToken;
            string subscriptionId = "<Your_Subscription_ID>";
            string rgName = "<ResourceGroup_of_your_Cosmos_account>";
            string accountName = "<Your_Cosmos_account_name>";
            string cosmosDBRestCall = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{rgName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2015-04-08";
 
            Uri restCall = new Uri(cosmosDBRestCall);
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Remove("Authorization");
            httpClient.DefaultRequestHeaders.Add("Authorization", "Bearer " + token);
            HttpResponseMessage response = await httpClient.PostAsync(restCall, null);
 
            Console.WriteLine("Got result {0} and keys {1}", response.StatusCode.ToString(), response.Content.ReadAsStringAsync().Result);
        }
 
        /// <summary>
        /// Reads the certificate
        /// </summary>
        private static X509Certificate2 ReadCertificateFromStore(string certName)
        {
            X509Certificate2 cert = null;
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection certCollection = store.Certificates;
 
            // Find unexpired certificates.
            X509Certificate2Collection currentCerts = certCollection.Find(X509FindType.FindByTimeValid, DateTime.Now, false);
 
            // From the collection of unexpired certificates, find the ones with the correct name.
            X509Certificate2Collection signingCert = currentCerts.Find(X509FindType.FindBySubjectName, certName, false);
 
            // Return the first certificate in the collection, has the right name and is current.
            cert = signingCert.OfType<X509Certificate2>().OrderByDescending(c => c.NotBefore).FirstOrDefault();
            store.Close();
            return cert;
        }
    }
}
```

此腳本輸出主主金鑰和輔助主金鑰，如以下螢幕截圖所示：

![c尖銳應用輸出](./media/certificate-based-authentication/csharp-application-output.png)

與上一節類似，您可以查看 Azure Cosmos 帳戶的活動日誌，以驗證獲取金鑰請求事件是否由"示例應用"應用程式啟動。 


## <a name="next-steps"></a>後續步驟

* [使用 Azure Key Vault 保護 Azure Cosmos 金鑰](access-secrets-from-keyvault.md)

* [Azure 宇宙資料庫的安全基線](security-baseline.md)
