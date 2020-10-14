---
title: 使用 Azure PowerShell 建立 Azure 自訂資源提供者
description: 說明使用 Azure PowerShell 建立 Azure 自訂資源提供者
author: MSEvanhi
ms.author: evanhi
ms.topic: quickstart
ms.devlang: azurepowershell
ms.date: 09/22/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6912f7f6fdc88c5d611bfbfd78f15e5f7a949f70
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951837"
---
# <a name="quickstart-create-an-azure-custom-resource-provider-with-azure-powershell"></a>快速入門：使用 Azure PowerShell 建立 Azure 自訂資源提供者

在本快速入門中，您將了解如何使用 [Az.CustomProviders](/powershell/module/az.customproviders) PowerShell 模組建立自己的 Azure 自訂資源提供者。

> [!CAUTION]
> Azure 自訂提供者目前處於公開預覽狀態。 所提供的這個預覽版本並沒有服務等級協定。 不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="requirements"></a>需求

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

如果您選擇在本機使用 PowerShell，本文會要求您安裝 Az PowerShell 模組，並使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) Cmdlet 連線到您的 Azure 帳戶。 如需安裝 Az PowerShell 模組的詳細資訊，請參閱[安裝 Azure PowerShell](/powershell/azure/install-az-ps)。 如果您選擇使用 Cloud Shell，請參閱 [Azure Cloud Shell 的概觀](../../cloud-shell/overview.md)以取得詳細資訊。

> [!IMPORTANT]
> **Az.CustomProviders** PowerShell 模組處於預覽狀態，因此您必須使用 `Install-Module` Cmdlet 將其分開安裝。 在此 PowerShell 模組正式推出後，會成為未來 Az PowerShell 模組版本的一部分，並可從 Azure Cloud Shell 內以原生方式使用。

```azurepowershell-interactive
Install-Module -Name Az.CustomProviders
```

如果您有多個 Azure 訂用帳戶，請選擇資源計費的適當訂用帳戶。 使用 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) Cmdlet 來選取特定的訂用帳戶。

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Cmdlet 來建立 [Azure 資源群組](../../azure-resource-manager/management/overview.md)。 資源群組是一個邏輯容器，Azure 資源會在其中以群組方式部署及管理。

下列範例會在指定的位置使用指定的名稱建立資源群組。

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="create-a-custom-resource-provider"></a>建立自訂資源提供者

若要建立或更新自訂資源提供者，請使用 [New-AzCustomProvider](/powershell/module/az.customproviders/new-azcustomprovider) Cmdlet，如下列範例所示。

```azurepowershell-interactive
New-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type -Location westus2 -ResourceType @{Name='CustomRoute1'; Endpoint='https://www.contoso.com/'}
```

## <a name="get-the-custom-resource-provider-manifest"></a>取得自訂資源提供者資訊清單

若要擷取自訂資源提供者資訊清單的相關資訊，請使用 [Get-AzCustomProvider](/powershell/module/az.customproviders/get-azcustomprovider) Cmdlet，如下列範例所示。

```azurepowershell-interactive
Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type | Format-List
```

## <a name="create-an-association"></a>建立關聯

若要建立或更新關聯，請使用 [New-AzCustomProviderAssociation](/powershell/module/az.customproviders/new-azcustomproviderassociation) Cmdlet，如下列範例所示。

```azurepowershell-interactive
$provider = Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
New-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc -TargetResourceId $provider.Id
```

## <a name="get-an-association"></a>取得關聯

若要擷取關聯的相關資訊，請使用 [Get-AzCustomProviderAssociation](/powershell/module/az.customproviders/get-azcustomproviderassociation) Cmdlet，如下列範例所示。

```azurepowershell-interactive
Get-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc
```

## <a name="clean-up-resources"></a>清除資源

如果不需要本文中建立的資源，您可以執行下列範例將其刪除。

### <a name="delete-an-association"></a>刪除關聯

若要移除關聯，請使用 [Remove-AzCustomProviderAssociation](/powershell/module/az.customproviders/remove-azcustomproviderassociation) Cmdlet。 下列範例會刪除關聯。

```azurepowershell
Remove-AzCustomProviderAssociation -Scope $id -Name Namespace.Type
```

### <a name="delete-a-custom-resource-provider"></a>刪除自訂資源提供者

若要移除自訂資源提供者，請使用 [Remove-AzCustomProvider](/powershell/module/az.customproviders/remove-azcustomprovider) Cmdlet。 下列範例會刪除自訂資源提供者。

```azurepowershell-interactive
Remove-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
```

### <a name="delete-the-resource-group"></a>刪除資源群組

> [!CAUTION]
> 下列範例會刪除指定的資源群組和其中包含的所有資源。
> 如果本文章範圍以外的資源存在於指定的資源群組中，則也會一併刪除。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 自訂資源提供者](overview.md)。