---
title: 自我測試用戶端，用來預先驗證虛擬機器 |Azure Marketplace
description: 如何建立對 Azure Marketplace 的虛擬機器映像進行預先驗證的自我測試用戶端。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: dsindona
ms.openlocfilehash: 9f16d26fa95254282e453cd7bf35d85f8b81ed73
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82143202"
---
# <a name="create-a-self-test-client-to-pre-validate-an-azure-virtual-machine-image"></a>建立用來預先驗證 Azure 虛擬機器映像的自我測試用戶端

> [!IMPORTANT]
> 從2020年4月13日開始，我們會開始將 Azure 虛擬機器供應專案的管理移至合作夥伴中心。 在遷移之後，您將在合作夥伴中心建立和管理您的供應專案。 請遵循[AZURE VM 映射認證](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-image-certification)中的指示來管理您遷移的供應專案。

依據本文的指引，建立使用自我測試 API 的用戶端服務。 您可以使用自我測試 API 預先驗證虛擬機器 (VM)，以確保該機器符合最新的 Azure Marketplace 發佈需求。 此用戶端服務可讓您先對 VM 進行測試，再將供應項目提交給 Microsoft 認證。

## <a name="development-and-testing-overview"></a>開發與測試概觀

作為自我測試程式的一部分，您將建立連線至 Azure Marketplace 的本機用戶端，以驗證在您的 Azure 訂用帳戶中執行的 VM。 VM 可在 Windows 或 Linux 作業系統中執行。

本機用戶端會執行使用自我測試 API 進行驗證的指令碼、傳送連線資訊，並接收測試結果。

用來建立自我測試用戶端的主要步驟包括：

1. 選擇應用程式的 Azure Active Directory (AD) 租用戶。
2. 註冊用戶端應用程式。
3. 建立用戶端的 Azure AD 應用程式的權杖。
4. 將權杖傳遞至自我測試 API。

建立用戶端之後，您即可對 VM 測試該用戶端。

### <a name="self-test-client-authorization"></a>自我測試用戶端授權

下圖顯示使用用戶端認證 (共用密碼或憑證) 的服務對服務呼叫如何進行授權。

![用戶端授權程序](./media/stclient-dev-process.png)

## <a name="the-self-test-client-api"></a>自我測試用戶端 API

自我測試 API 包含僅支援 POST 方法的單一端點。  其結構如下。

```
Uri:             https://isvapp.azurewebsites.net/selftest-vm
Method:          Post
Request Header:  Content-Type: "application/json"
Authorization:   "Bearer xxxx-xxxx-xxxx-xxxxx"
Request body:    The Request body parameters should use the following JSON format:
                 {
                   "DNSName":"XXXX.westus.cloudapp.azure.com",
                   "User":"XXX",
                   "Password":"XXX@1234567",
                   "OS":"XXX",
                   "PortNo":"22",
                   "CompanyName":"ABCD",
                 }
```

下表說明 API 欄位。


|      欄位         |    描述    |
|  ---------------   |  ---------------  |
|  授權     |  「持有人 xxxx-xxxx」字串包含 Azure Active Directory （AD）用戶端權杖，可以使用 PowerShell 來建立。          |
|  DNS 名稱           |  要測試的 VM 所具備的 DNS 名稱    |
|  User              |  用來登入 VM 的使用者名稱         |
|  密碼          |  用來登入 VM 的密碼          |
|  OS                |  VM 的作業系統：`Linux` 或 `Windows`          |
|  PortNo            |  連線至 VM 的開放連接埠號碼。 連接埠號碼是通常是 `22` (適用於 Linux) 和 `5986` (適用於 Windows)。          |
|  |  |

## <a name="consuming-the-api"></a>使用 API

您可以透過 PowerShell 或 cURL 使用自我測試 API。

### <a name="use-powershell-to-consume-the-api-on-the-linux-os"></a>在 Linux OS 上透過 PowerShell 使用 API

若要在 PowerShell 中呼叫 API，請遵循下列步驟：

