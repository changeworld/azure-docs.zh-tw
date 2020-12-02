---
title: 快速入門：使用 Azure PowerShell 建立 Azure 專用 HSM
description: 使用 Azure PowerShell 建立 Azure 專用 HSM
services: dedicated-hsm
author: msmbaldwin
ms.author: mbaldwin
ms.topic: quickstart
ms.service: key-vault
ms.devlang: azurepowershell
ms.date: 11/13/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: eebfb257d0324cf2771bd3af979ddbebb8429fb7
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2020
ms.locfileid: "94905538"
---
# <a name="quickstart-create-an-azure-dedicated-hsm-with-azure-powershell"></a>快速入門：使用 Azure PowerShell 建立 Azure 專用 HSM

本文說明如何使用 [Az.DedicatedHsm](/powershell/module/az.dedicatedhsm) PowerShell 模組來建立 Azure 專用 HSM。

## <a name="requirements"></a>需求

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

[!INCLUDE [azure-powershell-requirements-no-header.md](../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > **Az.DedicatedHsm** PowerShell 模組處於預覽狀態，因此您必須使用 `Install-Module` Cmdlet 將其分開安裝。 此 PowerShell 模組正式推出後，便會成為未來 Az PowerShell 模組版本的一部分，且預設可從 Azure Cloud Shell 內使用。

  ```azurepowershell-interactive
  Install-Module -Name Az.DedicatedHsm
  ```

* 如果您有多個 Azure 訂用帳戶，請選擇資源計費的適當訂用帳戶。 使用 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) Cmdlet 來選取特定的訂用帳戶。

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Cmdlet 來建立 [Azure 資源群組](../azure-resource-manager/management/overview.md)。 資源群組是一個邏輯容器，Azure 資源會在其中以群組方式部署及管理。

下列範例會在指定的位置使用指定的名稱建立資源群組。

```azurepowershell-interactive
New-AzResourceGroup -Name myRG -Location westus
```

## <a name="create-a-dedicated-hsm"></a>建立專用的 HSM

若要建立專用 HSM，請使用 [New-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/new-azdedicatedhsm) Cmdlet。 下列範例會在指定的訂用帳戶中建立專用 HSM。

```azurepowershell-interactive
$Params = @{
  Name  = 'MyHSM'
  ResourceGroupName = 'myRG'
  Location = 'westus'
  Sku = 'SafeNet Luna Network HSM A790'
  StampId = 'stamp1'
  SubnetId = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myhsm-vnet/subnets/hsmsubnet'
  NetworkInterface = @{PrivateIPAddress = '10.2.1.120'}
}
New-AzDedicatedHsm @Params
```

```Output
Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="get-a-dedicated-hsm"></a>取得專用 HSM

若要擷取現有專用 HSM 的相關資訊，請使用 [Get-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/get-azdedicatedhsm) Cmdlet。 下列範例會取得指定的專用 HSM。

```azurepowershell-interactive
Get-AzDedicatedHsm -Name MyHSM -ResourceGroupName myRG
```

```Output
Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="update-a-dedicated-hsm"></a>更新專用 HSM

若要更新專用 HSM，請使用 [Update-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/update-azdedicatedhsm) Cmdlet。 下列範例會更新指定訂用帳戶中的專用 HSM。

```azurepowershell-interactive
Update-AzDedicatedHsm -Name MyHSM -ResourceGroupName myRG -Tag @{'key1' = '1'; 'key2' = 2; 'key3' = 3}
```

```Output
PS C:\>Update-AzDedicatedHsm -Name  hsm-n7wfxi -ResourceGroupName dedicatedhsm-rg-n359cz -Tag @{'key1' = '1';
'key2' = 2; 'key3' = 3}

Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="clean-up-resources"></a>清除資源

如果不需要本文中建立的資源，您可以執行下列範例將其刪除。

### <a name="remove-a-dedicated-hsm"></a>移除專用 HSM

若要移除專用 HSM，請使用 [Remove-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/remove-azdedicatedhsm) Cmdlet。 下列範例會刪除指定的專用 HSM。

```azurepowershell-interactive
Remove-AzDedicatedHsm -Name hsm-7t2xaf -ResourceGroupName lucas-manual-test
```

### <a name="delete-the-resource-group"></a>刪除資源群組

> [!CAUTION]
> 下列範例會刪除指定的資源群組和其中包含的所有資源。
> 如果本文章範圍以外的資源存在於指定的資源群組中，則也會一併刪除。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myRG
```

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 專用 HSM](overview.md)。
