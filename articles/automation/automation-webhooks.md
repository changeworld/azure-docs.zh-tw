---
title: 從 Webhook 啟動 Azure 自動化 Runbook
description: 此文章說明如何使用 Webhook，透過 HTTP 呼叫在 Azure 自動化中啟動 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 06/24/2020
ms.topic: conceptual
ms.openlocfilehash: 4338bc4a11b785b27f6316748f9cbc4eeaaddbea
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87015097"
---
# <a name="start-a-runbook-from-a-webhook"></a>從 Webhook 啟動 Runbook

Webhook 可讓外部服務在 Azure 自動化中，透過單一 HTTP 要求啟動特定的 Runbook。 外部服務包括 Azure DevOps Services、GitHub、Azure 監視器記錄，以及自訂應用程式。 此類服務可以使用 Webhook 來啟動 Runbook，而不需要實作完整的 Azure 自動化 API。 您可以透過[在 Azure 自動化中啟動 Runbook](./start-runbooks.md)，比較 Webhook 與其他啟動 Runbook 的方法。

> [!NOTE]
> 不支援使用 Webhook 來啟動 Python Runbook。

![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

若要瞭解 TLS 1.2 與 webhook 的用戶端需求，請參閱[Azure 自動化的 tls 1.2 強制執行](automation-managing-data.md#tls-12-enforcement-for-azure-automation)。

## <a name="webhook-properties"></a>Webhook 屬性

下表描述您必須為 Webhook 設定的屬性。

| 屬性 | 說明 |
|:--- |:--- |
| 名稱 |Webhook 的名稱。 您可以提供任何想要的名稱，因為這並不會向用戶端公開。 該名稱僅供您用來識別 Azure 自動化中的 Runbook。 最佳做法是您給予 Webhook 的名稱應該與要使用它的用戶端相關。 |
| URL |Webhook 的 URL。 這是唯一性的位址，即用戶端用來呼叫 HTTP POST 以啟動連結至 Webhook 的 Runbook。 當您建立 Webhook 時其會自動產生。 您無法指定自訂 URL。 <br> <br> URL 包含可讓協力廠商系統不需進一步驗證即可叫用 Runbook 的安全性權杖。 基於此原因，您應該將 URL 視為密碼。 基於安全性原因，您僅能於 Webhook 建立時在 Azure 入口網站中檢視 URL。 請在安全的位置記下 URL 以供日後使用。 |
| 到期日期 | Webhook 的到期日，在此之後就無法再使用。 您可以在 Webhook 建立後修改到期日，只要 Webhook 尚未過期即可。 |
| 啟用 | 此設定表示在建立時預設是否啟用 Webhook。 如果您將此屬性設定為 [停用]，則不會有任何用戶端可以使用 Webhook。 您可以在建立 Webhook 時，或在其建立後的任何其他時間，設定此屬性。 |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Webhook 啟動 Runbook 時使用的參數

Webhook 可以定義 Runbook 啟動時所使用的 Runbook 參數值。 Webhook 必須包含任何強制 Runbook 參數的值，且可能包含選擇性的參數值。 您可以修改設定至 Webhoook 的參數值，甚至是在建立 Webhook 之後也可以進行修改。 多個 Webhook 連結至單一 Runbook 時，可以個別使用不同的 Runbook 參數值。 當用戶端使用 Webhook 啟動 Runbook 時，其無法覆寫 Webhook 中定義的參數值。

若要從用戶端接收資料，Runbook 支援稱為 `WebhookData` 的單一參數。 此參數會定義物件，其中包含用戶端在 POST 要求中所包含的資料。

![WebhookData 屬性](media/automation-webhooks/webhook-data-properties.png)

`WebhookData` 參數具有下列屬性：

| 屬性 | 說明 |
|:--- |:--- |
| `WebhookName` | Webhook 的名稱。 |
| `RequestHeader` | 包含傳入 POST 要求標頭的雜湊表。 |
| `RequestBody` | 傳入 POST 要求的本文。 此本文會保留任何資料格式設定 (例如字串、JSON、XML 或表單編碼)。 Runbook 必須撰寫用來搭配預期的資料格式。 |

支援 `WebhookData` 參數不需要 Webhook 的設定，且不需要 Runbook 就可以加以接受。 若 Runbook 並未定義參數，則會忽略從用戶端所傳送要求的任何詳細資料。

> [!NOTE]
> 呼叫 Webhook 時，用戶端應該一律儲存任何參數值，以防呼叫失敗。 如果發生網路中斷或連線問題，應用程式將無法擷取失敗的 Webhook 呼叫。

如果您在建立 Webhook 時指定 `WebhookData` 的值，當 Webhook 使用來自用戶端 POST 要求的資料來啟動 Runbook 時，就會加以覆寫。 即使應用程式未在要求本文中包含任何資料，也會發生這種情況。 

如果您使用 Webhook 以外的機制來啟動定義 `WebhookData` 的 Runbook，您可以針對 Runbook 可辨識的 `WebhookData` 提供值。 此值應該是與 `WebhookData` 參數具有相同[屬性](#webhook-properties)的物件，如此一來 Runbook 即可正確地與其一起運作，就像是其使用 Webhook 所傳遞的實際 `WebhookData` 物件一般。

例如，如果要從 Azure 入口網站啟動下列 Runbook，並想要傳遞一些範例 Webhook 資料進行測試，您必須在使用者介面中以 JSON 傳遞該資料。

![來自 UI 的 WebhookData 參數](media/automation-webhooks/WebhookData-parameter-from-UI.png)

針對下一個 Runbook 範例，讓我們定義 `WebhookData` 的下列屬性：

* **WebhookName**：MyWebhook
* **RequestBody**：`*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

現在，我們會在 UI 中傳遞 `WebhookData` 參數的下列 JSON 物件。 此範例使用歸位字元與新行字元，符合從 Webhook 傳入的格式。

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![啟動來自 UI 的 WebhookData 參數](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Azure 自動化會使用 Runbook 作業記錄所有輸入參數的值。 因此，任何由用戶端在 Webhook 要求中提供的輸入會加以記錄，並可讓任何有自動化作業存取權的人使用。 基於這個原因，您在 Webhook 呼叫中包含機密資訊時應該特別謹慎。

## <a name="webhook-security"></a>Webhook 安全性

Webhook 的安全性仰賴其 URL 的隱私權，其中包含允許叫用 Webhook 的安全性權杖。 只要針對正確的 URL 進行要求，Azure 自動化就不會對要求執行任何驗證。 基於此原因，若不使用驗證要求的替代方式時，您的用戶端不應使用執行高度敏感性作業的 Webhook。

請考慮下列策略：

* 您可以在 Runbook 中包含邏輯，以判斷 Runbook 是否由 Webhook 呼叫。 讓 Runbook 檢查 `WebhookData` 參數的 `WebhookName` 屬性。 Runbook 可以透過尋找 `RequestHeader` 或 `RequestBody` 屬性中的特定資訊來執行進一步的驗證。

* 讓 runbook 在收到 webhook 要求時，執行部分外部條件的驗證。 例如，請考慮每當 GitHub 存放庫出現新的認可時，就會由 GitHub 呼叫的 Runbook。 該 Runbook 在繼續執行之前，可能會先連線到 GitHub，以驗證新的認可已經出現。

* Azure 自動化支援 Azure 虛擬網路服務標籤，特別是[GuestAndHybridManagement](../virtual-network/service-tags-overview.md)。 您可以使用服務標籤來定義[網路安全性群組](../virtual-network/security-overview.md#security-rules)或[Azure 防火牆](../firewall/service-tags.md)上的網路存取控制，以及從您的虛擬網路內觸發 webhook。 當您建立安全性規則時，可以使用服務標記來取代特定的 IP 位址。 藉由在規則的適當 [來源] 或 [目的地] 欄位中指定服務標記名稱**GuestAndHybridManagement** ，您可以允許或拒絕自動化服務的流量。 此服務標記不支援將 IP 範圍限制在特定區域，以允許更細微的控制。

## <a name="create-a-webhook"></a>建立 Webhook

使用下列程序在 Azure 入口網站中建立連結至 Runbook 的全新 Webhook。

1. 從 Azure 入口網站的 [Runbook] 頁面中，按一下 Webhook 要開始檢視 Runbook 詳細資料的 Runbook。 確定 Runbook [狀態] 欄位設定為 [已發佈]。
2. 按一下頁面頂端的 [Webhook] 以開啟 [新增 Webhook] 頁面。
3. 按一下 [建立新的 Webhook] 以開啟 [建立 Webhook] 頁面。
4. 填入 Webhook 的 [名稱] 和 [到期日] 欄位，並指定是否應該加以啟用。 如需這些屬性的詳細資訊，請參閱 [Webhook 屬性](#webhook-properties)。
5. 按一下複製圖示，然後按 Ctrl + C 以複製 Webhook 的 URL。 然後將其記錄在安全的地方。 

    > [!NOTE]
    > 一旦您建立 Webhook，即無法再次擷取 URL。

   ![Webhook URL](media/automation-webhooks/copy-webhook-url.png)

1. 按一下 [參數]  來提供 Runbook 的參數值。 如果 Runbook 有強制參數，除非您提供值，否則無法建立 Webhook。

2. 按一下 [ **建立** ] 來建立 Webhook。

## <a name="use-a-webhook"></a>使用 Webhook

建立 Webhook 之後若要加以使用，您的用戶端應用程式必須使用該 Webhook 的 URL 來發出 HTTP `POST` 要求。 語法為：

```http
http://<Webhook Server>/token?=<Token Value>
```

用戶端會從 `POST` 要求中接收下列其中一個傳回碼。

| 程式碼 | Text | 描述 |
|:--- |:--- |:--- |
| 202 |已接受 |已接受要求，且 Runbook 已經成功排入佇列。 |
| 400 |不正確的要求 |基於下列其中一個因素而不接受此要求： <ul> <li>Webhook 已過期。</li> <li>Webhook 已停用。</li> <li>URL 中的權杖無效。</li>  </ul> |
| 404 |找不到 |基於下列其中一個因素而不接受此要求： <ul> <li>找不到該 Webhook。</li> <li>找不到該 Runbook。</li> <li>找不到帳戶。</li>  </ul> |
| 500 |內部伺服器錯誤 |URL 有效，但發生錯誤。 請重新提交要求。 |

假設要求成功，Webhook 會回應包含 JSON 格式的作業識別碼，如下所示。 其會包含單一作業識別碼，但是 JSON 格式允許未來可能的增強功能。

```json
{"JobIds":["<JobId>"]}
```

用戶端無法從 Webhook 判斷 Runbook 的工作何時完成或完成狀態。 其可使用作業識別碼搭配其他機制 (例如 [Windows PowerShell](/powershell/module/servicemanagement/azure.service/get-azureautomationjob) \(英文\) 或 [Azure 自動化 API](/rest/api/automation/job)) 來找出此資訊。

## <a name="renew-a-webhook"></a>更新 Webhook

建立 Webhook 時，其有效時間週期為 10 年，之後就會自動到期。 一旦 Webhook 過期，您就無法加以重新啟用。 您只能加以移除後再重新建立。 

您可以延長尚未達到其到期時間的 Webhook。 若要延長 Webhook：

1. 請瀏覽至包含 Webhook 的 Runbook。 
2. 在 [資源] 下方選取 [Webhook]。 
3. 按一下您要延長的 Webhook。 
4. 在 [Webhook] 頁面中，選擇新的到期日期和時間，然後按一下 [儲存]。

## <a name="sample-runbook"></a>範例 Runbook

下列範例 Runbook 會接受 Webhook 資料，並啟動要求本文中指定的虛擬機器。 若要測試此 Runbook，請在您自動化帳戶的 [Runbook] 下，按一下 [建立 Runbook]。 如果您不知道如何建立 Runbook，請參閱[建立 Runbook](automation-quickstart-create-runbook.md)。

> [!NOTE]
> 針對非圖形化 PowerShell Runbook，`Add-AzAccount` 和 `Add-AzureRMAccount` 是 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) \(英文\) 的別名。 您可使用這些 Cmdlet，也可以在您的自動化帳戶中[將您的模組更新](automation-update-azure-modules.md)為最新版本。 即使您才剛建立新的自動化帳戶，可能還是需要更新您的模組。

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

## <a name="test-the-sample"></a>測試範例

下列範例使用 Windows PowerShell 搭配 Webhook 來啟動 Runbook。 任何可提出 HTTP 要求的語言都可以使用 Webhook。 這裡使用 Windows PowerShell 作為範例。

Runbook 預期在要求的主體中有 JSON 格式的虛擬機器清單。 Runbook 也會驗證標頭中包含定義的訊息可驗證 Webhook 呼叫者的有效性。

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

下列範例顯示的要求本文可在 `WebhookData` 的 `RequestBody` 屬性中提供給 Runbook 使用。 此值會以 JSON 格式格式化，以與要求本文中包含的格式相容。

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

* 若要從警示觸發 Runbook，請參閱[使用警示來觸發 Azure 自動化 Runbook](automation-create-alert-triggered-runbook.md)。
