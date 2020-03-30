---
title: 將 Azure 自動化運行簿添加到網站恢復恢復計畫
description: 瞭解如何使用 Azure 自動化擴展恢復計畫以使用 Azure 網站恢復進行災害復原。
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: rajanaki
ms.openlocfilehash: ecfe993a137ca63c84438870ec54ac1e6d6707da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257481"
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>將 Azure 自動化 Runbook 新增至復原方案

本文介紹如何集成 Azure 自動化運行簿，以擴展[Azure 網站恢復](site-recovery-overview.md)計畫。 我們向您展示如何自動執行本來需要手動干預的基本任務，以及如何將多步驟恢復轉換為一鍵式操作。

## <a name="recovery-plans"></a>復原方案 

在容錯移轉本地電腦或 Azure VM 時，可以使用恢復計畫。 恢復計畫可説明您定義系統復原過程，該流程定義電腦容錯移轉的方式，以及它們在容錯移轉後如何啟動和恢復。 

恢復大型應用可能很複雜。 恢復計畫有助於強制實施順序，以便恢復始終準確、可重複和自動化。 您可以使用腳本和 Azure 自動化運行簿自動執行恢復計畫中的任務。 典型的示例可能是在容錯移轉後在 Azure VM 上配置設置，或者重新配置在 VM 上運行的應用。

- [深入了解](recovery-plan-overview.md) 復原計劃。
- [詳細瞭解](../automation/automation-runbook-types.md)Azure 自動化運行手冊。



## <a name="runbooks-in-recovery-plans"></a>恢復計畫中的運行簿

將 Azure 自動化帳戶和運行簿添加到恢復計畫。 運行恢復計畫時將調用 Runbook。

- 自動化帳戶可以位於任何 Azure 區域中，並且必須與網站恢復保存庫處於同一訂閱中。 
- 在容錯移轉期間，從主位置到輔助位置或在從輔助位置故障回退到主位置期間，Runbook 可以在恢復計畫中運行。
- 恢復計畫中的 Runbook 按設置的順序連續運行。
- 如果恢復計畫中的 Runbook 將 VM 配置為在不同的組中啟動，則僅當 Azure 報告所有 VM 為正在運行時，恢復計畫才會繼續。
- 恢復計畫將繼續運行，即使腳本失敗也是如此。

### <a name="recovery-plan-context"></a>恢復計畫上下文

當腳本運行時，它將恢復計畫上下文注入 Runbook。 上下文包含表中匯總的變數。

| **變數名稱** | **描述** |
| --- | --- |
| RecoveryPlanName |恢復計畫名稱。 用於基於名稱的操作。 |
| FailoverType |指定它是測試容錯移轉還是生產容錯移轉。 
| FailoverDirection | 指定恢復是到主位置還是輔助位置。 |
| GroupID |識別復原方案執行時方案內的群組編號。 |
| VmMap |群組中所有 VM 的陣列。 |
| VMMap 索引鍵 |每個 VM 的唯一索引鍵 (GUID)。 |
| SubscriptionId |建立 VM 的 Azure 訂用帳戶識別碼。 |
| resourceGroupName | VM 所在的資源組的名稱。
| CloudServiceName |在其下建立 VM 的 Azure 雲端服務名稱。 |
| RoleName |Azure VM 的名稱。 |
| RecoveryPointId|VM 恢復的時間戳記。 |

下列範例顯示內容變數：

```
{"RecoveryPlanName":"hrweb-recovery",
"FailoverType":"Test",
"FailoverDirection":"PrimaryToSecondary",
"GroupId":"1",
"VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":
    { "SubscriptionId":"7a1111111-c1d6-49c5-8c5d-111ce8dd183",
    "ResourceGroupName":"ContosoRG",
    "CloudServiceName":"pod02hrweb-Chicago-test",
    "RoleName":"Fabrikam-Hrweb-frontend-test",
    "RecoveryPointId":"TimeStamp"}
    }
}
```

