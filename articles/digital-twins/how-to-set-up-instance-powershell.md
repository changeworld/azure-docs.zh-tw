---
title: 設定 (PowerShell) 的實例和驗證
titleSuffix: Azure Digital Twins
description: 瞭解如何使用 Azure PowerShell 設定 Azure 數位 Twins 服務的實例
author: baanders
ms.author: baanders
ms.date: 12/16/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 65495da13bc6c9ed91c1ecbd587c16c949136d73
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98040680"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-powershell"></a>設定 Azure 數位 Twins 實例和驗證 (PowerShell) 

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

本文涵蓋 **設定新 Azure 數位 Twins 實例** 的步驟，包括建立實例和設定驗證。 完成本文之後，您將會有可開始進行程式設計的 Azure 數位 Twins 實例。

這一版的文章會使用 Azure PowerShell 手動逐一完成這些步驟。

* 若要使用 Azure 入口網站手動完成這些步驟，請參閱本文的入口網站版本： [*如何：設定實例和驗證 (入口網站)*](how-to-set-up-instance-portal.md)。
* 若要使用部署腳本範例來執行自動安裝，請參閱本文的腳本版本： [*如何：設定實例和驗證 (腳本)*](how-to-set-up-instance-scripted.md)。

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="prepare-your-environment"></a>準備您的環境

1. 如果您選擇在本機使用 Azure PowerShell：
   1. [安裝 Az PowerShell 模組](/powershell/azure/install-az-ps)。
   1. 使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) Cmdlet 連線至 Azure 帳戶。
1. 如果您選擇使用 Azure Cloud Shell：
   1. 請參閱 [Azure Cloud Shell 概觀](../cloud-shell/overview.md) 以取得詳細資訊。
   1. 在 Cloud Shell 圖示列中，確認您的 Cloud Shell 設定為執行 PowerShell 版本。

      :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="顯示所選 PowerShell 版本的 Cloud Shell 視窗":::

1. 如果您有多個 Azure 訂用帳戶，請選擇資源計費的適當訂用帳戶。 使用 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) Cmdlet 來選取特定的訂用帳戶。

   ```azurepowershell-interactive
   Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
   ```

1. 如果這是您第一次使用 Azure 數位 Twins 搭配此訂用帳戶，您必須註冊 **DigitalTwins** 資源提供者。

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.DigitalTwins
   ```

> [!IMPORTANT]
> **DigitalTwins** PowerShell 模組目前為預覽狀態，您必須使用指令程式個別進行安裝 `Install-Module` 。 此 PowerShell 模組正式推出後，便會成為未來 Az PowerShell 模組版本的一部分，且預設可從 Azure Cloud Shell 內使用。

```azurepowershell-interactive
Install-Module -Name Az.DigitalTwins
```

## <a name="create-the-azure-digital-twins-instance"></a>建立 Azure 數位 Twins 實例

在本節中，您將使用 Azure PowerShell **建立 Azure 數位 Twins 的新實例** 。
您將需要提供：

* [Azure 資源群組](../azure-resource-manager/management/overview.md)。 如果您還沒有現有的資源群組，您可以使用 [>new-azresourcegroup](/powershell/module/az.resources/new-azresourcegroup) Cmdlet 建立一個。

  ```azurepowershell-interactive
  New-AzResourceGroup -Name <name-for-your-resource-group> -Location <region>
  ```

* 部署的區域。 若要查看哪些區域支援 Azure 數位 Twins，請造訪 [*依區域提供的 azure 產品*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)。
* 實例的名稱。 新實例的名稱在訂用帳戶的區域內必須是唯一的。 如果您的訂用帳戶在已使用指定名稱的區域中有另一個 Azure 數位 Twins 實例，系統會要求您挑選不同的名稱。

您可以使用下列範例中的值來建立實例：

```azurepowershell-interactive
New-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> -Location <region>
```

### <a name="verify-success-and-collect-important-values"></a>驗證成功並收集重要值

如果成功建立實例，結果看起來會像下列輸出，其中包含您所建立之資源的相關資訊：

```Output
Location   Name                                         Type
--------   ----                                         ----
<region>   <name-for-your-Azure-Digital-Twins-instance> Microsoft.DigitalTwins/digitalTwinsInstances
```

> [!TIP]
> 您可以隨時執行和傳送至，以查看這些屬性，以及實例的所有屬性 `Get-AzDigitalTwinsInstance` `Select-Object -Property *` 。

```azurepowershell-interactive
Get-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> |
  Select-Object -Property *
```

請注意 Azure 數位 Twins 實例的 **主機名稱**、 **名稱** 和 **ResourceGroup**。 當您繼續使用 Azure 數位 Twins 實例、設定驗證以及相關的 Azure 資源時，這些是您可能需要的重要值。 如果其他使用者將針對該實例進行程式設計，您應該與這些使用者共用這些值。

您現在已準備好開始使用 Azure 數位 Twins 實例。 接下來，您將授與適當的 Azure 使用者權限來管理它。

## <a name="set-up-user-access-permissions"></a>設定使用者存取權限

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

使用 [>get-azaduser](/powershell/module/az.resources/get-azaduser)指令程式，判斷應指派角色之使用者的 Azure AD 帳戶的 **ObjectId** 。

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName <Azure-AD-user-principal-name-of-user-to-assign>
```

使用下列命令來指派角色。 它必須由具有 Azure 訂用帳戶中的 [足夠許可權](#prerequisites-permission-requirements) 的使用者執行。 此命令會要求您針對應指派角色的使用者 Azure AD 帳戶傳入 **ObjectId** 。 您也需要使用您稍早選擇的相同訂用帳戶識別碼、資源組名和 Azure 數位 Twins 實例名稱。

```azurepowershell-interactive
$Params = @{
  ObjectId = '<Azure-AD-user-object-id-of-user-to-assign>'
  RoleDefinitionName = 'Azure Digital Twins Data Owner'
  Scope = '/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<name-for-your-Azure-Digital-Twins-instance>'
}
New-AzRoleAssignment @Params
```

此命令的結果會輸出已建立之角色指派的相關資訊。

### <a name="verify-success"></a>確認是否成功

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

您現在已準備好開始使用 Azure 數位 Twins 實例，並已獲指派管理該實例的許可權。

## <a name="next-steps"></a>下一步

瞭解如何使用驗證碼將用戶端應用程式連接到您的實例：
* [*How to：撰寫應用程式驗證碼*](how-to-authenticate-client.md)
