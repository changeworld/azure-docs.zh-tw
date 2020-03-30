---
title: Azure VPN 閘道：在診斷日誌事件上配置警報
description: 在 VPN 閘道診斷日誌事件上配置警報的步驟
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: b587fa31f1aa42e8dbd5a9d2430c0117891f2091
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239794"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>從 VPN 閘道設置診斷日誌事件的警報

本文可説明您使用 Azure 日誌分析根據 Azure VPN 閘道的診斷日誌事件設置警報。 

Azure 中提供了以下日誌：

|***名稱*** | ***描述*** |
|---        | ---               |
|GatewayDiagnosticLog | 包含閘道配置事件、主要更改和維護事件的診斷日誌 |
|TunnelDiagnosticLog | 包含隧道狀態變更事件。 隧道連接/斷開連接事件有狀態更改的匯總原因（如果適用） |
|RouteDiagnosticLog | 記錄閘道上發生的靜態路由和 BGP 事件的更改 |
|IKEDiagnosticLog | 在閘道上記錄 IKE 控制訊息和事件 |
|P2SDiagnosticLog | 在閘道上記錄點對點控制訊息和事件 |

## <a name="set-up-alerts-in-the-azure-portal"></a><a name="setup"></a>在 Azure 入口網站中設定警示

以下示例步驟將為涉及網站到網站 VPN 隧道的斷開連接事件創建警報：


1. 在 Azure 門戶中，在 **"所有服務**"下搜索**日誌分析**，然後選擇**日誌分析工作區**。

   ![前往日誌分析工作區的選擇](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "建立")

2. 在**日誌分析**頁面上選擇 **"創建**"。

   ![使用"創建"按鈕的日誌分析頁面](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "選取")

3. 選擇 **"新建"** 並填寫詳細資訊。

   ![創建日誌分析工作區的詳細資訊](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "選取")

4. 在 **"監視器** > **診斷設置"** 邊欄選項卡上查找 VPN 閘道。

   ![在診斷設置中查找 VPN 閘道的選擇](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "選取")

5. 要打開診斷程式，請按兩下閘道，然後選擇"**打開診斷**"。

   ![打開診斷的選擇](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "選取")

6. 填寫詳細資訊，並確保選擇 **"發送到日誌分析和****隧道診斷日誌**"。 選擇您在步驟 3 中創建的日誌分析工作區。

   ![選定的核取方塊](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "選取")

   > [!NOTE]
   > 資料可能需要幾個小時才能最初顯示。

7. 轉到虛擬網路閘道資源的概述，並從 **"監視"** 選項卡中選擇 **"警報**"。然後創建新的警報規則或編輯現有的警報規則。

   ![創建新警報規則的選擇](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "選取")

   ![點對點](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "選取")
8. 選擇日誌分析工作區和資源。

   ![工作區和資源的選擇](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "選取")

9. 選擇**自訂日誌搜索**作為 **"添加條件**"下的信號邏輯。

   ![自訂日誌搜索的選擇](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "選取")

10. 在 **"搜索"查詢**文字方塊中輸入以下查詢。 根據需要替換 <> 和時間生成中的值。

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

    將閾值設置為 0 並選擇 **"完成**"。

    ![輸入查詢並選擇閾值](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "選取")

11. 在"**創建規則**"頁上，在 **"操作組"** 部分下選擇 **"新建**"。 填寫詳細資訊並選擇 **"確定**"。

    ![新操作組的詳細資訊](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "選取")

12. 在 **"創建規則"** 頁上，填寫 **"自訂操作"** 的詳細資訊，並確保正確的名稱顯示在 **"操作組名稱"** 部分中。 選擇 **"創建警報規則**以創建規則"。

    ![創建規則的選擇](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "選取")

## <a name="set-up-alerts-by-using-powershell"></a><a name="setuppowershell"></a>使用 PowerShell 設置警報

以下示例步驟為涉及網站到網站 VPN 隧道的斷開連接事件創建警報。

1. 創建日誌分析工作區：

   ```powershell
   $Location           = 'westus2'
   $ResourceGroupName  = 'TestRG1'
   $Sku                = 'pergb2018'
   $WorkspaceName      = 'LogAnalyticsWS123'

   New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku $Sku -ResourceGroupName $ResourceGroupName
   ```

2. 打開 VPN 閘道的診斷：

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

3. 創建操作組。

   此代碼創建一個操作組，在觸發警報時發送電子郵件通知：

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

4. 基於自訂日誌搜索創建警報規則：

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

要配置隧道指標上的警報，請參閱在[VPN 閘道指標上設置警報](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)。