如果要在迴圈中訪問 VMMap 中的所有 VM，可以使用以下代碼：

```
$VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
$vmMap = $RecoveryPlanContext.VmMap
    foreach($VMID in $VMinfo)
    {
        $VM = $vmMap.$VMID                
            if( !(($VM -eq $Null) -Or ($VM.ResourceGroupName -eq $Null) -Or ($VM.RoleName -eq $Null))) {
            #this check is to ensure that we skip when some data is not available else it will fail
    Write-output "Resource group name ", $VM.ResourceGroupName
    Write-output "Rolename " = $VM.RoleName
            }
        }
```


Aman Sharma 在[收穫雲](http://harvestingclouds.com)的博客有一個有用的例子，[一個恢復計畫上下文腳本](http://harvestingclouds.com/post/script-sample-azure-automation-runbook-for-asr-recovery-plan/)。



## <a name="before-you-start"></a>開始之前

- 如果您是 Azure 自動化的新功能，可以[註冊](https://azure.microsoft.com/services/automation/)並[下載示例腳本](https://azure.microsoft.com/documentation/scripts/)。
- 確定自動化帳戶具有下列模組：
    - AzureRM.profile
    - AzureRM.Resources
    - AzureRM.Automation
    - AzureRM.Network
    - AzureRM.Compute

    所有模組都必須是相容版本。 最簡單的方法是始終使用所有模組的最新版本。



## <a name="customize-the-recovery-plan"></a>自訂復原方案

1. 在保存庫中，選擇**恢復計畫（網站恢復）**
2. 要創建恢復計畫，請按一下 **"恢復計畫**"。 [深入了解](site-recovery-create-recovery-plans.md)。 如果您已經有一個恢復計畫，然後選擇打開它。
3. 在恢復計畫頁中，按一下 **"自訂**"。

    ![按一下 [自訂] 按鈕](media/site-recovery-runbook-automation-new/custom-rp.png)

2. 按一下組 1 旁邊的橢圓 （...）：**開始** > **添加帖子操作**。
3. 在 **"插入"操作**中，驗證**腳本**是否被選中，並指定腳本的名稱 **（Hello World）。**
4. 指定自動化帳戶並選擇 Runbook。 若要儲存指令碼，請按一下 [確定]****。 指令碼會新增至 [群組 1: 後續步驟]****。


## <a name="reuse-a-runbook-script"></a>重用 Runbook 腳本

您可以使用外部變數在多個恢復計畫中使用單個 Runbook 腳本。 

- 使用[Azure 自動化變數](../automation/automation-variables.md)存儲用於運行恢復計畫的參數。
- 您可以在變數前面加上復原方案名稱，為每個復原方案建立個別變數。 然後，使用這些變數作為參數。
- 您可以變更參數而不需要變更指令碼，但仍變更指令碼的運作方式。

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>在 Runbook 指令碼中使用簡單的字串變數

在此示例中，腳本獲取網路安全性群組 （NSG） 的輸入，並將其應用於恢復計畫中的 VM。 

1. 以便腳本可以檢測正在運行的恢復計畫，請使用此恢復計畫上下文：

    ```
    workflow AddPublicIPAndNSG {
        param (
              [parameter(Mandatory=$false)]
              [Object]$RecoveryPlanContext
        )

        $RPName = $RecoveryPlanContext.RecoveryPlanName
    ```

2. 請注意 NSG 名稱和資源組。 使用這些變數作為恢復計畫腳本的輸入。 
1. 在自動化帳戶資產中。 創建變數以存儲 NSG 名稱。 向具有恢復計畫名稱的變數名稱添加首碼。

    ![建立 NSG 名稱變數](media/site-recovery-runbook-automation-new/var1.png)

2. 創建變數以存儲 NSG 資源的資源組名稱。 向具有恢復計畫名稱的變數名稱添加首碼。

    ![建立 NSG 資源群組名稱](media/site-recovery-runbook-automation-new/var2.png)


3.  在腳本中，使用此引用代碼獲取變數值：

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  在 Runbook 中使用變數，將 NSG 套用至已容錯移轉之 VM 的網路介面：

    ```
    InlineScript {
    if (($Using:NSGname -ne $Null) -And ($Using:NSGRGname -ne $Null)) {
            $NSG = Get-AzureRmNetworkSecurityGroup -Name $Using:NSGname -ResourceGroupName $Using:NSGRGname
            Write-output $NSG.Id
            #Apply the NSG to a network interface
            #$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
            #Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            #  -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $NSG
        }
    }
    ```


針對每個復原方案，建立獨立變數讓您可以重複使用指令碼。 在開頭加上復原方案名稱。 

有關此方案的完整端到端腳本，請查看[此腳本](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee)。


### <a name="use-a-complex-variable-to-store-more-information"></a>使用複雜變數來儲存詳細資訊

在某些情況下，可能無法為每個恢復計畫創建單獨的變數。 請考慮一種方案，即您希望單個腳本在特定 VM 上分配公共 IP 位址。 在另一個案例中，您可能想要將不同的 NSG 套用至不同的 VM (並非所有 VM)。 請注意：

- 您可以製作可用於任何恢復計畫的腳本。
- 每個復原方案可以有任意數目的 VM。
- 例如，SharePoint 復原有兩個前端。 基本企業營運 (LOB) 應用程式只有一個前端。
- 在這種情況下，不能為每個恢復計畫創建單獨的變數。

在下面的示例中，我們在 Azure 自動化帳戶中創建一[個複雜的變數](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureautomationvariable)。

為此，我們使用 Azure PowerShell 指定多個值。

1. 在 PowerShell 中，登入您的 Azure 訂用帳戶：

    ```
    Connect-AzureRmAccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. 要存儲參數，請使用恢復計畫的名稱創建複雜變數：

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. 在此複雜變數中，**VMDetails** 是受保護 VM 的 VM 識別碼。 您若要取得 VM 識別碼，請在 Azure 入口網站中，檢視 VM 屬性。 下列螢幕擷取畫面顯儲存兩個 VM 之詳細資料的變數：

    ![使用 VM 識別碼作為 GUID](media/site-recovery-runbook-automation-new/vmguid.png)

4. 在您的 Runbook 中使用此變數。 如果在復原方案內容中找到指定的 VM GUID，請將 NSG 套用至 VM：

    ```
    $VMDetailsObj = (Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName).ToObject([hashtable])
    ```

4. 在您的 Runbook 中，循環存取復原方案內容的 VM。 檢查 VM 是否存在於 **$VMDetailsObj** 中。 如果存在的話，則存取變數的屬性來套用 NSG：

    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap

        foreach($VMID in $VMinfo) {
            $VMDetails = $VMDetailsObj[$VMID].ToObject([hashtable]);
            Write-output $VMDetails
            if ($VMDetails -ne $Null) { #If the VM exists in the context, this will not be Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetails.NSGName
                $NSGRGname = $VMDetails.NSGResourceGroupName

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

您可以將相同的指令碼用於不同的復原方案。 藉由在不同的變數中儲存對應至復原方案的值，來輸入不同的參數。

## <a name="sample-scripts"></a>範例指令碼

若要將範例指令碼部署至您的自動化帳戶，請按一下 [部署至 Azure]**** 按鈕。

[![部署到 Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

此視頻提供了另一個示例。 該影片示範如何將兩層式 WordPress 應用程式復原至 Azure：


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="next-steps"></a>後續步驟

- 瞭解[Azure 自動化作為帳戶運行](../automation/automation-create-runas-account.md)
- 查看[Azure 自動化示例腳本](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=User&f%5B0%5D.Value=SC%20Automation%20Product%20Team&f%5B0%5D.Text=SC%20Automation%20Product%20Team)。
- [瞭解有關](site-recovery-failover.md)運行容錯移轉的更多詳細資訊。



