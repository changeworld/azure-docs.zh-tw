---
title: Azure PowerShell 指令碼範例 - 根據前置詞刪除容器 | Microsoft Docs
description: 根據容器名稱前置詞來刪除 Azure 儲存體 Blob 容器。
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 06/13/2017
ms.author: tamram
ms.openlocfilehash: 73c2f221d2ac0b3ceb76e3db0fdc6379a6dd330a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "80060888"
---
# <a name="delete-containers-based-on-container-name-prefix"></a>根據容器名稱前置詞來刪除容器

此指令碼會根據容器名稱中的前置詞來刪除 Azure Blob 儲存體中的容器。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.ps1 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>清除部署

執行下列命令來移除資源群組、剩餘的容器，以及所有相關資源。

```powershell
Remove-AzResourceGroup -Name containerdeletetestrg
```

## <a name="script-explanation"></a>指令碼說明

此指令碼使用下列命令，根據容器名稱前置詞來刪除容器。 下表中的每個項目都會連結至特定命令的文件。

| Command | 注意 |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | 取得資源群組中或訂用帳戶中指定的儲存體帳戶或所有儲存體帳戶。 |
| [Get-AzStorageContainer](/powershell/module/az.storage/Get-AzStorageContainer) | 列出與儲存體帳戶關聯的儲存體容器。 |
| [Remove-AzStorageContainer](/powershell/module/az.storage/Remove-AzStorageContainer) | 移除指定的儲存體容器。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

在 [Azure Blob 儲存體的 PowerShell 範例](../blobs/storage-samples-blobs-powershell.md)中，提供了其他儲存體 PowerShell 指令碼範例。
