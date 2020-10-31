---
title: 使用 Azure CLI 建立及設定 Azure DDoS 保護方案
description: 瞭解如何使用 Azure CLI 來建立 DDoS 保護計劃
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: yitoh
ms.openlocfilehash: 47733f4b141b0064e966d0c083fd6414405f65f9
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095538"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard-using-azure-cli"></a>快速入門：使用 Azure CLI 建立及設定 Azure DDoS 保護 Standard

使用 Azure CLI Azure DDoS 保護 Standard 入門。 

DDoS 保護計劃會定義一組跨訂用帳戶且已啟用標準 DDoS 保護的虛擬網路。 您可以為組織設定一個 DDoS 保護計劃，然後將來自多個訂用帳戶的虛擬網路連結至該相同計劃。 

在本快速入門中，您將建立 DDoS 保護計劃，並將它連結至虛擬網路。 

## <a name="prerequisites"></a>Prerequisites

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 已在本機安裝 Azure CLI 或 Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本快速入門需要有 Azure CLI 2.0.28 版或更新版本。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="create-a-ddos-protection-plan"></a>建立 DDoS 保護計劃

在 Azure 中，您可以將相關資源配置到資源群組。 您可以使用現有的資源群組，或建立一個新的群組。

若要建立資源群組，請使用 [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create&preserve-view=true)。 在此範例中，我們會將資源群組命名為 _MyResourceGroup_ ，並使用 _美國東部_ 位置：

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus
```

現在，建立名為 _MyDdosProtectionPlan_ 的 DDoS 保護計劃：

```azurecli-interactive
az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

## <a name="enable-ddos-protection-for-a-virtual-network"></a>為虛擬網路啟用 DDoS 保護

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>針對新的虛擬網路啟用 DDoS 保護

您可以在建立虛擬網路時啟用 DDoS 保護。 在此範例中，我們會將虛擬網路命名為 _MyVnet_ ： 

```azurecli-interactive
az network vnet create \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --location eastus \
    --ddos-protection true
```

已為虛擬網路啟用「標準 DDoS」時，您無法將虛擬網路移到另一個資源群組或訂用帳戶。 如果您需要移動已啟用「標準 DDoS」的虛擬網路，請先將「標準 DDoS」停用，移動虛擬網路，然後再啟用「標準 DDoS」。 移動之後，就會重設虛擬網路中所有受保護公用 IP 位址的自動調整原則閾值。

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>針對現有的虛擬網路啟用 DDoS 保護

[建立 DDoS 保護計劃](#create-a-ddos-protection-plan)時，您可以將一或多個虛擬網路與方案產生關聯。 若要新增一個以上的虛擬網路，只需列出名稱或識別碼，以空格分隔。 在此範例中，我們將新增 _MyVnet_ ：

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus

az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
    --vnet MyVnet
```

或者，您可以為指定的虛擬網路啟用 DDoS 保護：

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --vnet MyVnet \
    --ddos-protection true
```

## <a name="validate-and-test"></a>驗證和測試

首先，請查看 DDoS 保護計劃的詳細資料：

```azurecli-interactive
az network ddos-protection show \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

確認命令會傳回您 DDoS 保護計劃的正確詳細資料。

## <a name="clean-up-resources"></a>清除資源

您可以保留資源以供下一個教學課程之用。 如果不再需要，請刪除 _MyResourceGroup_ 資源群組。 當您刪除資源群組時，也會刪除 DDoS 保護計劃及其所有相關資源。 

若要刪除資源群組，請使用 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az_group_delete&preserve-view=true)：

```azurecli-interactive
az group delete \
--name MyResourceGroup 
```

更新指定的虛擬網路以停用 DDoS 保護：

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --vnet MyVnet \
    --ddos-protection false
```

如果您想要刪除 DDoS 保護計劃，您必須先中斷所有虛擬網路與其的關聯。 

## <a name="next-steps"></a>後續步驟

若要瞭解如何為您的 DDoS 保護計劃查看及設定遙測，請繼續進行教學課程。

> [!div class="nextstepaction"]
> [檢視和設定 DDoS 保護遙測](telemetry-monitoring-alerting.md)