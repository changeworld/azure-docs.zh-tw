---
title: 如何使用 Azure PowerShell 部署 Azure 春天雲端
description: 如何使用 Azure PowerShell 部署 Azure 春天雲端
author: bmitchell287
ms.author: brendm
ms.topic: conceptual
ms.service: spring-cloud
ms.devlang: azurepowershell
ms.date: 11/16/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3cb320a37818084f2fbcad22a3cc992655b19c3d
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95549890"
---
# <a name="deploy-azure-spring-cloud-with-azure-powershell"></a>使用 Azure PowerShell 部署 Azure 春天雲端

本文說明如何使用 [Az. SpringCloud](/powershell/module/Az.SpringCloud) PowerShell 模組來建立 Azure 春季 Cloud 的實例。

## <a name="requirements"></a>需求

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

[!INCLUDE [azure-powershell-requirements-no-header.md](../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > **SpringCloud** PowerShell 模組目前為預覽狀態，您必須使用指令程式個別進行安裝 `Install-Module` 。 此 PowerShell 模組正式推出後，便會成為未來 Az PowerShell 模組版本的一部分，且預設可從 Azure Cloud Shell 內使用。

  ```azurepowershell-interactive
  Install-Module -Name Az.SpringCloud
  ```

* 如果您有多個 Azure 訂用帳戶，請選擇資源計費的適當訂用帳戶。 使用 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) Cmdlet 來選取特定的訂用帳戶。

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Cmdlet 來建立 [Azure 資源群組](../azure-resource-manager/management/overview.md)。 資源群組是一個邏輯容器，Azure 資源會在其中以群組方式部署及管理。

下列範例會在指定的位置使用指定的名稱建立資源群組。

```azurepowershell-interactive
New-AzResourceGroup -Name <resource group name> -Location eastus
```

## <a name="provision-a-new-instance-of-azure-spring-cloud"></a>布建 Azure 春季雲端的新實例

若要建立新的 Azure 春季 Cloud 實例，請使用 [AzSpringCloud](/powershell/module/az.springcloud/new-azspringcloud) Cmdlet。 下列範例會在先前建立的資源群組中，建立具有指定名稱的 Azure 春季雲端服務。

```azurepowershell-interactive
New-AzSpringCloud -ResourceGroupName <resource group name> -name <service instance name> -Location eastus
```

## <a name="create-a-new-azure-spring-cloud-app"></a>建立新的 Azure 春季 Cloud 應用程式

若要建立新的應用程式，請使用 [AzSpringCloudApp](/powershell/module/az.springcloud/new-azspringcloudapp) Cmdlet。 下列範例會建立名為的 Azure 春季 Cloud 應用程式 `gateway` 。

```azurepowershell-interactive
New-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

## <a name="create-a-new-azure-spring-cloud-deployment"></a>建立新的 Azure 春季雲端部署

若要建立新的部署，請使用 [AzSpringCloudAppDeployment](/powershell/module/az.springcloud/new-azspringcloudappdeployment) Cmdlet。 下列範例會為應用程式建立名為的 Azure 春季雲端部署 `default` `gateway` 。

```azurepowershell-interactive
New-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -Name <service instance name> -AppName gateway -DeploymentName default
```

## <a name="get-an-azure-spring-cloud-service"></a>取得 Azure 春季雲端服務

若要取得 Azure 春季雲端服務和其屬性，請使用 [AzSpringCloud](/powershell/module/az.springcloud/get-azspringcloud) Cmdlet。 下列範例會抓取指定的 Azure 春季雲端服務的相關資訊。

```azurepowershell-interactive
Get-AzSpringCloud -ResourceGroupName <resource group name> -ServiceName <service instance name>
```

## <a name="get-an-azure-spring-cloud-app"></a>取得 Azure 春季雲端應用程式

若要取得 Azure 春天雲端應用程式和其屬性，請使用 [AzSpringCloudApp](/powershell/module/az.springcloud/get-azspringcloudapp) Cmdlet。 下列範例會抓取 `gateway` 春季 Cloud app 的相關資訊。

```azurepowershell-interactive
Get-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

## <a name="get-an-azure-spring-cloud-deployment"></a>取得 Azure 春季雲端部署

若要取得 Azure 春季雲端部署及其屬性，請使用 [AzSpringCloudAppDeployment](/powershell/module/az.springcloud/get-azspringcloudappdeployment) Cmdlet。 下列範例會抓取 `default` 春季雲端部署的相關資訊。

```azurepowershell-interactive
Get-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway -DeploymentName default
```

## <a name="clean-up-resources"></a>清除資源

如果不需要本文中建立的資源，您可以執行下列範例將其刪除。

### <a name="delete-an-azure-spring-cloud-deployment"></a>刪除 Azure 春季雲端部署

若要移除 Azure 春季雲端部署，請使用 [AzSpringCloudAppDeployment](/powershell/module/az.springcloud/remove-azspringcloudappdeployment) Cmdlet。 下列範例會 `default` 針對指定的服務和應用程式，刪除名為的 Azure 春季 Cloud 應用程式部署。

```azurepowershell-interactive
Remove-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway -DeploymentName default
```

### <a name="delete-an-azure-spring-cloud-app"></a>刪除 Azure 春季 Cloud 應用程式

若要移除春季 Cloud 應用程式，請使用 [AzSpringCloudApp](/powershell/module/Az.SpringCloud/remove-azspringcloudapp) Cmdlet。 下列範例會刪除 `gateway` 指定服務和資源群組中的應用程式。

```azurepowershell
Remove-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

### <a name="delete-an-azure-spring-cloud-service"></a>刪除 Azure 春季雲端服務

若要移除 Azure 春季雲端服務，請使用 [AzSpringCloud](/powershell/module/Az.SpringCloud/remove-azspringcloud) Cmdlet。 下列範例會刪除指定的 Azure 春季雲端服務。

```azurepowershell
Remove-AzSpringCloud -ResourceGroupName <resource group name> -ServiceName <service instance name>
```

### <a name="delete-the-resource-group"></a>刪除資源群組

> [!CAUTION]
> 下列範例會刪除指定的資源群組和其中包含的所有資源。
> 如果本文章範圍以外的資源存在於指定的資源群組中，則也會一併刪除。

```azurepowershell-interactive
Remove-AzResourceGroup -Name <resource group name>
```

## <a name="next-steps"></a>後續步驟

[Azure 春季雲端開發人員資源](spring-cloud-resources.md)。
