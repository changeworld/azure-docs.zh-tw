---
title: 讓兩個虛擬網路對等互連 - Azure CLI 指令碼範例
description: 使用 Azure CLI 指令碼範例，在同一個區域中建立兩個虛擬網路，並透過 Azure 網路加以連線。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6cd6d61ba1404ca6b6de63a0151ec414b2df54e7
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87492457"
---
# <a name="peer-two-virtual-networks-with-an-azure-cli-script-sample"></a>讓兩個虛擬網路對等互連 - Azure CLI 指令碼範例

此指令碼範例會在同一個區域中建立兩個虛擬網路，並透過 Azure 網路連接這兩個虛擬網路。 執行此指令碼之後，在兩個虛擬網路之間會有對等互連。

您可以從 Azure [Cloud Shell](https://shell.azure.com/bash) 或從本機的 Azure CLI 安裝來執行指令碼。 如果是在本機使用 CLI，需要執行 2.0.28 版或更新版本，方可使用該指令碼。 若要尋找已安裝的版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 如果您在本機執行 CLI，則也需要執行 `az login` 以建立與 Azure 的連線。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.sh "Peer two networks")]

## <a name="clean-up-deployment"></a>清除部署 

執行下列命令來移除資源群組、VM 和所有相關資源：

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>指令碼說明

此指令碼使用下列命令來建立資源群組、虛擬機器和所有相關資源。 下表中的每個命令都會連結至命令特定的文件：

| Command | 注意 |
|---|---|
| [az group create](/cli/azure/group) | 建立用來存放所有資源的資源群組。 |
| [az network vnet create](/cli/azure/network/vnet) | 建立 Azure 虛擬網路和子網路。 |
| [az network vnet peering create](/cli/azure/network/vnet/peering) | 在兩個虛擬網路之間建立對等互連。  |
| [az group delete](/cli/azure/vm/extension) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure)。

可以在[虛擬網路 CLI 範例](../cli-samples.md)中找到其他的虛擬網路 CLI 指令碼範例。
