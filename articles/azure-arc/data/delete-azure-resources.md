---
title: 從 Azure 刪除資源
description: 從 Azure 刪除資源
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 4c76b2c0109637ce34681d2fa5c8b29e1ff800a1
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92364366"
---
# <a name="delete-resources-from-azure"></a>從 Azure 刪除資源

> [!NOTE]
>  在本文中刪除資源的選項無法復原！

> [!NOTE]
>  因為目前為 Azure Arc 啟用的資料服務提供的連線模式目前是間接連接模式，所以從 Kubernetes 中刪除實例不會將其從 Azure 中移除，而且從 Azure 刪除實例不會將它從 Kubernetes 中移除。  現在刪除資源是兩個步驟的程式，未來將會改善這項處理。  接下來，Kubernetes 將會是真實的來源，而 Azure 將會更新以反映它。

在某些情況下，您可能需要手動刪除 Azure Resource Manager (ARM) 中 Azure Arc 啟用的資料服務資源。  您可以使用下列任何選項來刪除這些資源。

- [從 Azure 刪除資源](#delete-resources-from-azure)
  - [刪除整個資源群組](#delete-an-entire-resource-group)
  - [刪除資源群組中的特定資源](#delete-specific-resources-in-the-resource-group)
  - [使用 Azure CLI 刪除資源](#delete-resources-using-the-azure-cli)
    - [使用 Azure CLI 刪除 SQL 受控實例資源](#delete-sql-managed-instance-resources-using-the-azure-cli)
    - [使用 Azure CLI 刪除于 postgresql 超大規模伺服器群組資源](#delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli)
    - [使用 Azure CLI 刪除 Azure Arc 資料控制器資源](#delete-azure-arc-data-controller-resources-using-the-azure-cli)
    - [使用 Azure CLI 刪除資源群組](#delete-a-resource-group-using-the-azure-cli)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-an-entire-resource-group"></a>刪除整個資源群組
如果您已針對 Azure Arc 啟用的資料服務使用特定且專用的資源群組，而您想要刪除資源群組內的 *所有專案* ，您可以刪除資源群組，這會刪除其內部的所有專案。  

您可以執行下列動作，以刪除 Azure 入口網站中的資源群組：

- 流覽至已建立 Azure Arc 啟用的資料服務資源的 Azure 入口網站中的資源群組。
- 按一下 [ **刪除資源群組** ] 按鈕。
- 輸入資源組名以確認刪除，然後按一下 [ **刪除**]。

## <a name="delete-specific-resources-in-the-resource-group"></a>刪除資源群組中的特定資源

您可以執行下列動作，以刪除 Azure 入口網站資源群組中的特定 Azure Arc 啟用的資料服務資源：

- 流覽至已建立 Azure Arc 啟用的資料服務資源的 Azure 入口網站中的資源群組。
- 選取要刪除的所有資源。
- 按一下 [刪除] 按鈕。
- 鍵入 [是] 以確認刪除，然後按一下 [ **刪除**]。

## <a name="delete-resources-using-the-azure-cli"></a>使用 Azure CLI 刪除資源

您可以使用 Azure CLI 來刪除特定的 Azure Arc 啟用的資料服務資源。

### <a name="delete-sql-managed-instance-resources-using-the-azure-cli"></a>使用 Azure CLI 刪除 SQL 受控實例資源

若要使用 Azure CLI 從 Azure 刪除 SQL 受控實例資源，請取代下列命令中的預留位置值並加以執行。

```azurecli
az resource delete --name <sql instance name> --resource-type Microsoft.AzureData/sqlManagedInstances --resource-group <resource group name>

#Example
#az resource delete --name sql1 --resource-type Microsoft.AzureData/sqlManagedInstances --resource-group rg1
```

### <a name="delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli"></a>使用 Azure CLI 刪除于 postgresql 超大規模伺服器群組資源

若要使用 Azure CLI 從 Azure 中刪除于 postgresql 超大規模伺服器群組資源，請取代下列命令中的預留位置值並加以執行。

```azurecli
az resource delete --name <postgresql instance name> --resource-type Microsoft.AzureData/postgresInstances --resource-group <resource group name>

#Example
#az resource delete --name pg1 --resource-type Microsoft.AzureData/postgresInstances --resource-group rg1
```

### <a name="delete-azure-arc-data-controller-resources-using-the-azure-cli"></a>使用 Azure CLI 刪除 Azure Arc 資料控制器資源

> [!NOTE]
> 在刪除 Azure Arc 資料控制器之前，您應該刪除其所管理的所有資料庫實例資源。

若要使用 Azure CLI 將 Azure Arc 資料控制器從 Azure 中刪除，請取代下列命令中的預留位置值並加以執行。

```azurecli
az resource delete --name <data controller name> --resource-type Microsoft.AzureData/dataControllers --resource-group <resource group name>

#Example
#az resource delete --name dc1 --resource-type Microsoft.AzureData/dataControllers --resource-group rg1
```

### <a name="delete-a-resource-group-using-the-azure-cli"></a>使用 Azure CLI 刪除資源群組

您也可以使用 Azure CLI 來 [刪除資源群組](../../azure-resource-manager/management/delete-resource-group.md)。