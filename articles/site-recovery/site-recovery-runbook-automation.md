---
title: 將 Azure 自動化 runbook 新增至 Site Recovery 復原方案
description: 瞭解如何使用 Azure Site Recovery 擴充復原計畫，以 Azure 自動化進行嚴重損壞修復。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: ramamill
ms.openlocfilehash: a141280338632fdad7053cbbe76c8bdf2797443d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89424866"
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>將 Azure 自動化 Runbook 新增至復原方案

本文說明如何整合 Azure 自動化的 runbook，以擴充 [Azure Site Recovery](site-recovery-overview.md) 復原方案。 我們會示範如何將需要手動介入的基本工作自動化，以及如何將多步驟復原轉換成單鍵動作。

## <a name="recovery-plans"></a>復原計畫 

當您容錯移轉內部部署機器或 Azure Vm 時，可以使用復原方案。 復原方案可協助您定義系統化復原程式，以定義機器容錯移轉的方式，以及在容錯移轉之後啟動和復原的方式。 

復原大型應用程式可能很複雜。 復原方案有助於強制執行訂單，讓復原保持一致、可重複且自動化。 您可以使用腳本以及 Azure 自動化的 runbook，將復原方案內的工作自動化。 一般範例可能是在容錯移轉後於 Azure VM 上進行設定，或重新設定在 VM 上執行的應用程式。

- [深入了解](recovery-plan-overview.md) 復原計劃。
- [深入瞭解](../automation/automation-runbook-types.md) Azure 自動化 runbook。



## <a name="runbooks-in-recovery-plans"></a>復原方案中的 runbook

您可以將 Azure 自動化帳戶和 runbook 新增至復原方案。 執行復原方案時，會叫用 runbook。

- 自動化帳戶可以位於任何 Azure 區域中，而且必須位於與 Site Recovery 保存庫相同的訂用帳戶中。 
- Runbook 可以在從主要位置容錯移轉至次要位置，或從次要位置容錯回復到主要位置時，在復原方案中執行。
- 復原計畫中的 runbook 會依照設定順序，以序列方式一次執行。
- 如果復原方案中的 runbook 將 Vm 設定為在不同的群組中啟動，則只有當 Azure 將所有 Vm 回報為執行時，才會繼續復原方案。
- 即使腳本失敗，復原方案仍會繼續執行。

### <a name="recovery-plan-context"></a>復原方案內容

當腳本執行時，它會在 runbook 中插入復原方案內容。 內容包含資料表中摘要說明的變數。

| **變數名稱** | **說明** |
| --- | --- |
| RecoveryPlanName |復原方案名稱。 用於以名稱為基礎的動作。 |
| FailoverType |指定這是測試或生產容錯移轉。 
| FailoverDirection | 指定復原是否為主要或次要位置。 |
| GroupID |識別復原方案執行時方案內的群組編號。 |
| VmMap |群組中所有 VM 的陣列。 |
| VMMap 索引鍵 |每個 VM 的唯一索引鍵 (GUID)。 |
| SubscriptionId |建立 VM 的 Azure 訂用帳戶識別碼。 |
| resourceGroupName | VM 所在之資源群組的名稱。
| CloudServiceName |在其下建立 VM 的 Azure 雲端服務名稱。 |
| RoleName |Azure VM 的名稱。 |
| RecoveryPointId|VM 復原的時間戳記。 |

>[!Note]
>如果發生容錯移轉，變數 ' FailoverDirection ' 的值將會是 ' PrimaryToSecondary '，而在容錯回復時則為 ' SecondaryToPrimary '。

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

如果您想要在迴圈中存取存取 vmmap 中的所有 Vm，您可以使用下列程式碼：

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


