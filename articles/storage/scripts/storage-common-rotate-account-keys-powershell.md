---
title: 使用 PowerShell 輪替儲存體帳戶存取金鑰
titleSuffix: Azure Storage
description: 建立 Azure 儲存體帳戶，然後擷取並輪替其中一個帳戶存取金鑰。
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7d8585b5d05012ab2aff2580d41fecf6423b509c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89070423"
---
# <a name="rotate-storage-account-access-keys-with-powershell"></a>使用 PowerShell 輪替儲存體帳戶存取金鑰

此指令碼會建立 Azure 儲存體帳戶，顯示新儲存體帳戶的主要存取金鑰，然後更新 (輪替) 該金鑰。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.ps1 "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>清除部署

執行下列命令來移除資源群組、儲存體帳戶和所有相關資源。

```powershell
Remove-AzResourceGroup -Name rotatekeystestrg
```

## <a name="script-explanation"></a>指令碼說明

此指令碼使用下列命令來建立儲存體帳戶，並擷取及輪替其中一個存取金鑰。 下表中的每個項目都會連結至特定命令的文件。

| Command | 注意 |
|---|---|
| [Get-AzLocation](/powershell/module/az.resources/get-azlocation) | 取得所有位置和每個位置支援的資源提供者。 |
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 建立 Azure 資源群組。 |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | 建立儲存體帳戶。 |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | 取得 Azure 儲存體帳戶的存取金鑰。 |
| [New-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) | 重新產生 Azure 儲存體帳戶的存取金鑰。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/)。

在 [Azure Blob 儲存體的 PowerShell 範例](../blobs/storage-samples-blobs-powershell.md)中，提供了其他儲存體 PowerShell 指令碼範例。
