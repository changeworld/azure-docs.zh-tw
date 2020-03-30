---
author: lucygoldbergmicrosoft
ms.service: data-explorer
ms.topic: include
ms.date: 11/28/2019
ms.author: lugoldbe
ms.openlocfilehash: 9598724db630db7fc48545b58f2b0c1cad464aa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74667860"
---
## <a name="clean-up-resources"></a>清除資源

不再需要 Azure 資源時，可藉由刪除資源群組來清除您所部署的資源。 

### <a name="clean-up-resources-using-the-azure-portal"></a>使用 Azure 門戶清理資源

按照[清理資源](../articles/data-explorer/create-cluster-database-portal.md#clean-up-resources)的步驟刪除 Azure 門戶中的資源。

### <a name="clean-up-resources-using-powershell"></a>使用 PowerShell 清除資源

如果雲外殼仍然打開，則無需複製/運行第一行（讀取主機）。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```