1. 使用 `Invoke-WebRequest` 命令呼叫 API。
2. 方法為 Post，內容類型為 JSON，如下列程式碼範例和螢幕擷取畫面所示。
3. 指定 JSON 格式的主體參數。

下列程式碼範例顯示對 API 的 PowerShell 呼叫。

```powershell
$accesstoken = "Get token for your Client AAD App"
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Linux"
    "PortNo" = "22"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" -Headers $headers;
$Content = $res | ConvertFrom-Json
```
下列螢幕擷取畫面顯示在 PowerShell 中呼叫 API 的範例。

![在 Linux OS 中使用 PowerShell 呼叫 API](./media/stclient-call-api-ps-linuxvm.png)

使用上述範例後，您將可擷取 JSON 並加以剖析，以取得下列詳細資料：

```powershell
$testresult = ConvertFrom-Json -InputObject (ConvertFrom-Json -InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++)
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}
```

下列螢幕擷取畫面會顯示 `$res.Content`，可為您提供 JSON 格式的測試結果詳細資料。

![以 PowerShell 呼叫 Linux 的 JSON 結果](./media/stclient-pslinux-rescontent-json.png)

下列螢幕擷取畫面顯示在線上 JSON 檢視器 (例如 [Code Beautify](https://codebeautify.org/jsonviewer) 或 [JSON 檢視器](https://jsonformatter.org/json-viewer)) 中檢視 JSON 測試結果的範例。

![以 PowerShell 呼叫 Linux VM 的 JSON 結果](./media/stclient-consume-api-pslinux-json.png)

### <a name="use-powershell-to-consume-the-api-on-the-windows-os"></a>在 Windows OS 上透過 PowerShell 使用 API

若要在 PowerShell 中呼叫 API，請遵循下列步驟：

1. 使用 `Invoke-WebRequest` 命令呼叫 API。
2. 方法為 Post，內容類型為 JSON，如下列程式碼範例和螢幕擷取畫面所示。
3. 建立 JSON 格式的主體參數。

下列程式碼範例顯示對 API 的 PowerShell 呼叫。

```powershell
$accesstoken = "Get token for your Client AAD App"
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Windows"
    "PortNo" = "5986"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" -Headers $headers;
$Content = $res | ConvertFrom-Json
```

下列螢幕擷取畫面顯示在 PowerShell 中呼叫 API 的範例。

![使用 PowerShell 對 Windows VM 呼叫 API](./media/stclient-call-api-ps-windowsvm.png)

使用上述範例後，您將可擷取 JSON 並加以剖析，以取得下列詳細資料：

```powershell
$testresult = ConvertFrom-Json -InputObject (ConvertFrom-Json -InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++)
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}
```

下列螢幕擷取畫面會顯示 `$res.Content`，可為您提供 JSON 格式的測試結果詳細資料。

![以 PowerShell 呼叫 Windows 的 JSON 結果](./media/stclient-pswindows-rescontent-json.png)

下列螢幕擷取畫面顯示在線上 JSON 檢視器中檢視的測試結果。
(例如 [Code Beautify](https://codebeautify.org/jsonviewer)、[JSON 檢視器](https://jsonformatter.org/json-viewer))

![以 PowerShell 呼叫 Windows VM 的 JSON 結果](./media/stclient-consume-api-pswindows-json.png)

### <a name="use-curl-to-consume-the-api-on-the-linux-os"></a>在 Linux OS 上透過 cURL 使用 API

若要使用 cURL 呼叫 API，請遵循下列步驟：

1. 使用 curl 命令呼叫 API。
2. 方法為 Post，內容類型為 JSON，如下列程式碼片段所示。

```
CURL POST -H "Content-Type:application/json"
-H "Authorization: Bearer XXXXXX-Token-XXXXXXXX"
https://isvapp.azurewebsites.net/selftest-vm
-d '{ "DNSName":"XXXX.westus.cloudapp.azure.com", "User":"XXX", "Password":"XXXX@123456", "OS":"Linux", "PortNo":"22", "CompanyName":"ABCD"}'
```

下列畫面顯示使用 curl 呼叫 API 的範例。

![使用 curl 命令呼叫 API](./media/stclient-consume-api-curl.png)

下列螢幕擷取畫面顯示 curl 呼叫產生的 JSON 結果。

![curl 呼叫產生的 JSON 結果](./media/stclient-consume-api-curl-json.png)


## <a name="choose-the-azure-ad-tenant-for-the-app"></a>選擇應用程式的 Azure AD 租用戶

使用下列步驟，選擇您要在其中建立應用程式的 Azure AD 租用戶。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在頂端功能表列上選取您的帳戶，然後在 [目錄] 清單下方，選擇您要在其中註冊應用程式的 Active Directory 租用戶。 或者，選取 [目錄 + 訂用帳戶]**** 圖示，以查看全域訂用帳戶篩選。 下列螢幕擷取畫面顯示此篩選的範例。

   ![選取訂用帳戶篩選](./media/stclient-subscription-filter.png)

3. 在左側導覽列上選取 [所有服務]****，然後選取 [Azure Active Directory]****。

   在下列步驟中，您可能需要租用戶名稱 (或目錄名稱) 或租用戶識別碼 (或目錄識別碼)。

   **若要取得租用戶資訊：**

   在**Azure Active Directory] [總覽**] 中，搜尋 [properties]，然後選取 [**屬性**]。 請以下列螢幕擷取畫面作為範例：

   - **名稱** - 租用戶名稱或目錄名稱
   - **目錄識別碼** - 租用戶識別碼或目錄識別碼，或是使用捲軸來尋找屬性。

   ![Azure Active Directory 屬性頁面](./media/stclient-aad-properties.png)

## <a name="register-the-client-app"></a>註冊用戶端應用程式

使用下列步驟註冊用戶端應用程式。

1. 在左側導覽列上選取 [所有服務]****，然後選取 [應用程式註冊]****。
2. 在 [應用程式註冊]**** 下方，選取 [+ 新增應用程式註冊]****。
3. 在 [建立]**** 下方，提供下列欄位的必要資訊：

   - **名稱**-輸入應用程式的易記名稱。 例如，"SelfTestClient"。
   - **應用程式類型**-選取**Web 應用程式/API**
   - 登**入 URL** -輸入 "HTTPs：\//isvapp.azurewebsites.net/selftest-vm"

4. 選取 [建立]  。
5. 在 [應用程式註冊]**** 或 [註冊的應用程式]**** 下方，複製 [應用程式識別碼]****。

   ![取得應用程式識別碼](./media/stclient-app-id.png)

6. 在已註冊的應用程式工具列中，選取 [設定]****。
7. 選取 [必要權限]**** 以設定應用程式的權限。
8. 在 [必要權限]**** 下方，選取 [+ 新增]****。
9. 在 [新增 API 存取權]**** 下方，選擇 [選取 API]****。
10. 在 [**選取 api**] 底下，輸入「Windows Azure 傳統部署模型」以搜尋 API。
11. 在搜尋結果中選擇 [Windows Azure 傳統部署模型]****，然後按一下 [選取]****。

    ![為應用程式設定多租用戶](./media/stclient-select-api.png)

12. 在 [新增 API 存取權]**** 下方，選擇 [選取權限]****。
13. 選取 **[存取] [Windows Azure 服務管理 API]**。

    ![為應用程式啟用 API 存取](./media/stclient-enable-api-access.png)

14. 按一下 [選取]  。
15. 選取 [完成]  。
16. 在 [設定]**** 下方，選取 [屬性]****。
17. 在 [屬性]**** 下方，向下捲動至 [多租用戶]****。 選取 [是]  。

    ![為應用程式設定多租用戶](./media/stclient-yes-multitenant.png)

18. 選取 [儲存]  。
19. 在 [設定]**** 下方，選取 [金鑰]****。
20. 選取金鑰的 [描述]**** 文字方塊以建立祕密金鑰。 設定下列欄位：

    - 輸入金鑰名稱。 例如，"selftestclient"
    - 在 [**到期**] 下拉式清單中，選取 [1 年]。
    - 選取 [儲存]**** 以產生金鑰。
    - 在 [值]**** 下方複製金鑰。

      >[!Important]
      >結束 [**金鑰**] 表單之後，您將無法看到此金鑰值。

    ![金鑰值表單](./media/stclient-create-key.png)

## <a name="create-the-token-for-the-client-app"></a>建立用戶端應用程式的權杖

您可以透過 OAuth REST API，使用下列任何程式來建立和取得權杖：

- postman
- Linux 中的 cURL
- C&#35;
- PowerShell

### <a name="to-create-and-get-a-token-using-postman"></a>使用 Postman 建立和取得權杖

 若要向 Auth0 要求任何已授權應用程式的權杖，請使用下列格式的[https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token)承載對端點執行 POST 作業：

```
Method Type : POST
Base Url: https://login.microsoftonline.com/common/oauth2/token
```

要求本文中傳入下列參數：

```
Body Content-Type: x-www-form-urlencoded
client_id: XXX (Paste your Application ID of Web App/API Type client AD App)
grant_type: client_credentials
client_secret: XXX (Paste your Secret Key of Web App/API Type client AD App)
resource: https://management.core.windows.net
```

要求標頭中傳入下列參數：

```
Content-Type: application/x-www-form-urlencoded
```

下列螢幕擷取畫面顯示使用 Postman 取得權杖的範例。

![使用 Postman 取得權杖](./media/stclient-postman-get-token.png)

### <a name="to-create-and-get-a-token-using-curl-in-linux"></a>在 Linux 中使用 cURL 建立和取得權杖

若要向 Auth0 要求任何已授權應用程式的權杖，請使用下列格式的[https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token)承載對端點執行 POST 作業：

```
Request:

curl --request POST \
                --url 'https://login.microsoftonline.com/common/oauth2/token' \
                --header "Content-Type:application/x-www-form-urlencoded" \
                --data "grant_type=client_credentials&client_id=XXXXX-XXXX-XXXX-XXXXXXX&client_secret=XXXXXXXXX&resource=https://management.core.windows.net"

Response:

{"token":"UClCUUKxUlkdbhE1cHLz3kyjbIZYVh9eB34A5Q21Y3FPqKGSJs","expires":"2014-02-17 18:46:08"}
```

下列螢幕擷取畫面顯示使用 curl 命令取得權杖的範例。

![使用 curl 命令取得權杖](./media/stclient-curl-get-token.png)

### <a name="to-create-and-get-a-token-using-c35"></a>使用 C&#35; 建立和取得權杖

若要向 Auth0 要求任何已授權應用程式的權杖，請使用下列格式的承載對\/HTTPs：/soamtenant.auth0.com/oauth/token 端點執行 POST 作業：

```csharp
string clientId = "Your Application Id";
string clientSecret = "Your Application Secret";
string audience = "https://management.core.windows.net";
string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, "https://login.microsoftonline.com/common/oauth2/token");
string grantType = "client_credentials";
var client = new RestClient(authority);
var request = new RestRequest(Method.POST);
       request.AddHeader("content-type", "application/x-www-form- urlencoded");

string requestBody = "grant_type=" + grantType + "&" + "client_id=" +   clientId + "&" + "client_secret=" + clientSecret + "&" + "resource=" +  audience;

request.AddParameter("application/x-www-form-urlencoded", requestBody,  ParameterType.RequestBody);

IRestResponse response = client.Execute(request);
var content = response.Content;
var token = JObject.Parse(content)["access_token"];
```

### <a name="to-create-and-get-a-token-using-powershell"></a>使用 PowerShell 建立和取得權杖

若要向 Auth0 要求任何已授權應用程式的權杖，請使用下列格式的承載對\/HTTPs：/soamtenant.auth0.com/oauth/token 端點執行 POST 作業：

```powershell
$clientId = "Application Id of AD Client APP";
$clientSecret = "Secret Key of AD Client APP "
$audience = "https://management.core.windows.net";
$authority = "https://login.microsoftonline.com/common/oauth2/token"
$grantType = "client_credentials";

$requestBody = "grant_type=" + $grantType + "&" + "client_id=" +  $clientId   + "&" + "client_secret=" + $clientSecret + "&" + "resource=" + $audience;

$headers = New-Object  "System.Collections.Generic.Dictionary[[String],[String]]"
             $headers.Add("ContentType", "application/x-www-form-urlencoded")
resp = Invoke-WebRequest -Method Post -Uri $authority -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $requestBody

$token = $resp.Content | ConvertFrom-Json
$token.AccessToken
```

## <a name="pass-the-client-app-token-to-the-api"></a>將用戶端應用程式權杖傳至 API

在授權標頭中使用下列程式碼，將權杖傳至自我測試 API：

```powershell
$redirectUri = 'https://isvapp.azurewebsites.net/selftest-vm'
$accesstoken = 'place your token here'

$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body =
@{
      'DNSName'="XXXX.cloudapp.azure.com";
      'User'="XXX";
      'Password'="XXXX@12345";
      'OS'="Linux";
      'PortNo'="22"
  } | ConvertTo-Json

$result=Invoke-WebRequest -Method Post -Uri $redirectUri -Headers $headers -ContentType 'application/json' -Body $Body
$result
Write-Output 'Test Results:'
$result.Content
```

## <a name="test-your-self-test-client"></a>測試您的自我測試用戶端

若要測試用戶端，請遵循下列步驟：

1. 部署要測試的 VM。
2. 使用您用於授權的用戶端應用程式權杖，呼叫自我測試 API。
3. 取得 JSON 格式的測試結果。

### <a name="test-result-examples"></a>測試結果範例

下列程式碼片段顯示 JSON 格式的測試結果。

**Windows VM 的測試結果：**

```json
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Microsoft Windows Server 2016 Datacenter",
  "OSVersion": "10.0.14393",
  "CreatedDate": "06/01/2018 07:48:04",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "Tes tID": "1.1.4",
      "TestCaseName": "OS Architecture",
      "Description": "Azure supports 64bit Operating System Only.",
      "Result": "Passed",
      "ActualValue": "64 Bit",
      "Re quiredValue": "OS Should be 64 bit"
    },
    {
      "TestID": "1.1.5",
      "TestCaseName": "User account dependency",
      "Description": "Application execution should not have de pendency on administrator account.",
      "Result": "Passed",
      "ActualValue": "isv",
      "RequiredValue": "Logged in user should not be an administrator"
    },
    {
      "TestID": " 1.1.6",
      "TestCaseName": "Failover Cluster",
      "Description": "Windows Server Failover Clustering feature is not yet supported, Application should not have dependency on this feature.",
      "Result": "Passed",
      "ActualValue": "Disabled",
      "RequiredValue": "Failover Clustering feature is disabled"
    },
```

**Linux VM 的測試結果：**

```json
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Ubuntu  16.04",
  "OSVersion": "16.04",
  "CreatedDate": "06/01/2018 07:04:24",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "TestID": "48",
      "TestCaseName": "Bash Hi story",
      "Description": "Bash history files should be cleared before creating the VM image.",
      "Result": "Passed",
      "ActualValue": "No file Exist",
      "RequiredVal ue": "1024"
    },
    {
      "TestID": "39",
      "TestCaseName": "Linux Agent Version",
      "Description": "Azure Linux Agent 2.2.10 and above should be installed. ",
      "Result": "Pas sed",
      "ActualValue": "2.2.20",
      "RequiredValue": "2.2.10"
    },
    {
      "TestID": "40",
      "TestCaseName": "Required Kernel Parameters",
      "Description": "Verifies the following  kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300",
      "Result": "Passed",
      "ActualValue": "Matched Parameter: console=ttyS0,earlypri ntk=ttyS0,rootdelay=300",
      "RequiredValue": "console=ttyS0#earlyprintk=ttyS0#rootdelay=300"
    },
```

## <a name="next-steps"></a>後續步驟

成功測試 Azure 虛擬機器之後，您即可[發佈供應項目](./cpp-publish-offer.md)。
