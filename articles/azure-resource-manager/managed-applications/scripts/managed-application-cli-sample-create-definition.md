---
title: 建立受控應用程式定義 - Azure CLI
description: 提供 Azure CLI 指令碼範例，以在訂用帳戶中建立受控應用程式定義。
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7feb00b581732cdc1956c4ac23af571180ff09e0
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87497829"
---
# <a name="create-a-managed-application-definition-with-azure-cli"></a>使用 Azure CLI 建立受控應用程式定義

此指令碼會將受控應用程式定義發佈到服務類別目錄。 


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[main](../../../../cli_scripts/managed-applications/create-definition/create-definition.sh "Create definition")]


## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來建立受控應用程式定義。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az managedapp definition create](/cli/azure/managedapp/definition#az-managedapp-definition-create) | 建立受控應用程式定義。 提供內含所需檔案的套件。 |


## <a name="next-steps"></a>後續步驟

* 如需受控應用程式的簡介，請參閱 [Azure 受控應用程式概觀](../overview.md)。
* 如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。
