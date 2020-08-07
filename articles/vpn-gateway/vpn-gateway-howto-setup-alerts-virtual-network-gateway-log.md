---
title: Azure VPN 閘道：設定診斷資源記錄檔事件的警示
description: 瞭解如何使用 Azure 監視器 Log Analytics 和 Azure 中可用的資源記錄，根據 Azure VPN 閘道的資源記錄事件設定警示。
services: vpn-gateway
author: kumudD
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: d79bdc669ab2c3785b1cde569cfce3021e4d924f
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87923181"
---
# <a name="set-up-alerts-on-resource-log-events-from-vpn-gateway"></a>從 VPN 閘道設定資源記錄檔事件的警示

本文可協助您使用 Azure 監視器 Log Analytics，根據 Azure VPN 閘道中的資源記錄事件來設定警示。 

以下是 Azure 中可用的資源記錄：

|***名稱*** | ***說明*** |
|---        | ---               |
|GatewayDiagnosticLog | 包含閘道設定事件、主要變更和維護事件的資源記錄 |
|TunnelDiagnosticLog | 包含通道狀態變更事件。 通道連接/中斷線上活動具有狀態變更的摘要原因（如果適用） |
|RouteDiagnosticLog | 記錄在閘道上發生的靜態路由和 BGP 事件的變更 |
|IKEDiagnosticLog | 記錄閘道上的 IKE 控制訊息和事件 |
|P2SDiagnosticLog | 記錄閘道上的點對站控制訊息和事件。 僅提供 IKEv2 連接的連線來源資訊 |

## <a name="set-up-alerts-in-the-azure-portal"></a><a name="setup"></a>在 Azure 入口網站中設定警示

下列範例步驟會針對包含站對站 VPN 通道的中斷線上活動建立警示：


1. 在 Azure 入口網站中，搜尋 [**所有服務**] 底下的 [ **log analytics** ]，然後選取 [ **log analytics 工作區**]。

   ![前往 Log Analytics 工作區的選取專案](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "建立")

2. 在 [ **Log Analytics** ] 頁面上選取 [**建立**]。

   ![具有 [建立] 按鈕的 Log Analytics 頁面](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "選取")

3. 選取 **[新建]** 並填入詳細資料。

   ![建立 Log Analytics 工作區的詳細資料](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "選取")

4. 在 [**監視**  >  **診斷設定**] 分頁上尋找您的 VPN 閘道。

   ![在診斷設定中尋找 VPN 閘道的選項](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "選取")

5. 若要開啟診斷，請按兩下閘道，然後選取 [**開啟診斷**]。

   ![開啟診斷的選取專案](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "選取")

6. 填入詳細資料，並確定已選取 [**傳送至 Log Analytics** ] 和 [ **TunnelDiagnosticLog** ]。 選擇您在步驟3中建立的 Log Analytics 工作區。

   ![選取的核取方塊](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "選取")

   > [!NOTE]
   > 一開始可能需要幾個小時的時間才會顯示資料。

7. 請移至虛擬網路閘道資源的總覽，然後從 [**監視**] 索引標籤中選取 [**警示**]。然後建立新的警示規則，或編輯現有的警示規則。

   ![建立新警示規則的選取專案](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "選取")

   ![點對站](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "選取")
8. 選取 Log Analytics 工作區和資源。

   ![工作區和資源的選取專案](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "選取")

9. 選取 [**自訂記錄搜尋**] 做為 [**新增條件**] 底下的 [信號邏輯]。

   ![自訂記錄搜尋的選取專案](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "選取")

10. 在 [**搜尋查詢**] 文字方塊中輸入下列查詢。 視需要取代 <> 和 TimeGenerated 中的值。

    ```
    AzureDiagnostics
    | where Category == "TunnelDiagnosticLog"
    | where _ResourceId == tolower("<RESOURCEID OF GATEWAY>")
    | where TimeGenerated > ago(5m) 
    | where remoteIP_s == "<REMOTE IP OF TUNNEL>"
    | where status_s == "Disconnected"
    | project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId 
    | sort by TimeGenerated asc
    ```

    將 [臨界值] 設定為0，然後選取 [**完成**]。

    ![輸入查詢並選取臨界值](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "選取")

