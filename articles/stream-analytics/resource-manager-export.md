---
title: 將 Azure 串流分析作業匯出 Azure Resource Manager 範本
description: 本文說明如何匯出 Azure 串流分析作業的 Azure Resource Manager 範本。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 52ea7b45d0dcdb3ae16b8212557ba6ab3344ff15
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78968911"
---
# <a name="export-an-azure-stream-analytics-job-azure-resource-manager-template"></a>將 Azure 串流分析作業匯出 Azure Resource Manager 範本

[Azure Resource Manager 範本](../azure-resource-manager/templates/overview.md)可讓您將基礎結構當做程式碼來執行。 範本是 JavaScript 物件標記法（JSON）檔案，可定義資源的基礎結構和設定。 您可以指定要部署的資源，以及這些資源的屬性。

您可以藉由匯出 Azure Resource Manager 範本來重新部署 Azure 串流分析作業。

## <a name="open-a-job-in-vs-code"></a>在 VS Code 中開啟作業

在您可以匯出範本之前，您必須先在 Visual Studio Code 中開啟現有的串流分析作業。 

若要將作業匯出至本機專案，請在 Azure 入口網站的**串流分析 Explorer**中找出您想要匯出的作業。 在 [**查詢**] 頁面中，選取 [**在 Visual Studio 中開啟**]。 然後選取 [ **Visual Studio Code**]。

![在 Visual Studio Code 中開啟串流分析作業](./media/resource-manager-export/open-job-vs-code.png)

如需使用 Visual Studio Code 管理串流分析作業的詳細資訊，請參閱[Visual Studio Code 快速入門](quick-create-vs-code.md)。

## <a name="compile-the-script"></a>編譯指令碼 

下一個步驟是將作業腳本編譯為 Azure Resource Manager 範本。 在您編譯腳本之前，請確定您的作業至少已設定一個輸入和一個輸出。 如果未設定任何輸入或輸出，您必須先設定輸入和輸出。

1. 在 Visual Studio Code 中，流覽至您作業的*script.asaql*檔案。

   ![Visual Studio Code 中的轉換 script.asaql 檔案](./media/resource-manager-export/transformation-asaql.png)

1. 以滑鼠右鍵按一下*script.asaql*檔案，然後從功能表中選取 [ **ASA：編譯腳本**]。

1. 請注意，[**部署**] 資料夾會出現在 [串流分析作業] 工作區中。

1. 探索*JobTemplate* ，這是用來部署的 Azure 資源管理範本。

## <a name="complete-the-parameters-file"></a>完成參數檔案

接下來，完成 Azure 資源管理範本參數檔案。

1. 開啟位於 Visual Studio Code 中串流分析作業] 工作區的 [**部署**] 資料夾中的*JobTemplate* 。

1. 請注意，輸入和輸出索引鍵都是 null。 將 null 值取代為輸入和輸出資源的實際存取金鑰。

1. 儲存參數檔案。

## <a name="deploy-using-templates"></a>使用範本部署

您已準備好使用您在上一節中產生的 Azure Resource Manager 範本來部署 Azure 串流分析作業。

在 PowerShell 視窗中，執行下列命令。 請務必使用您的實際資源組名來 reaplce *ResourceGroupName*、 *TemplateFile*和*TemplateParameterFile* ，並在作業工作區的 [**部署] 資料夾**中，將完整的檔案路徑放到*JobTemplate*和*JobTemplate. parameters*檔案。

如果您未設定 Azure PowerShell，請依照[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-Az-ps)中的步驟進行。

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName "<your resource group>" -TemplateFile "<path to JobTemplate.json>" -TemplateParameterFile "<path to JobTemplate.parameters.json>"
```

## <a name="next-steps"></a>後續步驟

* [使用 Visual Studio Code 在本機測試 Azure 串流分析作業和即時輸入](visual-studio-code-local-run-live-input.md)

* [使用 Visual Studio Code 探索 Azure 串流分析作業（預覽）](visual-studio-code-explore-jobs.md)