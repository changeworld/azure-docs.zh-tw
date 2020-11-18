---
title: 教學課程：使用 PowerShell 部署到現有的虛擬網路 -Azure 專用 HSM | Microsoft Docs
description: 示範如何使用 PowerShell 將專用 HSM 部署到現有虛擬網路中的教學課程
services: dedicated-hsm
documentationcenter: na
author: msmbaldwin
manager: rkarlin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18, devx-track-azurepowershell
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/14/2020
ms.author: johndaw
ms.openlocfilehash: ee431df89128a516e3a1cabeb43b5cbe9e356dae
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92927850"
---
# <a name="tutorial--deploying-hsms-into-an-existing-virtual-network-using-powershell"></a>教學課程 – 使用 PowerShell 將 HSM 部署至現有的虛擬網路

Azure 專用 HSM 服務提供實體裝置以供單獨客戶使用，其具有完整的系統管理控制權和完整的管理責任。 Microsoft 會提供實體硬體，因此必須控制這些裝置的配置方式，以確保有效地管理容量。 因此，在 Azure 訂用帳戶內，通常看不見可供佈建資源的專用 HSM 服務。 任何需要存取專用 HSM 服務的 Azure 客戶都必須先連絡其 Microsoft 客戶代表，要求註冊專用 HSM 服務。 唯有順利完成此程序，才可能進行佈建。
本教學課程主要示範典型的佈建程序，其中：

- 客戶已經有虛擬網路
- 他們有虛擬機器
- 他們必須將 HSM 資源新增到該現有的環境中。

高可用性、多區域的典型部署架構可能如下所示：

![多區域部署](media/tutorial-deploy-hsm-powershell/high-availability.png)

本教學課程著重於一對已整合到現有虛擬網路 (請參閱上述的 VNET 1) 的 HSM 和必要的 ExpressRoute 閘道 (請參閱上述的子網路 1)。  所有其他資源都是標準 Azure 資源。 相同的整合程序可以用於上述 VNET 3 上子網路 4 中的 HSM。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisites

Azure 入口網站目前不提供 Azure 專用 HSM ，因此與服務的所有互動都是透過命令列或使用 PowerShell 進行。 本教學課程會在 Azure Cloud Shell 中使用 PowerShell。 如果您不熟悉 PowerShell，請遵循以下的使用者入門指示：[Azure PowerShell 使用者入門](/powershell/azure/get-started-azureps)。

假設：

- 您已完成 Azure 專用 HSM 註冊程序並且獲准使用服務。 若非如此，則連絡 Microsoft 客戶代表以取得詳細資料。 
- 您已建立這些資源的資源群組，而本教學課程中部署的新資訊將會加入該群組。
- 您已經按照上圖建立所需的虛擬網路、子網路和虛擬機器，而現在想要將 2 個 HSM 整合到該部署中。

以下所有指示都假設您已經導覽至 Azure 入口網站，並已開啟 Cloud Shell (選取靠近入口網站右上方的 “\>\_”)。

## <a name="provisioning-a-dedicated-hsm"></a>佈建專用 HSM

透過 ExpressRoute 閘道佈建 HSM 並將其整合到現有的虛擬網路時，會使用 ssh 來驗證，以確保 HSM 裝置的連線性和基本可用性，以便進行任何進一步的設定活動。 下列命令會使用 Resource Manager 範本來建立 HSM 資源和相關聯的網路資源。

### <a name="validating-feature-registration"></a>驗證功能註冊

