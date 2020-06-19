---
title: Azure 服務匯流排 - 自動更新傳訊單位
description: 本文說明如何使用 Azure 自動化 Runbook 來自動更新服務匯流排命名空間的傳訊單位。
services: service-bus-messaging
ms.service: service-bus-messaging
documentationcenter: ''
author: spelluru
ms.topic: how-to
ms.date: 05/14/2020
ms.author: spelluru
ms.openlocfilehash: 5ece7beaea709c9b1e52cf2130484663da0aa4ac
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83663096"
---
# <a name="automatically-update-messaging-units-of-an-azure-service-bus-namespace"></a>自動更新 Azure 服務匯流排命名空間的傳訊單位 
本文會說明如何根據資源 (CPU 或記憶體) 使用量，自動更新服務匯流排命名空間的[傳訊單位](service-bus-premium-messaging.md)。 

本文中的範例會說明當命名空間的 CPU 使用量高於 75% 時，如何增加服務匯流排命名空間的傳訊單位。 高階步驟如下：

1. 使用 PowerShell 指令碼建立 Azure 自動化 Runbook，以相應增加 (增加) 服務匯流排命名空間的傳訊單位。 
2. 建立服務匯流排命名空間的 CPU 使用量警示，以在命名空間 CPU 使用量超過 75% 時叫用 PowerShell 指令碼。 

> [!IMPORTANT]
> 本文僅適用於 Azure 服務匯流排的 [進階] 層。 


