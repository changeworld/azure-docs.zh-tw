---
title: 將使用方式資料、計量和記錄上傳至 Azure 監視器
description: 將資源清查、使用量資料、計量和記錄上傳至 Azure 監視器
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: c333b95ed762c905511ab1d4a84050d50f0e023c
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371319"
---
# <a name="upload-usage-data-metrics-and-logs-to-azure-monitor"></a>將使用方式資料、計量和記錄上傳至 Azure 監視器

您可以定期匯出使用量資訊，以便進行計費、監視計量和記錄，然後將其上傳至 Azure。 這三種資料類型的匯出和上傳也會在 Azure 中建立及更新資料控制器、SQL 受控實例和于 postgresql 超大規模伺服器群組資源。

> [!NOTE] 
> 在預覽期間，使用 Azure Arc 啟用的資料服務不會產生任何費用。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

在您可以上傳使用方式資料、計量或記錄之前，您必須：

* 安裝工具 
* [註冊 `Microsoft.AzureData` 資源提供者](#register-the-resource-provider) 
* [建立服務主體](#create-service-principal)

## <a name="install-tools"></a>安裝工具

必要的工具組括： 
* Azure CLI (az)  
* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 

請參閱 [安裝工具](./install-client-tools.md)。

## <a name="register-the-resource-provider"></a>註冊資源提供者

將計量或使用者資料上傳至 Azure 之前，您必須確定您的 Azure 訂用帳戶已 `Microsoft.AzureData` 註冊資源提供者。

若要驗證資源提供者，請執行下列命令：

```azurecli
az provider show -n Microsoft.AzureData -o table
```

如果目前未在您的訂用帳戶中註冊資源提供者，您可以進行註冊。 若要註冊，請執行下列命令。  此命令可能需要一或兩分鐘的時間來完成。

```azurecli
az provider register -n Microsoft.AzureData --wait
```

## <a name="create-service-principal"></a>建立服務主體

服務主體可用來上傳使用方式和計量資料。

請依照下列命令來建立您的計量上傳服務主體：

> [!NOTE]
> 建立服務主體需要 [Azure 中的特定許可權](/azure/active-directory/develop/howto-create-service-principal-portal#permissions-required-for-registering-an-app)。

若要建立服務主體，請更新下列範例。 `<ServicePrincipalName>`以您的服務主體名稱取代，然後執行命令：

```azurecli
az ad sp create-for-rbac --name <ServicePrincipalName>
``` 

如果您先前已建立服務主體，而且只需要取得目前的認證，請執行下列命令來重設認證。

```azurecli
az ad sp credential reset --name <ServicePrincipalName>
```

例如，若要建立名為的服務主體 `azure-arc-metrics` ，請執行下列命令

```
az ad sp create-for-rbac --name azure-arc-metrics
```

範例輸出︰

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

將 `appId` 、 `password` 和值儲存 `tenant` 在環境變數中，以便稍後使用。 

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_CLIENT_ID=<appId>
SET SPN_CLIENT_SECRET=<password>
SET SPN_TENANT_ID=<tenant>
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_CLIENT_ID='<appId>'
export SPN_CLIENT_SECRET='<password>'
export SPN_TENANT_ID='<tenant>'
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:SPN_CLIENT_ID="<appId>"
$Env:SPN_CLIENT_SECRET="<password>"
$Env:SPN_TENANT_ID="<tenant>"
```

::: zone-end

在您建立服務主體之後，請將服務主體指派給適當的角色。 

## <a name="assign-roles-to-the-service-principal"></a>將角色指派給服務主體

執行此命令，將服務主體指派給 `Monitoring Metrics Publisher` 您資料庫實例資源所在之訂用帳戶上的角色：

::: zone pivot="client-operating-system-windows-command"

> [!NOTE]
> 在 Windows 環境中執行時，您必須使用雙引號作為角色名稱。

```azurecli
az role assignment create --assignee <appId> --role "Monitoring Metrics Publisher" --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role "Contributor" --scope subscriptions/<Subscription ID>
```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```azurecli
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role 'Contributor' --scope subscriptions/<Subscription ID>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```powershell
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role 'Contributor' --scope subscriptions/<Subscription ID>
```

::: zone-end

範例輸出︰

```output
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

將服務主體指派給適當的角色之後，您就可以繼續上傳計量或使用者資料。 

## <a name="upload-logs-metrics-or-user-data"></a>上傳記錄、計量或使用者資料

上傳記錄、計量或使用者資料的特定步驟會因您所上傳的資訊類型而異。 

[將記錄上傳至 Azure 監視器](upload-logs.md)

[將計量上傳至 Azure 監視器](upload-metrics.md)

[將使用方式資料上傳至 Azure 監視器](upload-usage-data.md)

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>匯出和上傳使用方式、計量的一般指引

在 Azure Arc 啟用的資料服務上建立、讀取、更新和刪除 (CRUD) 作業，以供計費和監視之用。 有背景服務會監視這些 CRUD 作業，並適當地計算耗用量。 使用量或耗用量的實際計算會依排程進行，並在背景中完成。 

在預覽期間，此程式會在夜間進行。 一般的指導方針是每天上傳一次使用。 當使用資訊在同一個24小時期間內匯出和上傳多次時，只會在 Azure 入口網站中更新資源清查，但不會更新資源使用量。

針對上傳計量，Azure 監視器只會接受過去30分鐘的資料， ([深入瞭解](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)) 。 上傳計量的指引是在建立匯出檔案之後，立即上傳計量，讓您可以在 Azure 入口網站中查看整個資料集。 比方說，如果您在下午2:00 匯出計量，並在下午2:50 執行上傳命令。 由於 Azure 監視器只接受過去30分鐘的資料，因此您可能不會在入口網站中看到任何資料。 

## <a name="next-steps"></a>後續步驟

[深入瞭解服務主體](/powershell/azure/azurerm/create-azure-service-principal-azureps#what-is-a-service-principal)

[將帳單資料上傳至 Azure，並在 Azure 入口網站中加以查看](view-billing-data-in-azure.md)

[在 Azure 入口網站中查看 Azure Arc 的資料控制器資源](view-data-controller-in-azure-portal.md)