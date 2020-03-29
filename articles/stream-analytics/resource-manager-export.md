---
title: 匯出 Azure 流分析作業 Azure 資源管理器範本
description: 本文介紹如何為 Azure 流分析作業匯出 Azure 資源管理器範本。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 52ea7b45d0dcdb3ae16b8212557ba6ab3344ff15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968911"
---
# <a name="export-an-azure-stream-analytics-job-azure-resource-manager-template"></a>匯出 Azure 流分析作業 Azure 資源管理器範本

[Azure 資源管理器範本](../azure-resource-manager/templates/overview.md)允許您將基礎結構作為代碼實現。 該範本是一個 JavaScript 物件標記法 （JSON） 檔，用於定義資源的基礎結構和配置。 指定要部署的資源和這些資源的屬性。

您可以通過匯出 Azure 資源管理器範本來重新部署 Azure 流分析作業。

## <a name="open-a-job-in-vs-code"></a>在 VS 代碼中打開作業

必須先在視覺化工作室代碼中打開現有的流分析作業，然後才能匯出範本。 

要將作業匯出到本地專案，請在 Azure 門戶中的**流分析資源管理器**中找到要匯出的作業。 從 **"查詢**"頁中，選擇 **"在視覺工作室中打開**"。 然後選擇**視覺工作室代碼**。

![在視覺化工作室代碼中打開流分析作業](./media/resource-manager-export/open-job-vs-code.png)

有關使用視覺化工作室代碼管理流分析作業的詳細資訊，請參閱[視覺化工作室代碼快速入門](quick-create-vs-code.md)。

## <a name="compile-the-script"></a>編譯指令碼 

下一步是將作業腳本編譯為 Azure 資源管理器範本。 在編譯腳本之前，請確保作業至少配置了一個輸入和一個輸出。 如果未配置輸入或輸出，則需要首先配置輸入和輸出。

1. 在視覺化工作室代碼中，導航到作業的*轉換.asaql*檔。

   ![變形.asaql 檔在視覺化工作室代碼](./media/resource-manager-export/transformation-asaql.png)

1. 按右鍵 *"轉換.asaql"* 檔，然後從功能表中選擇**ASA：編譯腳本**。

1. 請注意 **，"部署"** 資料夾將顯示在流分析作業工作區中。

1. 流覽*工作範本.json*檔，該檔是用於部署的 Azure 資源管理範本。

## <a name="complete-the-parameters-file"></a>完成參數檔

接下來，完成 Azure 資源管理範本參數檔。

1. 打開位於視覺化工作室代碼中的"部署"工作區的 **"部署"** 資料夾中的*工作範本.parameters.json*檔。

1. 請注意，輸入和輸出鍵為空。 將空值替換為輸入和輸出資源的實際便捷鍵。

1. 儲存參數檔案。

## <a name="deploy-using-templates"></a>使用範本進行部署

可以使用上一節中生成的 Azure 資源管理器範本部署 Azure 流分析作業。

在 PowerShell 視窗中，運行以下命令。 請務必使用實際資源組名稱重新使用*資源組名稱*、*範本檔*檔和*範本參數檔*，以及作業工作區**的"部署資料夾"** 中*工作範本.json*和*JobTemplate.parameter.json*檔的完整檔路徑。

如果未配置 Azure PowerShell，請按照[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-Az-ps)中的步驟操作。

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName "<your resource group>" -TemplateFile "<path to JobTemplate.json>" -TemplateParameterFile "<path to JobTemplate.parameters.json>"
```

## <a name="next-steps"></a>後續步驟

* [使用視覺化工作室代碼使用即時輸入在本地測試 Azure 流分析作業](visual-studio-code-local-run-live-input.md)

* [使用視覺化工作室代碼流覽 Azure 流分析作業（預覽版）](visual-studio-code-explore-jobs.md)