## <a name="create-a-service-bus-namespace"></a>建立服務匯流排命名空間
建立進階層服務匯流排命名空間。 遵循[在 Azure 入口網站中建立命名空間](service-bus-quickstart-portal.md#create-a-namespace-in-the-azure-portal)一文中的步驟來建立命名空間。 

## <a name="create-an-azure-automation-account"></a>建立 Azure 自動化帳戶
遵循[建立 Azure 自動化帳戶](../automation/automation-quickstart-create-account.md)一文中的指示，建立 Azure 自動化帳戶。 

## <a name="import-azservice-module-from-gallery"></a>從資源庫匯入 Az.Service 模組
將資源庫中的 `Az.Accounts` 和 `Az.ServiceBus` 模組匯入自動化帳戶中。 `Az.ServiceBus` 模組相依於 `Az.Accounts` 模組，因此必須先加以安裝。 

如需逐步指示，請參閱[從模組資源庫匯入模組](../automation/automation-runbook-gallery.md#import-a-module-from-the-module-gallery-with-the-azure-portal)。

## <a name="create-and-publish-a-powershell-runbook"></a>建立及發佈 PowerShell Runbook

1. 遵循[建立 PowerShell Runbook](../automation/automation-quickstart-create-runbook.md) 一文中的指示，建立 PowerShell Runbook。 

    以下是您可以用來增加服務匯流排命名空間之傳訊單位的 PowerShell 指令碼範例。 這個在自動化 Runbook 中的 PowerShell 指令碼會將 MU 從 1 增加到 2、2 到 4 或 4 到 8。 此屬性的允許值為︰1、2、4 和 8。 您可以建立另一個 Runbook 來減少傳訊單位。

    **namespaceName** 和 **resourceGroupName** 參數是用來與警示案例分開測試指令碼。 
    
    **WebHookData** 參數用於警示，以便在執行階段傳遞資源群組名稱、資源名稱等資訊。 

    ```powershell
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData,
    
        [Parameter (Mandatory = $false)]
        [String] $namespaceName,
    
        [Parameter (Mandatory = $false)]
        [String] $resourceGroupName
    )
    
    
    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    
        # Get the alert schema ID
        $schemaId = $WebhookBody.schemaId

        # If it's a metric alert
        if ($schemaId -eq "AzureMonitorMetricAlert") {

            # Get the resource group name from the alert context
            $resourceGroupName = $AlertContext.resourceGroupName
            
            # Get the namespace name from the alert context
            $namespaceName = $AlertContext.resourceName
        }
    }
    
    # Connect to Azure account
    $connection = Get-AutomationConnection -Name AzureRunAsConnection
    
    while(!($connectionResult) -And ($logonAttempt -le 10))
    {
        $LogonAttempt++
        # Logging in to Azure...
        $connectionResult =    Connect-AzAccount `
                                    -ServicePrincipal `
                                    -Tenant $connection.TenantID `
                                    -ApplicationId $connection.ApplicationID `
                                    -CertificateThumbprint $connection.CertificateThumbprint
    
        Start-Sleep -Seconds 30
    }
    
    # Get the current capacity (number of messaging units) of the namespace
    $sbusns=Get-AzServiceBusNamespace `
        -Name $namespaceName `
        -ResourceGroupName $resourceGroupName
    
    $currentCapacity = $sbusns.Sku.Capacity
    
    # Increase the capacity
    # Capacity can be one of these values: 1, 2, 4, 8
    if ($currentCapacity -eq 1) {
        $newMU = 2
    }
    elseif ($currentCapacity -eq 2) {
        $newMU = 4
    }
    elseif ($currentCapacity -eq 4) {
        $newMU = 8    
    }
    else {
    
    }
    
    # Update the capacity of the namespace
    Set-AzServiceBusNamespace `
            -Location eastus `
            -SkuName Premium `
            -Name $namespaceName `
            -SkuCapacity $newMU `
            -ResourceGroupName $resourceGroupName

    ```
2. 藉由指定 **namespaceName** 和 **resourceGroupName** 參數的值來[測試活頁簿](../automation/manage-runbooks.md#test-a-runbook)。 確認已更新命名空間上的傳訊單位。 
3. 測試成功之後，[發佈活頁簿](..//automation/manage-runbooks.md#publish-a-runbook)，讓其稍後在命名空間中可新增做為警示的動作。 

## <a name="create-an-alert-on-the-namespace-to-trigger-the-runbook"></a>在命名空間上建立警示以觸發 Runbook
請參閱[使用警示來觸發 Azure 自動化 Runbook](../automation/automation-create-alert-triggered-runbook.md) 一文，以在您的服務匯流排命名空間上設定警示，藉此觸發您所建立的自動化 Runbook。 例如，您可以針對 [每個命名空間的 CPU 使用量] 或 [每個命名空間的記憶體大小使用量] 計量來建立警示，並新增動作以觸發您所建立的自動化 Runbook。 如需這些計量的詳細資料，請參閱[資源使用計量](service-bus-metrics-azure-monitor.md#resource-usage-metrics)。

下列程序示範如何建立警示，以在命名空間 **CPU 使用量**高於 **75%** 時觸發自動化 Runbook。

1. 在命名空間的 [服務匯流排命名空間] 頁面上，選取左側功能表上的 [警示]，然後在工具列上選取 [+ 新增警示規則]。 
    
    ![[警示] 頁面 - [新增警示規則] 按鈕](./media/automate-update-messaging-units/alerts-page.png)
2. 在 [建立警示規則] 頁面上，按一下 [選取條件]。 

    ![[建立警示規則] 頁面 - 選取條件](./media/automate-update-messaging-units/alert-rule-select-condition.png) 
3. 在 [設定訊號邏輯] 頁面上，選取 [CPU] 以取得訊號。 

    ![設定訊號邏輯 - 選取 CPU](./media/automate-update-messaging-units/configure-signal-logic.png)
4. 輸入**臨界值** (在此範例中為 **75%** )，然後選取 [完成]。 

    ![設定 CPU 訊號](./media/automate-update-messaging-units/cpu-signal-configuration.png)
5. 現在，在 [建立警示] 頁面上，按一下 [選取動作群組]。
    
    ![選取動作群組](./media/automate-update-messaging-units/select-action-group-button.png)
6. 選取工具列上的 [建立動作群組] 按鈕。 

    ![[建立動作群組] 按鈕](./media/automate-update-messaging-units/create-action-group-button.png)
7. 在 [新增動作群組] 頁面上，執行下列步驟：
    1. 輸入動作群組的**名稱**。 
    2. 輸入動作群組的**簡短名稱**。
    3. 選取您要在其中建立此動作群組的**訂用帳戶**。
    4. 選取**資源群組**。 
    5. 在 [動作] 區段中，輸入**動作的名稱**，然後針對 [動作類型] 選取 [自動化 Runbook]。 

        ![新增動作群組分頁](./media/automate-update-messaging-units/add-action-group-page.png)
8. 在 [設定 Runbook] 頁面上，執行下列步驟：
    1. 針對 [Runbook 來源]，選取 [使用者]。 
    2. 針對 [訂用帳戶]，選取包含自動化帳戶的 Azure **訂用帳戶**。 
    3. 針對 [自動化帳戶]，選取您的**自動化帳戶**。
    4. 針對 [Runbook]，請選取您的 Runbook。 
    5. 在 [設定 Runbook] 頁面上，選取 [確定]。 
        ![設定 Runbook](./media/automate-update-messaging-units/configure-runbook.png)
9. 在 [新增動作群組] 頁面上，選取 [確定]。 
5. 現在，在 [建立警示規則] 頁面上，輸入**規則的名稱**，然後選取 [建立警示規則]。 
    ![建立警示規則](./media/automate-update-messaging-units/create-alert-rule.png)

    > [!NOTE]
    > 現在，當命名空間的 CPU 使用量超過 75 時，警示就會觸發自動化 Runbook，這會增加服務匯流排命名空間的傳訊單位。 同樣地，您可以針對另一個自動化 Runbook 建立警示，如果命名空間的 CPU 使用量低於 25，則會減少傳訊單位。 

## <a name="next-steps"></a>後續步驟
若要深入了解傳訊單位，請參閱[進階傳訊](service-bus-premium-messaging.md)