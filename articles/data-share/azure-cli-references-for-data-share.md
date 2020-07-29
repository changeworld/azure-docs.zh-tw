---
title: Azure Data Share 的 Azure CLI 參考
description: Azure Data Share 的 Azure CLI 參考登陸頁面
services: data-share
author: dbradish-microsoft
manager: barbkess
ms.service: data-share
ms.devlang: azurecli
ms.topic: reference
ms.date: 05/27/2020
ms.author: dbradish
ms.openlocfilehash: 404022b13f44174e4b647f0430a58fac5eeb81df
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87298456"
---
# <a name="azure-cli-for-azure-data-share"></a>Azure Data Share 的 Azure CLI

Azure 命令列介面 ([Azure CLI](/cli/azure/what-is-azure-cli)) 是用來建立和管理 Azure 資源的一組命令。  其適用於許多 Azure 服務，包括 Azure Data Share。  資料共用擁有超過 65 個不同的命令！  您能夠使用這些命令從命令列讓服務有效運作。

## <a name="references-for-data-share"></a>Azure Data Share 參考

Azure Data Share 的所有 Azure CLI 命令目前都是 Azure CLI 的擴充功能。  擴充功能可讓您存取實驗性和發行前版本命令。  如需擴充功能參考的詳細資訊，請參閱[使用擴充功能搭配 Azure CLI](/cli/azure/azure-cli-extensions-overview)。

|Azure CLI 參考 |描述
|-|-|-|
| [az datashare](/cli/azure/ext/datashare/datashare) | 所有管理 Azure Data Share 的命令。
| [az datashare account](/cli/azure/ext/datashare/datashare/account) | 管理 Azure Data Share 帳戶的命令。
| [az datashare consumer](/cli/azure/ext/datashare/datashare/consumer) | 取用者用來管理 Azure Data Share 的命令。
| [az datashare dataset](/cli/azure/ext/datashare/datashare/dataset) | 提供者用來管理 Azure Data Share 資料集的命令。
| [az datashare invitation](/cli/azure/ext/datashare/datashare/invitation) | 取用者用來管理 Azure Data Share 邀請的命令。
| [az datashare provider-share-subscription](/cli/azure/ext/datashare/datashare/provider-share-subscription) | 提供者用來管理 Azure Data Share 訂用帳戶的命令。
| [az datashare synchronization](/cli/azure/ext/datashare/datashare/synchronization)  | 管理 Azure Data Share 同步處理的命令。
| [az datashare synchronization-setting](/cli/azure/ext/datashare/datashare/synchronization-setting)  | 提供者用來管理 Azure Data Share 同步處理設定的命令。

## <a name="reference-examples"></a>參考範例

每個 Azure CLI 參考都會隨附範例。 雖然您也可以透過 Azure 入口網站完成這些工作，但是使用 Azure CLI 需要單一命令列。  以下是幾個程式碼區塊，讓您了解使用 Azure CLI 有多麼容易。

若要使用 Azure Data Share，首先需要一個資源群組。  使用 Azure CLI 可輕易建立及管理 Azure 資源群組。  

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

* 在 Azure CLI 文件中，探索其他[核心](/cli/azure/reference-index)和[擴充功能](/cli/azure/azure-cli-extensions-list)參考。