Aman Sharma 的 blog 在 [搜集](http://harvestingclouds.com) 雲端上，有一個實用的復原 [方案內容腳本](http://harvestingclouds.com/post/script-sample-azure-automation-runbook-for-asr-recovery-plan/)範例。



## <a name="before-you-start"></a>開始之前

- 如果您是 Azure 自動化的新手，您可以 [註冊](https://azure.microsoft.com/services/automation/) 並 [下載範例腳本](https://azure.microsoft.com/documentation/scripts/)。
- 確定自動化帳戶具有下列模組：
    - AzureRM.profile
    - AzureRM.Resources
    - AzureRM.Automation
    - AzureRM.Network
    - AzureRM.Compute

    所有模組都必須是相容版本。 最簡單的方式是一律使用所有模組的最新版本。



## <a name="customize-the-recovery-plan"></a>自訂復原方案

1. 在保存庫中，選取 [復原 **方案] (Site Recovery) **
2. 若要建立復原方案，請按一下 [ **+ 復原方案**]。 [深入了解](site-recovery-create-recovery-plans.md)。 如果您已經有復原方案，請選取以開啟它。
3. 在 [復原計畫] 頁面中，按一下 [ **自訂**]。

    ![按一下 [自訂] 按鈕](media/site-recovery-runbook-automation-new/custom-rp.png)

2. 按一下 [**群組1：開始**  >  **新增張貼動作**] 旁的省略號 ( ... ) 。
3. 在 [ **插入動作**] 中，確認已選取 [ **腳本** ]，並指定腳本的名稱 (**Hello World**) 。
4. 指定自動化帳戶並選取 runbook。 若要儲存指令碼，請按一下 [確定]****。 指令碼會新增至 [群組 1: 後續步驟]****。


## <a name="reuse-a-runbook-script"></a>重複使用 runbook 腳本

您可以使用外部變數，在多個復原方案中使用單一 runbook 腳本。 

- 您可以使用 [Azure 自動化變數](../automation/shared-resources/variables.md) 來儲存用來執行復原計畫的參數。
- 您可以在變數前面加上復原方案名稱，為每個復原方案建立個別變數。 然後，使用這些變數作為參數。
- 您可以變更參數而不需要變更指令碼，但仍變更指令碼的運作方式。

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>在 Runbook 指令碼中使用簡單的字串變數

在此範例中，腳本會使用網路安全性群組 (NSG) 的輸入，並將其套用至復原方案中的 Vm。 

1. 因此，腳本可以偵測正在執行的復原方案，請使用此復原方案內容：

    ```
    workflow AddPublicIPAndNSG {
        param (
              [parameter(Mandatory=$false)]
              [Object]$RecoveryPlanContext
        )

        $RPName = $RecoveryPlanContext.RecoveryPlanName
    ```

2. 請注意 NSG 名稱和資源群組。 您可以使用這些變數作為復原方案腳本的輸入。 
1. 在自動化帳戶資產中。 建立變數來儲存 NSG 名稱。 以復原方案的名稱加入變數名稱的前置詞。

    ![建立 NSG 名稱變數](media/site-recovery-runbook-automation-new/var1.png)

2. 建立變數來儲存 NSG 資源的資源組名。 以復原方案的名稱加入變數名稱的前置詞。

    ![建立 NSG 資源群組名稱](media/site-recovery-runbook-automation-new/var2.png)


3.  在腳本中，使用下列參考程式碼來取得變數值：

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

如需此案例的完整端對端腳本，請參閱 [此腳本](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee)。


### <a name="use-a-complex-variable-to-store-more-information"></a>使用複雜變數來儲存詳細資訊

在某些情況下，您可能無法為每個復原方案建立個別變數。 假設您想要在特定 Vm 上將公用 IP 位址指派給單一腳本的案例。 在另一個案例中，您可能想要將不同的 NSG 套用至不同的 VM (並非所有 VM)。 請注意：

- 您可以針對任何復原方案建立可重複使用的腳本。
- 每個復原方案可以有任意數目的 VM。
- 例如，SharePoint 復原有兩個前端。 基本企業營運 (LOB) 應用程式只有一個前端。
- 在此案例中，您無法為每個復原方案建立個別變數。

在下列範例中，我們會建立 Azure 自動化帳戶中的 [複雜變數](/powershell/module/servicemanagement/azure.service/set-azureautomationvariable) 。

若要這麼做，請使用 Azure PowerShell 指定多個值。

1. 在 PowerShell 中，登入您的 Azure 訂用帳戶：

    ```
    Connect-AzureRmAccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. 若要儲存參數，請使用復原方案的名稱來建立複雜變數：

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

[![部署至 Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

這段影片提供另一個範例。 該影片示範如何將兩層式 WordPress 應用程式復原至 Azure：


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="next-steps"></a>後續步驟

- 瞭解 [Azure 自動化執行帳戶](../automation/manage-runas-account.md)
- 請參閱 [Azure 自動化範例腳本](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=User&f%5B0%5D.Value=SC%20Automation%20Product%20Team&f%5B0%5D.Text=SC%20Automation%20Product%20Team)。
- [深入瞭解](site-recovery-failover.md) 如何執行容錯移轉。
