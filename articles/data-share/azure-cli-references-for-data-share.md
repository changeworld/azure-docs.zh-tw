---
title: Azure 資料共用的 Azure CLI 參考
description: Azure 資料共用的 Azure CLI 參考登陸頁面
services: data-share
author: dbradish-microsoft
manager: barbkess
ms.service: data-share
ms.devlang: azurecli
ms.topic: reference
ms.date: 05/27/2020
ms.author: dbradish
ms.openlocfilehash: 404022b13f44174e4b647f0430a58fac5eeb81df
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84302640"
---
# <a name="azure-cli-for-azure-data-share"></a>適用于 Azure 資料共用的 Azure CLI

Azure 命令列介面 ([Azure CLI](/cli/azure/what-is-azure-cli)) 是用來建立和管理 Azure 資源的一組命令。  這適用于許多 Azure 服務，包括 Azure 資料共用。  資料共用有超過65個不同的命令！  這些命令可讓您從命令列使用服務有效率地運作。

## <a name="references-for-data-share"></a>資料共用的參考

Azure 資料共用的所有 Azure CLI 命令目前都是 Azure CLI 的延伸模組。  擴充功能可讓您存取實驗性和發行前版本命令。  如需擴充功能參考的詳細資訊，請參閱[使用擴充功能搭配 Azure CLI](/cli/azure/azure-cli-extensions-overview)。

|Azure CLI 參考 |Description
|-|-|-|
| [az datashare](/cli/azure/ext/datashare/datashare) | 所有用來管理 Azure 資料共用的命令。
| [az datashare account](/cli/azure/ext/datashare/datashare/account) | 用來管理 Azure 資料共用帳戶的命令。
| [az datashare 取用者](/cli/azure/ext/datashare/datashare/consumer) | 可供取用者用來管理 Azure 資料共用的命令。
| [az datashare dataset](/cli/azure/ext/datashare/datashare/dataset) | 可供提供者用來管理 Azure 資料共用資料集的命令。
| [az datashare 邀請](/cli/azure/ext/datashare/datashare/invitation) | 可供取用者用來管理 Azure 資料共用邀請的命令。
| [az datashare provider-共用-訂用帳戶](/cli/azure/ext/datashare/datashare/provider-share-subscription) | 可供提供者用來管理 Azure 資料共用訂用帳戶的命令。
| [az datashare 同步處理](/cli/azure/ext/datashare/datashare/synchronization)  | 用來管理 Azure 資料共用同步處理的命令。
| [az datashare 同步處理-設定](/cli/azure/ext/datashare/datashare/synchronization-setting)  | 提供者用來管理 Azure 資料共用同步處理設定的命令。

## <a name="reference-examples"></a>參考範例

每個 Azure CLI 參考都會隨附範例。 雖然您也可以透過 Azure 入口網站完成這些工作，但是使用 Azure CLI 需要單一命令列。  以下是幾個程式碼區塊，讓您了解使用 Azure CLI 有多麼容易。

若要使用 Azure 資料共用，您需要先有資源群組。  使用 Azure CLI 可輕易建立及管理 Azure 資源群組。  

```azurecli
#create a resource group
az group create -location westus -name MyResourceGroup
```

```azurecli
#get a list of resource groups for a subscription
az group list --subscription MySubscription --output table
```

建立資料共用帳戶的方式很簡單。

```azurecli
#create a data share account
az datashare account create --location "West US 2" --tags tag1=Red tag2=White --name MyAccount --resource-group MyResourceGroup
```

## <a name="see-also"></a>另請參閱

* [開始使用 Azure CLI](/cli/azure/get-started-with-azure-cli)，以了解安裝和登入。

* 探索 Azure CLI 檔中的其他[核心](/cli/azure/reference-index)和[延伸](/cli/azure/azure-cli-extensions-list)模組參考。
