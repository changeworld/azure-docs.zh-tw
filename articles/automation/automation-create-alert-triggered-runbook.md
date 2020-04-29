---
title: 使用警示來觸發 Azure 自動化 Runbook
description: 了解如何在引發 Azure 警示時觸發 Runbook 執行。
services: automation
ms.subservice: process-automation
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: e8ddcaf6a5c9ab51147e540e2426ef8c4a1fdd3a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81392379"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>使用警示來觸發 Azure 自動化 Runbook

您可以使用 [Azure 監視器](../azure-monitor/overview.md?toc=%2fazure%2fautomation%2ftoc.json)來監視 Azure 中大多數服務的基準層級計量和記錄。 您可以使用[動作群組](../azure-monitor/platform/action-groups.md?toc=%2fazure%2fautomation%2ftoc.json)或使用傳統警示來呼叫 Azure 自動化 Runbook，以根據警示來自動執行工作。 本文示範如何使用警示來設定及執行 Runbook。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需有關混合式 Runbook 背景工作角色的 Az 模組安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="alert-types"></a>警示類型

您可以將自動化 Runbook 與三種警示類型搭配使用：

* 常見警示
* 活動記錄警示
* 近乎即時的計量警示

> [!NOTE]
> 一般警示架構會將 Azure 中警示通知的耗用量體驗標準化。 在過去，Azure 中的三個警示類型（計量、記錄和活動記錄）都有自己的電子郵件範本、webhook 架構等。若要深入瞭解，請參閱[一般警示架構](../azure-monitor/platform/alerts-common-schema.md)

當警示呼叫 Runbook 時，實際的呼叫是對 Webhook 的 HTTP POST 要求。 POST 要求的本文包含 JSON 格式的物件，其中具有與警示相關的實用屬性。 下表列出每種警示類型的承載結構描述連結：