11. 在 [**建立規則**] 頁面上，選取 [**動作群組**] 區段底下的 [**建立新**的]。 填入詳細資料，然後選取 **[確定]**。

    ![新動作群組的詳細資料](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "選取")

12. 在 [**建立規則**] 頁面上，填入**自訂動作**的詳細資料，並確定 [**動作組名**] 區段中出現正確的名稱。 選取 [**建立警示規則**] 以建立規則。

    ![建立規則的選取專案](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "選取")

## <a name="set-up-alerts-by-using-powershell"></a><a name="setuppowershell"></a>使用 PowerShell 設定警示

下列範例步驟會針對包含站對站 VPN 通道的中斷線上活動建立警示。

1. 建立 Log Analytics 工作區：

   ```powershell
   $Location           = 'westus2'
   $ResourceGroupName  = 'TestRG1'
   $Sku                = 'pergb2018'
   $WorkspaceName      = 'LogAnalyticsWS123'

   New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku $Sku -ResourceGroupName $ResourceGroupName
   ```

2. 開啟 VPN 閘道的診斷：

   ```powershell
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   Set-AzDiagnosticSetting `
       -Name 'VPN tunnel' `
       -ResourceId $VpnGateway.Id `
       -WorkspaceId $Workspace.ResourceId `
       -Enabled $true `
       -Category 'TunnelDiagnosticLog'
   ```

3. 建立動作群組。

   此程式碼會建立動作群組，以便在觸發警示時傳送電子郵件通知：

   ```powershell
   $ActionGroupName            = 'EmailAdmins'   # Max. 60 characters long
   $ActionGroupShortName       = 'EmailAdmins'   # Max. 12 characters long
   $ActionGroupReceiverName    = 'My receiver Name'
   $EmailAddress               = 'xyz@contoso.com'
   $ResourceGroupName          = 'TestRG1'

   $ActionGroupReceiver = New-AzActionGroupReceiver -Name $ActionGroupReceiverName -UseCommonAlertSchema -EmailReceiver -EmailAddress $EmailAddress

   Set-AzActionGroup `
      -ResourceGroupName $ResourceGroupName `
      -Name $ActionGroupName `
      -ShortName $ActionGroupShortName `
      -Receiver @($ActionGroupReceiver)
   ```

4. 根據自訂記錄檔搜尋建立警示規則：

   ```powershell
   $ActionGroupName    = 'EmailAdmins'
   $EmailSubject       = 'Redmond VPN tunnel is disconnected'
   $Location           = 'westus2'
   $RemoteIp           = '104.42.209.46'
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   $Query = @"
   AzureDiagnostics |
   where Category == "TunnelDiagnosticLog" |
   where TimeGenerated > ago(5m) |
   where _ResourceId == tolower("$($VpnGateway.id)") |
   where remoteIP_s == "$($RemoteIp)" |
   where status_s == "Disconnected" |
   project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId |
   sort by TimeGenerated asc
   "@

   $Source             = New-AzScheduledQueryRuleSource -Query $Query -DataSourceId $Workspace.ResourceId
   $Schedule           = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 5 -TimeWindowInMinutes 5
   $TriggerCondition   = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator 'GreaterThan' -Threshold 0

   $ActionGroup        = Get-AzActionGroup -ResourceGroupName $ResourceGroupName -Name $ActionGroupName
   $AznsActionGroup    = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup $ActionGroup.Id -EmailSubject $EmailSubject
   $AlertingAction     = New-AzScheduledQueryRuleAlertingAction -AznsAction $AznsActionGroup -Severity '1' -Trigger $TriggerCondition

   New-AzScheduledQueryRule `
       -ResourceGroupName $ResourceGroupName `
       -Location $Location `
       -Action $AlertingAction `
       -Enabled $true `
       -Description 'The tunnel between Azure and Redmond with IP address 104.42.209.46 is disconnected' `
       -Schedule $Schedule `
       -Source $Source `
       -Name 'The Azure to Redmond tunnel is disconnected'
   ```

## <a name="next-steps"></a>後續步驟

若要設定通道計量的警示，請參閱[設定 VPN 閘道計量的警示](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)。
