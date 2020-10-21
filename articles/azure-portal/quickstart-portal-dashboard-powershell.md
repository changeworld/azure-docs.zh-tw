---
title: 使用 PowerShell 建立 Azure 入口網站儀表板
description: 了解如何使用 Azure PowerShell 在 Azure 入口網站中建立儀表板。
author: mgblythe
ms.service: azure-portal
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.author: mblythe
ms.date: 07/24/2020
ms.openlocfilehash: de0394f9b3254931537441d9f44606d16392a62d
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92073881"
---
# <a name="quickstart-create-an-azure-portal-dashboard-with-powershell"></a>快速入門：使用 PowerShell 建立 Azure 入口網站儀表板

Azure 入口網站中的儀表板是您雲端資源的聚焦及經過組織的檢視。 本文著重於使用 Az.Portal PowerShell 模組來建立儀表板的程序。
儀表板會顯示虛擬機器 (VM) 的效能，以及一些靜態資訊和連結。

## <a name="requirements"></a>需求

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

如果您選擇在本機使用 PowerShell，本文會要求您安裝 Az PowerShell 模組，並使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) Cmdlet 連線到您的 Azure 帳戶。 如需安裝 Az PowerShell 模組的詳細資訊，請參閱[安裝 Azure PowerShell](/powershell/azure/install-az-ps)。

> [!IMPORTANT]
> 雖然 **Az.Portal** PowerShell 模組處於預覽狀態，但您仍必須使用 `Install-Module` Cmdlet，將其與 Az PowerShell 模組分開安裝。 在此 PowerShell 模組正式推出後，會成為未來 Az PowerShell 模組版本的一部分，並可從 Azure Cloud Shell 內以原生方式使用。

```azurepowershell-interactive
Install-Module -Name Az.Portal
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="choose-a-specific-azure-subscription"></a>選擇特定的 Azure 訂用帳戶

如果您有多個 Azure 訂用帳戶，請選擇資源計費的適當訂用帳戶。 使用 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) Cmdlet 來選取特定的訂用帳戶。

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="define-variables"></a>定義變數

您將會重複使用數項資訊。 建立變數來儲存資訊。

```azurepowershell-interactive
# Name of resource group used throughout this article
$resourceGroupName = 'myResourceGroup'

# Azure region
$location = 'centralus'

# Dashboard Title
$dashboardTitle = 'Simple VM Dashboard'

# Dashboard Name
$dashboardName = $dashboardTitle -replace '\s'

# Your Azure Subscription ID
$subscriptionID = (Get-AzContext).Subscription.Id

# Name of test VM
$vmName = 'SimpleWinVM'
```

## <a name="create-a-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Cmdlet 來建立 [Azure 資源群組](../azure-resource-manager/management/overview.md)。 資源群組是一個邏輯容器，Azure 資源會在其中以群組方式部署及管理。

下列範例會根據 `$location` 變數所指定區域中 `$resourceGroupName` 變數中的名稱，建立資源群組。

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
```

## <a name="create-a-virtual-machine"></a>建立虛擬機器

您在本快速入門下一個部分中所建立的儀表板需要現有的 VM。 依照下列步驟建立 VM。

將 VM 的登入認證儲存在變數中。 必須使用複雜密碼。 這是新的使用者名稱和密碼；而不是您用來登入 Azure 的帳戶。 如需詳細資訊，請參閱[使用者名稱需求](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm)和[密碼需求](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)。

```azurepowershell-interactive
$Cred = Get-Credential
```

建立 VM。

```azurepowershell-interactive
$AzVmParams = @{
  ResourceGroupName = $resourceGroupName
  Name = $vmName
  Location = $location
  Credential = $Cred
}
New-AzVm @AzVmParams
```

VM 部署現在開始，通常需要數分鐘的時間才能完成。 部署完成之後，請移至下一節。

## <a name="download-the-dashboard-template"></a>下載儀表板範本

因為 Azure 儀表板屬於資源，所以能夠以 JSON 的形式呈現。 下列程式碼會下載範例儀表板的 JSON 表示法。 如需詳細資訊，請參閱 [Azure 儀表板的結構](./azure-portal-dashboards-structure.md)。

```azurepowershell-interactive
$myPortalDashboardTemplateUrl = 'https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json'

$myPortalDashboardTemplatePath = "$env:TEMP\portal-dashboard-template-testvm.json"

Invoke-WebRequest -Uri $myPortalDashboardTemplateUrl -OutFile $myPortalDashboardTemplatePath -UseBasicParsing
```

## <a name="customize-the-template"></a>自訂範本

執行下列程式碼來自訂已下載的範本。

```azurepowershell
$Content = Get-Content -Path $myPortalDashboardTemplatePath -Raw
$Content = $Content -replace '<subscriptionID>', $subscriptionID
$Content = $Content -replace '<rgName>', $resourceGroupName
$Content = $Content -replace '<vmName>', $vmName
$Content = $Content -replace '<dashboardTitle>', $dashboardTitle
$Content = $Content -replace '<location>', $location
$Content | Out-File -FilePath $myPortalDashboardTemplatePath -Force
```

如需詳細資訊，請參閱 [Microsoft 入口網站儀表板範本參考](/azure/templates/microsoft.portal/dashboards)。

## <a name="deploy-the-dashboard-template"></a>部署儀表板範本

您可以使用屬於 Az.Portal 模組的 `New-AzPortalDashboard` Cmdlet，直接從 PowerShell 部署範本。

```azurepowershell
$DashboardParams = @{
  DashboardPath = $myPortalDashboardTemplatePath
  ResourceGroupName = $resourceGroupName
  DashboardName = $dashboardName
}
New-AzPortalDashboard @DashboardParams
```

## <a name="review-the-deployed-resources"></a>檢閱已部署的資源

檢查是否已成功建立儀表板。

```azurepowershell
Get-AzPortalDashboard -Name $dashboardName -ResourceGroupName $resourceGroupName
```

確認您可以從 Azure 入口網站中查看 VM 的相關資料。

1. 在 Azure 入口網站中，選取 [儀表板]。

   ![Azure 入口網站導覽至儀表板](media/quickstart-portal-dashboard-powershell/navigate-to-dashboards.png)

1. 在儀表板頁面上，選取 [簡單的 VM 儀表板]。

   ![瀏覽至簡單的 VM 儀表板](media/quickstart-portal-dashboard-powershell/select-simple-vm-dashboard.png)

1. 檢閱儀表板。 您可以看到某些內容是靜態的，但是也有圖表顯示 VM 效能。

   ![檢閱簡單的 VM 儀表板](media/quickstart-portal-dashboard-powershell/review-simple-vm-dashboard.png)

## <a name="clean-up-resources"></a>清除資源

若要移除 VM 和相關聯的儀表板，請刪除包含上述項目的資源群組。

> [!CAUTION]
> 下列範例會刪除指定的資源群組和其中包含的所有資源。
> 如果本文章範圍以外的資源存在於指定的資源群組中，則也會一併刪除。

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>後續步驟

如需 Az.Portal PowerShell 模組中所含 Cmdlet 的詳細資訊，請參閱：

> [!div class="nextstepaction"]
> [Microsoft Azure PowerShell：入口網站儀表板 Cmdlet](/powershell/module/Az.Portal/)