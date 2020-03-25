---
title: Azure CLI 指令碼範例 - 部署受控應用程式
description: 提供 Azure CLI 指令碼範例，以將 Azure 受控應用程式定義部署至訂用帳戶。
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: 346ea59209bc2f74970e708c947f5caa158a0338
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "75648785"
---
# <a name="deploy-a-managed-application-for-service-catalog-with-azure-cli"></a>使用 Azure CLI 為服務類別目錄部署受控應用程式

此指令碼會從服務類別目錄部署受控應用程式定義。 


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[main](../../../../cli_scripts/managed-applications/create-application/create-application.sh "Create application")]


## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來部署受控應用程式。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az managedapp create](https://docs.microsoft.com/cli/azure/managedapp#az-managedapp-create) | 建立受控應用程式。 提供範本的定義識別碼和參數。 |


## <a name="next-steps"></a>後續步驟

* 如需受控應用程式的簡介，請參閱 [Azure 受控應用程式概觀](../overview.md)。
* 如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure)。
