---
title: 使用 PowerShell 部署 Azure SSIS 集成運行時
description: 這個 PowerShell 指令碼會建立可在雲端中執行 SSIS 封裝的 Azure-SSIS 整合執行階段。
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.author: sawinark
author: swinarko
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 09/12/2017
ms.openlocfilehash: c6d9a9299fa25013a440ee6ac45f5eae407225b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929811"
---
# <a name="powershell-script---deploy-azure-ssis-integration-runtime"></a>PowerShell 指令碼 - 部署 Azure-SSIS 整合執行階段

這個範例 PowerShell 指令碼會建立可在 Azure 中執行 SSIS 封裝的 Azure-SSIS 整合執行階段。  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/data-factory/deploy-azure-ssis-integration-runtime/deploy-azure-ssis-integration-runtime.ps1 "Deploy Azure-SSIS Integration Runtime")]

## <a name="clean-up-deployment"></a>清除部署

執行範例指令碼之後，您可以使用下列命令以移除資源群組及與其相關聯的所有資源：

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
若要從資源群組移除資料處理站，請執行下列命令： 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令：

| Command | 注意 |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [集-阿茲達工廠V2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | 建立資料處理站。 |
| [設置-阿茲達工廠V2集成運行時](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) | 建立可在雲端中執行 SSIS 封裝的 Azure-SSIS 整合執行階段 |
| [啟動-阿茲達工廠V2集成執行時間](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) | 啟動 Azure-SSIS 整合執行階段。 |
| [獲取阿茲達工廠V2集成執行時間](/powershell/module/az.datafactory/get-Azdatafactoryv2integrationruntime) | 取得 Azure-SSIS 整合執行階段的相關資訊。 |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 刪除資源群組，包括所有的巢狀資源。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/)。

您可以在 [Azure Data Factory PowerShell 範例](../samples-powershell.md)中，找到其他 Azure Data Factory PowerShell 指令碼範例。
