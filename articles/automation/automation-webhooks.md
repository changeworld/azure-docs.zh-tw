---
title: 使用 Webhook 啟動 Azure 自動化 Runbook
description: 可讓用戶端透過 HTTP 呼叫在 Azure 自動化中啟動 Runbook 的 Webhook。  本文說明如何建立 Webhook，以及如何進行呼叫以啟動 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 01/16/2020
ms.topic: conceptual
ms.openlocfilehash: 8cb641f95e7327e80f42df86a56eba8c34e7e598
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367018"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>使用 Webhook 啟動 Azure 自動化 Runbook

Webhook 允許外部服務通過單個 HTTP 要求在 Azure 自動化中啟動特定 Runbook。 外部服務包括 Azure DevOps 服務、GitHub、Azure 監視器日誌和自訂應用程式。 此類服務可以使用 Webhook 啟動 Runbook，而無需使用 Azure 自動化 API 實現完整解決方案。 您可以將 Webhook 與在[Azure 自動化 中啟動 Runbook](automation-starting-a-runbook.md)中啟動 Runbook 的其他方法進行比較。

> [!NOTE]
> 不支援使用 Webhook 啟動 Python 運行簿。

![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶，可以使用["如何更新 Azure 自動化 中的 Azure PowerShell"模組](automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="webhook-properties"></a>網鉤屬性

下表描述您必須為 Webhook 設定的屬性。

| 屬性 | 描述 |
|:--- |:--- |
| 名稱 |網鉤的名稱。 您可以提供所需的任何名稱，因為它不會暴露給用戶端。 該名稱僅供您用來識別 Azure 自動化中的 Runbook。 最佳做法是您給予 Webhook 的名稱應該與要使用它的用戶端相關。 |
| URL |網址的網址。 這是用戶端使用 HTTP POST 調用的唯一位址，用於啟動連結到 Webhook 的 Runbook。 當您建立 Webhook 時其會自動產生。 您無法指定自訂 URL。 <br> <br> 該 URL 包含一個安全權杖，允許協力廠商系統調用 Runbook，無需進一步身份驗證。 因此，您應該將 URL 視為密碼。 出於安全原因，您只能在創建 Webhook 時在 Azure 門戶中查看 URL。 請在安全的位置記下 URL 以供日後使用。 |
| 到期日期 | Webhook 的到期日期，之後不能再使用它。 只要 Webhook 尚未過期，就可以在創建 Webhook 後修改到期日期。 |
| 啟用 | 設置，指示創建 Webhook 時是否預設啟用。 如果將此屬性設置為"已禁用"，則任何用戶端都無法使用 Webhook。 您可以在創建 Webhook 或 Webhook 創建後的任何其他時間設置此屬性。 |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Webhook 啟動 Runbook 時使用的參數

Webhook 可以為 Runbook 開始時使用的 Runbook 參數定義值。 Webhook 必須包含任何必需 Runbook 參數的值，並且可以包含可選參數的值。 即使在創建 Webhook 後，也可以修改配置為 Webhook 的參數值。 連結到單個 Runbook 的多個 Webhook 可以每個 Webbook 參數值不同。 當用戶端使用 Webhook 啟動 Runbook 時，其無法覆寫 Webhook 中定義的參數值。

要從用戶端接收資料，Runbook 支援名為 的`WebhookData`單個參數。 此參數定義包含用戶端在 POST 請求中包含的資料的物件。

![Webhook 資料屬性](media/automation-webhooks/webhook-data-properties.png)

`WebhookData` 參數具有下列屬性：

| 屬性 | 描述 |
|:--- |:--- |
| `WebhookName` | 網鉤的名稱。 |
| `RequestHeader` | 包含傳入 POST 請求標頭的雜湊表。 |
| `RequestBody` | 傳入 POST 請求的正文。 此正文保留任何資料格式，如字串、JSON、XML 或表單編碼。 Runbook 必須撰寫用來搭配預期的資料格式。 |

不支援`WebhookData`該參數不需要 Webhook 的配置，並且運行簿不需要接受它。 如果 Runbook 未定義參數，則忽略從用戶端發送的請求的任何詳細資訊。

> [!NOTE]
> 調用 Webhook 時，用戶端應始終存儲任何參數值，以防調用失敗。 如果存在網路中斷或連接問題，應用程式無法檢索失敗的 Webhook 調用。

如果在 Webhook 創建`WebhookData`時指定值，則當 Webhook 使用用戶端 POST 請求中的資料啟動 Runbook 時，將重寫該值。 即使應用程式不包含請求正文中的任何資料，也會發生這種情況。 

如果啟動`WebhookData`使用 Webhook 以外的機制定義的 Runbook，則可以為`WebhookData`Runbook 識別的值。 此值應是具有與`WebhookData`參數相同的[屬性](#webhook-properties)的物件，以便 Runbook 可以處理它，就像它處理 Webhook 傳遞的實際`WebhookData`物件一樣。

例如，如果要從 Azure 門戶啟動以下 Runbook，並且想要傳遞一些示例 Webhook 資料進行測試，則必須在使用者介面中傳遞 JSON 中的資料。

![來自 UI 的 WebhookData 參數](media/automation-webhooks/WebhookData-parameter-from-UI.png)

對於下一個 Runbook 示例，讓我們為`WebhookData`定義以下屬性：

* **網鉤名稱**： 我的網路鉤
* **請求正文**：`*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

現在我們在 UI 中傳遞以下 JSON 物件`WebhookData`。 此示例使用回車符和分行符號，與從 Webhook 傳入的格式匹配。

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![啟動來自 UI 的 WebhookData 參數](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Azure 自動化使用 Runbook 作業記錄所有輸入參數的值。 因此，用戶端在 Webhook 請求中提供的任何輸入都記錄下來，並且可供有權訪問自動化作業的任何人使用。 基於這個原因，您在 Webhook 呼叫中包含機密資訊時應該特別謹慎。

## <a name="webhook-security"></a>網路鉤安全性

Webhook 的安全性依賴于其 URL 的隱私，該 URL 包含允許調用 Webhook 的安全權杖。 Azure 自動化不對請求執行任何身份驗證，只要對正確的 URL 進行身份驗證。 因此，如果不使用驗證請求的替代方法，用戶端不應對執行高度敏感操作的 Runbook 使用 Webhook。

可以在 Runbook 中包含邏輯，以確定它是否由 Webhook 調用。 讓 Runbook 檢查`WebhookName`參數的屬性`WebhookData`。 Runbook 可以通過在`RequestHeader`和`RequestBody`屬性中查找特定資訊來執行進一步驗證。

另一種策略是讓 Runbook 在收到 Webhook 請求時對外部條件執行某些驗證。 例如，在 GitHub 向 GitHub 存儲庫提交新時，請考慮 GitHub 調用的運行手冊。 Runbook 可能會連接到 GitHub 以驗證在繼續之前是否發生了新的提交。

## <a name="creating-a-webhook"></a>建立 Webhook

使用下列程序在 Azure 入口網站中建立連結至 Runbook 的全新 Webhook。

1. 在 Azure 門戶中的 Runbook 頁中，按一下 Webhook 開始查看 Runbook 詳細資訊的 Runbook。 確保 Runbook**狀態**欄位設置為 **"已發佈**"。
2. 按一下頁面頂端的 [Webhook]**** 以開啟 [新增 Webhook] 頁面。
3. 按一下 **"創建新網頁鉤子**"以打開"創建網頁"
4. 填寫 Webhook**的名稱**和**到期日期**欄位，並指定是否應啟用該欄位。 有關這些屬性的詳細資訊，請參閱[Webhook 屬性](#webhook-properties)。
5. 按一下複製圖示，然後按 Ctrl + C 以複製 Webhook 的 URL。 然後將其記錄在安全的地方。 

    > [!NOTE]
    > 一旦您建立 Webhook，即無法再次擷取 URL。

   ![Webhook URL](media/automation-webhooks/copy-webhook-url.png)

1. 按一下 [參數] **** 來提供 Runbook 的參數值。 如果 Runbook 具有必需的參數，則除非提供值，否則無法創建 Webhook。
1. 按一下 [ **建立** ] 來建立 Webhook。

## <a name="using-a-webhook"></a>使用 Webhook

要在 Webhook 創建後使用它，用戶端必須發出帶有 Webhook URL 的 HTTP`POST`請求。 語法為：

```http
http://<Webhook Server>/token?=<Token Value>
```

用戶端從請求接收以下返回代碼之一`POST`。

| 程式碼 | Text | 描述 |
|:--- |:--- |:--- |
| 202 |已接受 |已接受要求，且 Runbook 已經成功排入佇列。 |
| 400 |不正確的要求 |基於下列其中一個因素而不接受此要求： <ul> <li>Webhook 已過期。</li> <li>Webhook 已停用。</li> <li>URL 中的權杖無效。</li>  </ul> |
| 404 |找不到 |基於下列其中一個因素而不接受此要求： <ul> <li>找不到該 Webhook。</li> <li>找不到該 Runbook。</li> <li>找不到帳戶。</li>  </ul> |
| 500 |內部伺服器錯誤 |URL 有效，但發生錯誤。 請重新提交要求。 |

假設請求成功，Webhook 回應將包含 JSON 格式的作業 ID，如下所示。 它包含單個作業 ID，但 JSON 格式允許將來進行可能的增強。

```json
{"JobIds":["<JobId>"]}
```

用戶端無法從 Webhook 判斷 Runbook 的工作何時完成或完成狀態。 它可以使用作業 ID 與其他機制（如[Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob)或[Azure 自動化 API）](/rest/api/automation/job)一起查找此資訊。

## <a name="renewing-a-webhook"></a><a name="renew-webhook"></a>續訂網路鉤子

創建 Webhook 時，其有效期為 10 年，之後它會自動過期。 網鉤過期後，無法重新啟動它。 您只能刪除並重新創建它。 

您可以擴展尚未達到過期時間的 Webhook。 要擴展網路鉤子：

1. 導航到包含 Webhook 的 Runbook。 
2. 在 [資源]**** 下方選取 [Webhook]****。 
3. 按一下要擴展的 Webhook。 
4. 在 Webhook 頁中，選擇新的到期日期和時間，然後按一下"**保存**"。

## <a name="sample-runbook"></a>範例 Runbook

下列範例 Runbook 會接受 Webhook 資料，並啟動要求本文中指定的虛擬機器。 要測試此 Runbook，請在**Runbook**下的自動化帳戶中按一下"**創建運行簿**"。 如果您不知道如何建立 Runbook，請參閱[建立 Runbook](automation-quickstart-create-runbook.md)。

> [!NOTE]
> 對於非圖形 PowerShell 運行簿`Add-AzAccount`，`Add-AzureRMAccount`並且是[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0)的別名。 您可以使用這些 Cmdlet，也可以將自動化帳戶中的[模組更新](automation-update-azure-modules.md)到最新版本。 即使您剛剛創建了新的自動化帳戶，您也可能需要更新模組。

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData
)

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

    # Check header for message to validate request
    if ($WebhookData.RequestHeader.message -eq 'StartedbyContoso')
    {
        Write-Output "Header has required information"}
    else
    {
        Write-Output "Header missing required information";
        exit;
    }

    # Retrieve VMs from Webhook request body
    $vms = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.

    Write-Output "Authenticating to Azure with service principal and certificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    Write-Output "Get connection asset: $ConnectionAssetName"

    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Output "Authenticating to Azure with service principal."
            Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="testing-the-sample"></a>測試範例

下列範例使用 Windows PowerShell 搭配 Webhook 來啟動 Runbook。 任何可以發出 HTTP 要求的語言都可以使用 Webhook。 此處使用 Windows PowerShell 作為示例。

Runbook 預期在要求的主體中有 JSON 格式的虛擬機器清單。 Runbook 還驗證標頭是否包含已定義的消息，以驗證 Webhook 調用方是否有效。

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms
$header = @{ message="StartedbyContoso"}
$response = Invoke-WebRequest -Method Post -Uri $uri -Body $body -Headers $header
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

下面的示例顯示 對`RequestBody`屬性 中的 Runbook 可用的請求正文。 `WebhookData` 此值在 JSON 中格式化，以便與請求正文中包含的格式相容。

```json
[
    {
        "Name":  "vm01",
        "ResourceGroup":  "myResourceGroup"
    },
    {
        "Name":  "vm02",
        "ResourceGroup":  "myResourceGroup"
    }
]
```

下圖顯示由 Windows PowerShell 送出的要求以及產生的回應。 工作識別碼從回應中擷取，再轉換為字串。

![Webhook 按鈕](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>後續步驟

* 要瞭解如何使用 Azure 自動化對 Azure 警報執行操作，請參閱[使用警報觸發 Azure 自動化運行簿](automation-create-alert-triggered-runbook.md)。