如上所述，任何佈建活動都要求向您的訂用帳戶註冊專用 HSM 服務。 若要加以驗證，請在 Azure 入口網站 Cloud Shell 中執行下列 PowerShell 命令。 

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.HardwareSecurityModules -FeatureName AzureDedicatedHsm
```

在您繼續進行前，命令應該會傳回 “Registered” 狀態 (如下所示)。  如果您尚未註冊此服務，請連絡您的 Microsoft 帳戶代表。

![訂用帳戶狀態](media/tutorial-deploy-hsm-powershell/subscription-status.png)

### <a name="creating-hsm-resources"></a>建立 HSM 資源

HSM 裝置會佈建到客戶的虛擬網路中。 這表示子網路的需求。 ExpressRoute 閘道是可供 HSM 啟用虛擬網路與實體裝置之間通訊的相依性，而虛擬機器最終需要使用 Gemalto 用戶端軟體存取 HSM 裝置。 這些資源已收集到範本檔案中 (具有對應的參數檔案)，以便使用。 直接以 HSMrequest@Microsoft.com 連絡 Microsoft 即可取得檔案。

擁有檔案後，您必須編輯參數檔案，以插入慣用的資源名稱。 這表示使用 “value”: “” 編輯程式碼行。

- `namingInfix` HSM 資源名稱的首碼
- `ExistingVirtualNetworkName` 用於 HSM 的虛擬網路名稱
- `DedicatedHsmResourceName1` 資料中心戳記 1 中的 HSM 資源名稱
- `DedicatedHsmResourceName2` 資料中心戳記 2 中的 HSM 資源名稱
- `hsmSubnetRange` HSM 的子網路 IP 位址範圍
- `ERSubnetRange` VNET 閘道的子網路 IP 位址範圍

這些變更的範例如下所示：

```json
{
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "namingInfix": {
      "value": "MyHSM"
    },
    "ExistingVirtualNetworkName": {
      "value": "MyHSM-vnet"
    },
    "DedicatedHsmResourceName1": {
      "value": "HSM1"
    },
    "DedicatedHsmResourceName2": {
      "value": "HSM2"
    },
    "hsmSubnetRange": {
      "value": "10.0.2.0/24"
    },
    "ERSubnetRange": {
      "value": "10.0.255.0/26"
    },
  }
}
```

相關聯的 Resource Manager 範本檔案會以此資訊建立 6 個資源：

- 指定的 VNET 中 HSM 的子網路
- 虛擬網路閘道的子網路 
- 可將 VNET 連線到 HSM 裝置的虛擬網路閘道
- 閘道的公用 IP 位址
- 戳記 1 中的 HSM
- 戳記 2 中的 HSM

設定參數值後，必須將檔案上傳至 Azure 入口網站 Cloud Shell 檔案共用，以便使用。 在 Azure 入口網站中，按一下右上方的 “\>\_” Cloud Shell 符號，這會讓畫面的底部成為命令環境。 其選項包含 BASH 和 PowerShell，而您應該選取 BASH (如果尚未設定)。

命令殼層在工具列上有上傳/下載選項，您應該選取此選項以將範本和參數檔案上載至您的檔案共用：

![檔案共用](media/tutorial-deploy-hsm-powershell/file-share.png)

上傳檔案後，您就準備好建立資源。
建立新的 HSM 資源之前，有一些您應確保已備妥的必要資源。 您必須具有虛擬網路，內含適用於計算、HSM 和閘道的子網路範圍。 下列命令可作為建立這類虛擬網路的範例。

```powershell
$compute = New-AzVirtualNetworkSubnetConfig `
  -Name compute `
  -AddressPrefix 10.2.0.0/24
```

```powershell
$delegation = New-AzDelegation `
  -Name "myDelegation" `
  -ServiceName "Microsoft.HardwareSecurityModules/dedicatedHSMs"

```

```powershell
$hsmsubnet = New-AzVirtualNetworkSubnetConfig ` 
  -Name hsmsubnet ` 
  -AddressPrefix 10.2.1.0/24 ` 
  -Delegation $delegation 

```

```powershell

$gwsubnet= New-AzVirtualNetworkSubnetConfig `
  -Name GatewaySubnet `
  -AddressPrefix 10.2.255.0/26

```

```powershell

New-AzVirtualNetwork `
  -Name myHSM-vnet `
  -ResourceGroupName myRG `
  -Location westus `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $compute, $hsmsubnet, $gwsubnet

```

>[!NOTE]
>請注意，對於虛擬網路而言最重要的組態是 HSM 裝置的子網路必須將委派設定為 “Microsoft.HardwareSecurityModules/dedicatedHSMs”。  若未設定，HSM 佈建將無法運作。

一旦備妥所有必要條件，請執行下列命令以使用 Resource Manager 範本，確保您已使用您唯一的名稱來更新值 (最少資源群組名稱)：

```powershell

New-AzResourceGroupDeployment -ResourceGroupName myRG `
     -TemplateFile .\Deploy-2HSM-toVNET-Template.json `
     -TemplateParameterFile .\Deploy-2HSM-toVNET-Params.json `
     -Name HSMdeploy -Verbose

```

此命令應該需要大約 20 分鐘才能完成。 使用的 “-verbose” 選項可確保持續顯示狀態。

![佈建狀態](media/tutorial-deploy-hsm-powershell/progress-status.png)

“provisioningState”: “Succeeded” 表示順利完成時，您可以連線到現有的虛擬機器，並使用 SSH 來確保 HSM 裝置的可用性。

## <a name="verifying-the-deployment"></a>確認部署

若要驗證是否已佈建裝置以及查看裝置屬性，請執行下列命令集。 請確定已適當設定資源群組，且資源名稱完全與您在參數檔案中擁有的資源名稱相同。

```powershell

