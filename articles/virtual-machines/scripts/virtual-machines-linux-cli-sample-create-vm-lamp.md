---
title: Azure CLI 指令碼範例 - 在負載平衡虛擬機器擴展集中部署 LAMP 堆疊
description: 使用自訂指令碼擴充功能，在 Azure 上的負載平衡虛擬機器擴展集中部署 LAMP 堆疊。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
ms.author: cynthn
tags: azure-service-management
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.custom: mvc, devx-track-azurecli
ms.date: 04/05/2017
ms.openlocfilehash: e71b2f940c637a8b1375be71f9b6ca95fd76628e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87501860"
---
# <a name="deploy-the-lamp-stack-in-a-load-balanced-virtual-machine-scale-set"></a>在負載平衡虛擬機器擴展集中部署 LAMP 堆疊

這個範例會建立虛擬機器擴展集，並且套用執行自訂指令碼的擴充功能，在擴展集的每部虛擬機器上部署 LAMP 堆疊。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/build-stack.sh "Create virtual machine scale set with LAMP stack")]

## <a name="connect"></a>連線

使用下列程式碼，以了解如何連線至您的 VM 和您的擴展集。

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/how-to-access.sh "Access the virtual machine scale set")]

## <a name="clean-up-deployment"></a>清除部署 

執行下列命令來移除資源群組、擴展集和 VM，以及所有相關資源。

```azurecli-interactive 
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>指令碼說明

此指令碼使用下列命令來建立資源群組、虛擬機器、可用性設定組、負載平衡器和所有相關資源。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az group create](/cli/azure/group) | 建立用來存放所有資源的資源群組。 |
| [az vmss create](/cli/azure/vmss) | 建立虛擬機器擴展集 |
| [az network lb rule create](/cli/azure/network/lb/rule) | 新增負載平衡端點 |
| [az vmss extension set](/cli/azure/vmss/extension) | 建立擴充功能，該擴充功能在 VM 部署上執行自訂指令碼 |
| [az vmss update-instances](/cli/azure/vmss) | 在 VM 執行個體上執行自訂指令碼，該執行個體是在擴充功能套用至擴展集之前部署的。 |
| [az vmss scale](/cli/azure/vmss) | 藉由新增更多 VM 執行個體以擴大擴展集。 當這些執行個體部署時，自訂指令碼會在上面執行。 |
| [az network public-ip list](/cli/azure/network/public-ip) | 取得範例建立之 VM 的 IP 位址。 |
| [az network lb show](/cli/azure/network/lb) | 取得由負載平衡器使用的前端和後端連接埠。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

您可以在 [Azure Linux VM 文件](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中找到其他的虛擬機器 CLI 指令碼範例。
