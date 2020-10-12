---
title: 資源提供者註冊錯誤
description: 描述如何在使用 Azure Resource Manager 部署資源時解決 Azure 資源提供者註冊錯誤。
ms.topic: troubleshooting
ms.date: 02/15/2019
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 143cf03a33739f43a29af94fc2f8a336fb3aef8f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89076662"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>解決資源提供者註冊的錯誤

本文描述使用您先前未在訂用帳戶中使用的資源提供者時，可能會遇到的錯誤。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>徵狀

在部署資源時，您可能會收到下列錯誤代碼和訊息︰

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

或者，您可能會收到類似訊息，指出︰

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

錯誤訊息應可提供給您支援的位置和 API 版本建議。 您可以將您的範本變更為其中一個建議的值。 Azure 入口網站或正在使用的命令列介面會自動註冊大部分的提供者；但並非全部。 如果您未曾使用特定的資源提供者，您可能需要註冊該提供者。

否則，當您停用虛擬機器的自動關機時，可能會收到類似下列的錯誤訊息：

```
Code: AuthorizationFailed
Message: The client '<identifier>' with object id '<identifier>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope ...
```

## <a name="cause"></a>原因

您會因為下列其中一個原因而收到此錯誤︰

* 尚未向您的訂用帳戶註冊所需的資源提供者
* 此資源類型不支援 API 版本
* 此資源類型不支援位置
* 針對 VM 的自動關機，必須註冊 Microsoft.DevTestLab 資源提供者。

## <a name="solution-1---powershell"></a>解決方案 1：PowerShell

針對 PowerShell，請使用 **Get-AzResourceProvider** 來查看您的註冊狀態。

```powershell
Get-AzResourceProvider -ListAvailable
```

若要註冊提供者，請使用 **Register-AzResourceProvider** ，並提供您想要註冊的資源提供者名稱。

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Cdn
```

若要取得支援特定資源類型的位置，請使用︰

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

若要取得支援特定資源類型的 API 版本，請使用︰

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

## <a name="solution-2---azure-cli"></a>解決方案 2：Azure CLI

若要查看是否已註冊該提供者，請使用 `az provider list` 命令。

```azurecli-interactive
az provider list
```

若要註冊資源提供者，請使用 `az provider register` 命令，然後指定要註冊的*命名空間*。

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

若要查看資源類型所支援的位置和 API 版本，請使用︰

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="solution-3---azure-portal"></a>解決方案 3：Azure 入口網站

您可以看到註冊狀態，並透過入口網站註冊資源提供者命名空間。

1. 從入口網站，選取 [所有服務]****。

   ![選取 [所有服務]](./media/error-register-resource-provider/select-all-services.png)

1. 選取 **訂用帳戶** 。

   ![選取訂用帳戶](./media/error-register-resource-provider/select-subscriptions.png)

1. 從訂用帳戶的清單中，選擇要用於註冊資源提供者的訂用帳戶。

   ![選取訂用帳戶以註冊資源提供者](./media/error-register-resource-provider/select-subscription-to-register.png)

1. 對於您的訂用帳戶，選取**資源提供者**。

   ![選取資源提供者](./media/error-register-resource-provider/select-resource-provider.png)

1. 查看資源提供者的清單，並視需要選取 [註冊]**** 連結以註冊您嘗試部署的資源提供者類型。

   ![列出資源提供者](./media/error-register-resource-provider/list-resource-providers.png)