$subid = (Get-AzContext).Subscription.Id
$resourceGroupName = "myRG"
$resourceName = "HSM1"  
Get-AzResource -Resourceid /subscriptions/$subId/resourceGroups/$resourceGroupName/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/$resourceName

```

![佈建狀態](media/tutorial-deploy-hsm-powershell/progress-status2.png)

您現在也可以使用 [Azure 資源總管](https://resources.azure.com/)來查看資源。   在總管中，依序展開左側的 [訂用帳戶]、專用 HSM 的特定訂用帳戶、資源群組、您所使用的資源群組，最後選取 [資源] 項目。

## <a name="testing-the-deployment"></a>測試部署

測試部署時，請連線到可存取 HSM 的虛擬機器，然後直接連線到 HSM 裝置。 這些動作會確認 HSM 可連線。
SSH 工具用於連線至虛擬機器。 此命令將如下所示，但採用您在參數中指定的系統管理員名稱和 dns 名稱。

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

所要使用的密碼為參數檔案中的密碼。
登入 Linux VM 後，即可使用在資源 \<prefix>hsm_vnic 的入口網站中找到的私人 IP 位址登入 HSM。

```powershell

(Get-AzResource -ResourceGroupName myRG -Name HSMdeploy -ExpandProperties).Properties.networkProfile.networkInterfaces.privateIpAddress

```
當您擁有 IP 位址時，請執行下列命令：

`ssh tenantadmin@<ip address of HSM>`

如果成功，系統會提示您輸入密碼。 預設密碼為 PASSWORD。 HSM 會要求您變更您的密碼，以設定強式密碼，然後使用貴組織所偏好的機制來儲存密碼並防止遺失。  

>[!IMPORTANT]
>如果您遺失此密碼，HSM 必須重設，這表示您的金鑰會遺失。

當您使用 ssh 連線到 HSM 裝置時，請執行下列命令，以確保 HSM 運作正常。

`hsm show`

其輸出應類似下圖所示：

![螢幕擷取畫面，顯示 hsm (顯示命令) 的輸出。](media/tutorial-deploy-hsm-powershell/output.png)

此時，您已配置所有資源，以提供高可用性、兩個 HSM 部署和已驗證的存取和操作狀態。 任何進一步的設定或測試都牽涉到更多 HSM 裝置本身的工作。 為此，您應該遵循 Gemalto Luna Network HSM 7 Administration Guide 第 7 章的指示，來初始化 HSM 及建立分割區。 您在 Gemalto 客戶支援入口網站中註冊且具有客戶識別碼後，可以直接從 Gemalto 下載所有文件和軟體。 下載用戶端軟體 7.2 版，以取得所有必要的元件。

## <a name="delete-or-clean-up-resources"></a>刪除或清除資源

如果您已處理完 HSM 裝置，即可將它當作資源刪除並傳回可用的集區。 執行此作業時的顯著考量就是裝置上的任何敏感性客戶資料。 將裝置「歸零」的最佳方式是讓 HSM 管理員的密碼連錯 3 次 (注意：這裡指的不是裝置管理員，而是實際的 HSM 管理員)。 由於金鑰內容有安全措施保護，裝置必須處於歸零狀態，才能以 Azure 資源的形式刪除。

> [!NOTE]
> 如果您有關於任何 Gemalto 裝置組態的問題，您應該連絡 [Gemalto 客戶支援](https://safenet.gemalto.com/technical-support/)。

如果您想要移除 Azure 中的 HSM 資源，您可以使用下列命令將 "$" 變數取代為您的唯一參數：

```powershell

$subid = (Get-AzContext).Subscription.Id
$resourceGroupName = "myRG" 
$resourceName = "HSMdeploy"  
Remove-AzResource -Resourceid /subscriptions/$subId/resourceGroups/$resourceGroupName/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/$resourceName 

```

## <a name="next-steps"></a>後續步驟

完成本教學課程中的步驟之後，就會佈建專用 HSM 資源並可使用於您的虛擬網路中。 您現在可以慣用部署架構所需的更多資源，讓此部署更加完善。 如需有關協助規劃部署的詳細資訊，請參閱「概念」文件。 建議採用以下設計：主要區域中有兩個 HSM 可處理機架層級的可用性，而次要區域中有兩個 HSM 可處理區域可用性。 本教學課程中所用的範本檔案可輕鬆地作為兩個 HSM 部署的基礎，但必須修改其參數以能夠以符合您的需求。

* [高可用性](high-availability.md)
* [實體安全性](physical-security.md)
* [網路功能](networking.md)
* [監視](monitoring.md)
* [支援能力](supportability.md)