|警示  |描述|承載結構描述  |
|---------|---------|---------|
|[一般警示](../azure-monitor/platform/alerts-common-schema.md?toc=%2fazure%2fautomation%2ftoc.json)|一般警示架構，可將 Azure 中警示通知的耗用量體驗標準化。|一般警示承載架構|
|[活動記錄警示](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |當 Azure 活動記錄中的任何新事件符合特定條件時，便傳送通知。 例如，當 **myProductionResourceGroup** 中發生 `Delete VM` 作業時，或出現狀態為 [作用中] 的新「Azure 服務健康狀態」事件時。| [活動記錄警示承載結構描述](../azure-monitor/platform/activity-log-alerts-webhook.md)        |
|[近乎即時計量警示](../azure-monitor/platform/alerts-metric-near-real-time.md?toc=%2fazure%2fautomation%2ftoc.json)    |當一或多個平台層級的計量符合指定的條件時，便以比計量警示快的速度傳送通知。 例如，當 VM 上的**CPU%** 值大於90時，在過去5分鐘**內的 Network**值大於 500 MB。| [近乎即時計量警示承載結構描述](../azure-monitor/platform/alerts-webhooks.md#payload-schema)          |

由於每種類型的警示所提供的資料不同，因此每種警示類型的處理方式也不同。 在下一節中，您將了解如何建立 Runbook 來處理不同類型的警示。

## <a name="create-a-runbook-to-handle-alerts"></a>建立 Runbook 來處理警示

若要搭配警示使用「自動化」，您需要具有邏輯的 Runbook，此邏輯會管理傳遞給 Runbook 的警示 JSON 承載。 下列範例 Runbook 必須從 Azure 警示呼叫。

如上一節所述，每種類型的警示都有不同的結構描述。 腳本會從`WebhookData` runbook 輸入參數中的警示取得 webhook 資料。 然後，腳本會評估 JSON 承載，以判斷所使用的是哪一種警示類型。

此範例使用來自 VM 的警示。 它會從承載擷取 VM 資料，然後使用該資訊來停止 VM。 您必須在執行 Runbook 的「自動化」帳戶中設定連線。 使用警示來觸發 runbook 時，請務必檢查觸發的 runbook 中的警示狀態。 Runbook 會在每次警示變更狀態時觸發。 警示有多個狀態，其中兩個最常見的是已啟用和已解決。 檢查 runbook 邏輯中的狀態，以確保 runbook 不會執行一次以上。 本文中的範例顯示如何尋找僅啟用狀態的警示。

Runbook 會使用連線資產`AzureRunAsConnection`執行身分[帳戶](automation-create-runas-account.md)向 Azure 進行驗證，以對 VM 執行管理動作。

請使用此範例來建立名為 **Stop-AzureVmInResponsetoVMAlert** 的 Runbook。 您可以修改 PowerShell 指令碼，然後將它與許多不同的資源搭配使用。

1. 移至您的「Azure 自動化」帳戶。
2. 在 [程序自動化]  下方，選取 [Runbook]  。
3. 在 Runbook 清單頂端，選取 [+ 建立 Runbook]  。
4. 在 [**新增 runbook** ] 頁面上，為 Runbook 名稱輸入**stop-azurevminresponsetovmalert** 。 針對 Runbook 類型，選取 [PowerShell]  。 然後，選取 [Create]  \(建立\)。  
5. 將下列 PowerShell 範例複製到 [編輯]  頁面中。

    ```powershell-interactive
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )
    $ErrorActionPreference = "stop"

    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        # Get the info needed to identify the VM (depends on the payload schema)
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "azureMonitorCommonAlertSchema") {
            # This is the common Metric Alert schema (released March 2019)
            $Essentials = [object] ($WebhookBody.data).essentials
            # Get the first target only as this script doesn't handle multiple
            $alertTargetIdArray = (($Essentials.alertTargetIds)[0]).Split("/")
            $SubId = ($alertTargetIdArray)[2]
            $ResourceGroupName = ($alertTargetIdArray)[4]
            $ResourceType = ($alertTargetIdArray)[6] + "/" + ($alertTargetIdArray)[7]
            $ResourceName = ($alertTargetIdArray)[-1]
            $status = $Essentials.monitorCondition
        }
        elseif ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # Schema not supported
            Write-Error "The alert data schema - $schemaId - is not supported."
        }

        Write-Verbose "status: $status" -Verbose
        if (($status -eq "Activated") -or ($status -eq "Fired"))
        {
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose

            # Determine code path depending on the resourceType
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is an Resource Manager VM
                Write-Verbose "This is an Resource Manager VM." -Verbose

                # Authenticate to Azure with service principal and certificate and set subscription
                Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
                $ConnectionAssetName = "AzureRunAsConnection"
                Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
                $Conn = Get-AutomationConnection -Name $ConnectionAssetName
                if ($Conn -eq $null)
                {
                    throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
                }
                Write-Verbose "Authenticating to Azure with service principal." -Verbose
                Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the Resource Manager VM
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType not supported
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated' or 'Fired' so no action taken
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```

6. 選取 [發佈]  來儲存並發佈 Runbook。

## <a name="create-the-alert"></a>建立警示

警示會使用動作群組，這是警示所觸發的動作集合。 Runbook 只是可與動作群組搭配使用的眾多動作之一。

1. 在您的自動化帳戶中，選取 [**監視**] 下的 [**警示**]。
1. 選取 [+ 新增警示規則]****。
1. 按一下 [**資源**] 底下的 [**選取**]。 在 [**選取資源**] 頁面上，選取您的 VM 以發出警示，然後按一下 [**完成**]。
1. 按一下 [**條件**] 底下的 [**新增條件**]。 選取您想要使用的信號，例如 [ **CPU 百分比**]，然後按一下 [**完成**]。
1. 在 [**設定信號邏輯**] 頁面上，于 [**警示邏輯**] 下輸入**閾值**，然後按一下 [**完成**]。
1. 在 [動作群組]  底下，選取 [新建]  。
1. 在 [**新增動作群組**] 頁面上，為您的動作群組提供名稱和簡短名稱。
1. 指定動作的名稱。 針對 [動作類型]，選取 [**自動化 Runbook**]。
1. 選取 [**編輯詳細資料**]。 在 [設定 Runbook]**** 頁面上的 [Runbook 來源]**** 底下，選取 [使用者]****。  
1. 選取您的 [訂用帳戶]**** 和 [自動化帳戶]****，然後選取 [Stop-AzureVmInResponsetoVMAlert]**** Runbook。  
1. 針對 **[啟用一般警示架構** **] 選取 [是]** 。
1. 若要建立動作群組，請選取 [確定]****。

    ![新增動作群組分頁](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    您可以在所建立的[活動記錄警示](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)和[近乎即時警示](../azure-monitor/platform/alerts-overview.md?toc=%2fazure%2fautomation%2ftoc.json)中使用此動作群組。

1. 在 [**警示詳細資料**] 底下，新增警示規則名稱和描述，然後按一下 [**建立警示規則**]。

## <a name="next-steps"></a>後續步驟

* 如需有關使用 Webhook 來啟動「自動化」Runbook 的詳細資訊，請參閱[從 Webhook 啟動 Runbook](automation-webhooks.md)。
* 如需以不同方式啟動 Runbook 的詳細資訊，請參閱 [啟動 Runbook](automation-starting-a-runbook.md)。
* 若要了解如何建立活動記錄警示，請參閱[建立活動記錄警示](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)。
* 若要了解如何建立近乎即時計量警示，請參閱[在 Azure 入口網站中建立警示規則](../azure-monitor/platform/alerts-metric.md?toc=/azure/azure-monitor/toc.json)。
* 如需 PowerShell Cmdlet 參考，請參閱 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
