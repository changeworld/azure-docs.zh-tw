---
title: 使用 Webhook 啟動 Azure 自動化 Runbook
description: 可讓用戶端透過 HTTP 呼叫在 Azure 自動化中啟動 Runbook 的 Webhook。  本文說明如何建立 Webhook，以及如何進行呼叫以啟動 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 01/16/2020
ms.topic: conceptual
ms.openlocfilehash: 8cb641f95e7327e80f42df86a56eba8c34e7e598
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367018"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>使用 Webhook 啟動 Azure 自動化 Runbook

Webhook 可讓外部服務透過單一 HTTP 要求，在 Azure 自動化中啟動特定的 runbook。 外部服務包括 Azure DevOps Services、GitHub、Azure 監視器記錄，以及自訂應用程式。 這類服務可以使用 webhook 來啟動 runbook，而不需要使用 Azure 自動化 API 來執行完整的解決方案。 您可以在[Azure 自動化中啟動 runbook](automation-starting-a-runbook.md)，將 webhook 與其他啟動 runbook 的方法進行比較。

> [!NOTE]
> 不支援使用 webhook 來啟動 Python runbook。

![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需混合式 Runbook 背景工作角色上的 Az module 安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="webhook-properties"></a>Webhook 屬性

下表描述您必須為 Webhook 設定的屬性。

| 屬性 | 描述 |
|:--- |:--- |
| 名稱 |Webhook 的名稱。 您可以提供任何想要的名稱，因為它不會公開給用戶端。 該名稱僅供您用來識別 Azure 自動化中的 Runbook。 最佳做法是您給予 Webhook 的名稱應該與要使用它的用戶端相關。 |
| URL |Webhook 的 URL。 這是用戶端使用 HTTP POST 呼叫的唯一位址，用以啟動連結至 webhook 的 runbook。 當您建立 Webhook 時其會自動產生。 您無法指定自訂 URL。 <br> <br> URL 包含安全性權杖，可讓協力廠商系統不需進一步驗證即可叫用 runbook。 基於這個理由，您應該將 URL 視為密碼。 基於安全性理由，您只能在建立 webhook 時，于 Azure 入口網站中查看 URL。 請在安全的位置記下 URL 以供日後使用。 |
| 到期日期 | Webhook 的到期日，在此之後就無法再使用。 您可以在 webhook 建立之後修改到期日，只要 webhook 尚未過期即可。 |
| 啟用 | 此設定表示在建立時預設是否啟用 webhook。 如果您將此屬性設定為 [停用]，則不會有任何用戶端可以使用 webhook。 您可以在建立 webhook 時，或在其建立後的任何其他時間，設定此屬性。 |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Webhook 啟動 runbook 時使用的參數

Webhook 可以定義 runbook 啟動時使用的 runbook 參數值。 Webhook 必須包含任何強制 runbook 參數的值，而且可以包含選擇性參數的值。 即使在 webhook 建立之後，也可以修改設定為 webhook 的參數值。 連結至單一 runbook 的多個 webhook 可以使用不同的 runbook 參數值。 當用戶端使用 Webhook 啟動 Runbook 時，其無法覆寫 Webhook 中定義的參數值。

若要從用戶端接收資料，runbook 支援稱為 `WebhookData`的單一參數。 此參數會定義物件，其中包含用戶端在 POST 要求中所包含的資料。

![WebhookData 屬性](media/automation-webhooks/webhook-data-properties.png)

`WebhookData` 參數具有下列屬性：

| 屬性 | 描述 |
|:--- |:--- |
| `WebhookName` | Webhook 的名稱。 |
| `RequestHeader` | 雜湊表，包含傳入 POST 要求的標頭。 |
| `RequestBody` | 傳入 POST 要求的主體。 此主體會保留任何資料格式，例如字串、JSON、XML 或表單編碼。 Runbook 必須撰寫用來搭配預期的資料格式。 |

不需要設定 webhook 就能支援 `WebhookData` 參數，而且 runbook 不需要接受它。 如果 runbook 並未定義參數，則會忽略從用戶端傳送之要求的任何詳細資料。

> [!NOTE]
> 呼叫 webhook 時，用戶端應該一律儲存任何參數值，以防呼叫失敗。 如果發生網路中斷或連線問題，應用程式將無法取得失敗的 webhook 呼叫。

如果您在建立 webhook 時指定 `WebhookData` 的值，當 webhook 使用來自用戶端 POST 要求的資料來啟動 runbook 時，就會覆寫它。 即使應用程式未在要求主體中包含任何資料，也會發生這種情況。 

如果您使用 webhook 以外的機制來啟動定義 `WebhookData` 的 runbook，您可以為 runbook 可辨識的 `WebhookData` 提供值。 這個值應該是具有與 `WebhookData` 參數相同[屬性](#webhook-properties)的物件，因此 runbook 可以使用它，就像它適用于由 webhook 所傳遞的實際 `WebhookData` 物件一樣。

例如，如果您從 Azure 入口網站啟動下列 runbook，並想要傳遞一些範例 webhook 資料進行測試，您必須在使用者介面中以 JSON 傳遞資料。

![來自 UI 的 WebhookData 參數](media/automation-webhooks/WebhookData-parameter-from-UI.png)

針對下一個 runbook 範例，讓我們定義 `WebhookData`的下列屬性：

* **WebhookName**： MyWebhook
* **RequestBody**： `*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

現在，我們會在 UI 中傳遞 `WebhookData` 參數的下列 JSON 物件。 這個範例使用回車和分行符號，符合從 webhook 傳入的格式。

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![啟動來自 UI 的 WebhookData 參數](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Azure 自動化會使用 runbook 作業來記錄所有輸入參數的值。 因此，會記錄用戶端在 webhook 要求中提供的任何輸入，並可供任何可存取自動化作業的人員使用。 基於這個原因，您在 Webhook 呼叫中包含機密資訊時應該特別謹慎。

## <a name="webhook-security"></a>Webhook 安全性

Webhook 的安全性依賴其 URL 的隱私權，其中包含允許叫用 webhook 的安全性權杖。 Azure 自動化不會對要求執行任何驗證，只要它是對正確的 URL。 基於這個理由，您的用戶端不應針對執行高度機密作業的 runbook 使用 webhook，而不需使用替代方法來驗證要求。

您可以在 runbook 中包含邏輯，以判斷 webhook 是否呼叫它。 讓 runbook 檢查 `WebhookData` 參數的 `WebhookName` 屬性。 Runbook 可以藉由尋找 `RequestHeader` 和 `RequestBody` 屬性中的特定資訊來執行進一步的驗證。

另一個策略是讓 runbook 在收到 webhook 要求時，執行部分外部條件的驗證。 例如，當 GitHub 存放庫有新的認可時，請考慮由 GitHub 呼叫的 runbook。 Runbook 可能會連線到 GitHub，以驗證是否已發生新的認可，然後再繼續。

## <a name="creating-a-webhook"></a>建立 Webhook

使用下列程序在 Azure 入口網站中建立連結至 Runbook 的全新 Webhook。

1. 從 Azure 入口網站的 [Runbook] 頁面中，按一下 webhook 開始查看 runbook 詳細資料的 runbook。 確定 [runbook**狀態**] 欄位設定為 [**已發佈**]。
2. 按一下頁面頂端的 [ **webhook** ] 以開啟 [新增 webhook] 頁面。
3. 按一下 [**建立新的 webhook** ] 以開啟 [建立 webhook] 頁面。
4. 填入 webhook 的 [**名稱**] 和 [**到期日**] 欄位，並指定是否應該啟用。 如需這些屬性的詳細資訊，請參閱[Webhook 屬性](#webhook-properties)。
5. 按一下複製圖示，然後按 Ctrl + C 以複製 Webhook 的 URL。 然後將其記錄在安全的地方。 

    > [!NOTE]
    > 一旦您建立 webhook 之後，就無法再重新取得 URL。

   ![Webhook URL](media/automation-webhooks/copy-webhook-url.png)

1. 按一下 [參數] 來提供 Runbook 的參數值。 如果 runbook 有強制參數，除非您提供值，否則無法建立 webhook。
1. 按一下 [ **建立** ] 來建立 Webhook。

## <a name="using-a-webhook"></a>使用 Webhook

若要在建立 webhook 之後使用它，您的用戶端必須發出 HTTP `POST` 要求，並提供 webhook 的 URL。 語法為：

```http
http://<Webhook Server>/token?=<Token Value>
```

用戶端會從 `POST` 要求接收下列其中一個傳回碼。

| 程式碼 | Text | 描述 |
|:--- |:--- |:--- |
| 202 |已接受 |已接受要求，且 Runbook 已經成功排入佇列。 |
| 400 |不正確的要求 |基於下列其中一個因素而不接受此要求： <ul> <li>Webhook 已過期。</li> <li>Webhook 已停用。</li> <li>URL 中的權杖無效。</li>  </ul> |
| 404 |找不到 |基於下列其中一個因素而不接受此要求： <ul> <li>找不到該 Webhook。</li> <li>找不到該 Runbook。</li> <li>找不到帳戶。</li>  </ul> |
| 500 |內部伺服器錯誤 |URL 有效，但發生錯誤。 請重新提交要求。 |

假設要求成功，webhook 回應會包含 JSON 格式的工作識別碼，如下所示。 它包含單一作業識別碼，但 JSON 格式允許未來可能的增強功能。

```json
{"JobIds":["<JobId>"]}
```

用戶端無法從 Webhook 判斷 Runbook 的工作何時完成或完成狀態。 它可以使用工作識別碼搭配另一個機制（例如[Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob)或[Azure 自動化 API](/rest/api/automation/job)）來找出此資訊。

## <a name="renew-webhook"></a>更新 webhook

建立 webhook 時，它的有效時間週期為10年，之後就會自動到期。 一旦 webhook 過期，您就無法重新啟用它。 您只能移除後再重新建立。 

您可以擴充尚未達到其到期時間的 webhook。 若要擴充 webhook：

1. 流覽至包含 webhook 的 runbook。 
2. 在 [資源] 下方選取 [Webhook]。 
3. 按一下您要擴充的 webhook。 
4. 在 [Webhook] 頁面中，選擇新的到期日和時間，然後按一下 [**儲存**]。

## <a name="sample-runbook"></a>範例 Runbook

下列範例 Runbook 會接受 Webhook 資料，並啟動要求本文中指定的虛擬機器。 若要測試此 runbook，請在您的自動化帳戶的 [ **runbook**] 底下，按一下 [**建立 runbook**]。 如果您不知道如何建立 Runbook，請參閱[建立 Runbook](automation-quickstart-create-runbook.md)。

> [!NOTE]
> 針對非圖形化 PowerShell runbook，`Add-AzAccount` 和 `Add-AzureRMAccount` 是[disconnect-azaccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0)的別名。 您可以使用這些 Cmdlet，也可以將自動化帳戶中的[模組更新](automation-update-azure-modules.md)為最新版本。 即使您剛建立新的自動化帳戶，也可能需要更新您的模組。

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

下列範例使用 Windows PowerShell 搭配 Webhook 來啟動 Runbook。 任何可提出 HTTP 要求的語言都可以使用 webhook。 這裡使用 Windows PowerShell 做為範例。

Runbook 預期在要求的主體中有 JSON 格式的虛擬機器清單。 Runbook 也會驗證標頭中包含已定義的訊息，以驗證 webhook 呼叫端是否有效。

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

下列範例顯示在 `WebhookData`的 `RequestBody` 屬性中，可供 runbook 使用的要求主體。 此值會以 JSON 格式格式化，以與要求主體中包含的格式相容。

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

* 若要瞭解如何使用 Azure 自動化來對 Azure 警示採取動作，請參閱[使用警示來觸發 Azure 自動化 runbook](automation-create-alert-triggered-runbook.md)。
