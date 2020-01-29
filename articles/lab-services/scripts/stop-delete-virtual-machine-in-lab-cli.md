---
title: Azure CLI - 停止和刪除實驗室中的虛擬機器
description: 此 Azure CLI 指令碼會停止和刪除實驗室中的虛擬機器。
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2020
ms.author: spelluru
ms.custom: mvc
ms.openlocfilehash: 7465770eaeb35772012be2219858ccda512775d6
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2020
ms.locfileid: "76166419"
---
# <a name="use-azure-cli-to-stop-and-delete-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>使用 Azure CLI 在 Azure DevTest Labs 中停止和刪除實驗室中的虛擬機器

此 Azure CLI 指令碼會停止和刪除實驗室中的虛擬機器 (VM)。 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/stop-delete-virtual-machine-in-lab/stop-delete-virtual-machine-in-lab.sh "Stop and delete a VM in a lab")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令：

| Command | 注意 |
|---|---|
| [az lab vm stop](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-stop) | 停止實驗室中的虛擬機器 (VM)。 此作業可能需要一些時間來完成。 |
| [az lab vm delete](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-delete) | 刪除實驗室中的虛擬機器 (VM)。 此作業可能需要一些時間來完成。 |


## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure)。

您可以在 [Azure 實驗室服務 CLI 範例](../samples-cli.md)中找到其他的 Azure 實驗室服務 CLI 指令碼範例。
