---
title: Azure HPC Cache 的 Azure CLI 必要條件
description: 您可以使用 Azure CLI 來建立或修改 Azure HPC Cache 之前的設定步驟。
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 13f45c96a830110bd0f4a2d4a2b422921d7a2e31
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654451"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>設定適用於 Azure HPC Cache 的 Azure CLI

使用 Azure CLI 來建立或管理 Azure HPC Cache 之前，請遵循下列步驟來準備您的環境。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Azure HPC Cache 需要版本2.7 或更新版本的 Azure CLI。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。

## <a name="set-default-resource-group-optional"></a>將預設資源群組設定 (選擇性) 

大部分的 hpc cache 命令都需要您傳遞快取的資源群組。 您可以使用 [az configure](/cli/azure/reference-index#az-configure)來設定預設的資源群組。

## <a name="next-steps"></a>後續步驟

安裝 Azure CLI 擴充功能並登入之後，您可以使用 Azure CLI 來建立和管理 Azure HPC Cache 系統。

* [建立 Azure HPC Cache](hpc-cache-create.md)
* [Azure CLI hpc-快取檔](/cli/azure/ext/hpc-cache/hpc-cache)
