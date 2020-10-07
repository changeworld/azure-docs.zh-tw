---
title: 使用 Azure Resource Manager 範本 (ARM 範本) 建立 Azure Data Factory
description: 使用 Azure Resource Manager 範本 (ARM 範本) 建立範例的 Azure Data Factory 管線。
services: data-factory
ms.service: data-factory
tags: azure-resource-manager
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan, jingwang
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 07/16/2020
ms.openlocfilehash: eb423ebd354adeb8273755d34323b283d53eb8b5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "87283889"
---
# <a name="quickstart-create-an-azure-data-factory-using-arm-template"></a>快速入門：使用 ARM 範本建立 Azure Data Factory

> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](v1/data-factory-build-your-first-pipeline-using-arm.md)
> * [目前的版本](quickstart-create-data-factory-resource-manager-template.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本快速入門說明如何使用 Azure Resource Manager 範本 (ARM 範本) 建立 Azure Data Factory。 在此資料處理站中建立的管線會將資料從 Azure Blob 儲存體中的一個資料夾**複製**到其他資料夾。 如需如何使用 Azure Data Factory **轉換**資料的教學課程，請參閱[教學課程︰使用 Spark 轉換資料](transform-data-using-spark.md)。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

> [!NOTE]
> 本文不提供 Data Factory 服務的詳細簡介。 如需 Azure Data Factory 服務簡介，請參閱 [Azure Data Factory 簡介](introduction.md)。

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-factory-v2-blob-to-blob-copy%2Fazuredeploy.json)

## <a name="prerequisites"></a>必要條件

### <a name="azure-subscription"></a>Azure 訂用帳戶

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

### <a name="create-a-file"></a>建立檔案

請開啟文字編輯器 (例如 [記事本]) 並使用下列內容建立名為 **emp.txt** 的檔案：

```emp.txt
John, Doe
Jane, Doe
```

將該檔案儲存在 **C:\ADFv2QuickStartPSH** 資料夾中。 (如果該資料夾不存在，請予以建立。)

## <a name="review-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-data-factory-v2-blob-to-blob-copy/)。

:::code language="json" source="~/quickstart-templates/101-data-factory-v2-blob-to-blob-copy/azuredeploy.json":::

範本中定義了 Azure 資源：

- [Microsoft.Storage/storageAccounts](/azure/templates/Microsoft.Storage/storageAccounts)：定義儲存體帳戶。
- [Microsoft.DataFactory/factories](/azure/templates/microsoft.datafactory/factories)：建立 Azure Data Factory。
- [Microsoft.DataFactory/factories/linkedServices](/azure/templates/microsoft.datafactory/factories/linkedservices)：建立 Azure Data Factory 連結服務。
- [Microsoft.DataFactory/factories/datasets](/azure/templates/microsoft.datafactory/factories/datasets)：建立 Azure Data Factory 資料集。
- [Microsoft.DataFactory/factories/pipelines](/azure/templates/microsoft.datafactory/factories/pipelines)：建立 Azure Data Factory 管線。

您可以在[快速入門範本資源庫](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datafactory&pageNumber=1&sort=Popular)中找到更多 Azure Data Factory 範本的範例。

## <a name="deploy-the-template"></a>部署範本

1. 選取以下影像來登入 Azure 並開啟範本。 此範本會建立一個 Azure Data Factory 帳戶、一個儲存體帳戶和一個 Blob 容器。

    [![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-factory-v2-blob-to-blob-copy%2Fazuredeploy.json)

2. 選取或輸入下列值。

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-deploy-arm-template.png" alt-text="部署 ADF ARM 範本":::。

    除非有指定，否則請使用預設值來建立 Azure Data Factory 資源：

    - 訂用帳戶：選取 Azure 訂用帳戶。
    - **資源群組**：選取 [新建]，輸入資源群組的唯一名稱，然後選取 [確認]。
    - **區域**：選取位置。  例如，「美國東部」。
    - **Data Factory 名稱**：使用預設值。
    - **位置**：使用預設值。
    - **儲存體帳戶名稱**：使用預設值。
    - **Blob 容器**：使用預設值。

## <a name="review-deployed-resources"></a>檢閱已部署的資源

1. 選取 [前往資源群組]。

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-go-to-resource-group.png" alt-text="部署 ADF ARM 範本":::

2.  確認是否已建立 Azure Data Factory。
    1. 您的 Azure Data Factory 名稱格式為 - datafactory\<uniqueid\>。

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-sample.png" alt-text="部署 ADF ARM 範本":::

2. 確認已建立儲存體帳戶。
    1. 儲存體帳戶名稱的格式為 - storage\<uniqueid\>。

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-storage-account.png" alt-text="部署 ADF ARM 範本":::

3. 選取建立的儲存體帳戶，然後選取 [容器] 。
    1. 在 [B容器] 頁面中，選取您建立的 Blob 容器。
        1. Blob 容器名稱的格式為 - blob\<uniqueid\>。

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-blob-container.png" alt-text="部署 ADF ARM 範本":::

### <a name="upload-a-file"></a>上傳檔案

1. 在 [容器] 頁面上，選取 [上傳]。

2. 在右側窗格中，選取選取 [檔案] 方塊，然後選取稍早建立的 **emp.txt** 檔案。

3. 展開 [進階] 標題。

4. 在 [上傳至資料夾] 方塊中，輸入 *input*。

5. 選取 [上傳] 按鈕。 您應該會在清單中看到 **emp.txt** 檔案以及上傳的狀態。

6. 選取 [關閉] 圖示 (**X**) 以關閉 [上傳 Blob] 頁面。

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-upload-blob-file.png" alt-text="部署 ADF ARM 範本":::

將 [容器] 頁面保持開啟，因為您可以用該頁面來驗證本快速入門結尾處的輸出。

### <a name="start-trigger"></a>啟動觸發程序

1. 瀏覽至 [資料處理站] 頁面，選取您建立的資料管理站。 

2. 選取 [建立者和監視] 圖格。 

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-author-monitor-tile.png" alt-text="部署 ADF ARM 範本":::

2. 選取 [建立者]:::image type="icon" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-author.png" border="false"::: 索引標籤。

3. 選取已建立的管線 - ArmtemplateSampleCopyPipeline。

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-pipelines.png" alt-text="部署 ADF ARM 範本":::

4. 選取 [新增觸發程序] > [立即觸發]。

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-trigger-now.png" alt-text="部署 ADF ARM 範本":::

5. 在右側窗格的 **管線執行**下，選取 [確定]。

### <a name="monitor-the-pipeline"></a>監視管線

1. 選取 [監視] 索引標籤 :::image type="icon" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-monitor.png" border="false":::。

2. 您會看到與管線執行相關聯的活動執行。 在本快速入門中，管線只有一個以下類型的活動：複製。 如此，您會看到該活動的執行。

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-successful-run.png" alt-text="部署 ADF ARM 範本":::

### <a name="verify-the-output-file"></a>驗證輸出檔案

管道會自動在 Blob 容器中建立輸出資料夾。 然後，它會將 emp.txt 檔案從輸入資料夾複製到輸出資料夾。 

1. 在 Azure 入口網站的 [容器] 頁面上，選取 [重新整理] 查看輸出資料夾。 

2. 在資料夾清單中，選取 [輸出]。

3. 確認 **emp.txt** 已複製到輸出資料夾。 

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-output.png" alt-text="部署 ADF ARM 範本":::

## <a name="clean-up-resources"></a>清除資源

您有兩種方式可以清除您在本快速入門中建立的資源。 您可以刪除 [Azure 資源群組](../azure-resource-manager/management/delete-resource-group.md)，其中包括資源群組中的所有資源。 如果您想要讓其他資源保持不變，請僅刪除您在本教學課程中建立的資料處理站。

刪除資源群組會刪除所有資源，包含其中的 Data Factory。 執行下列命令來刪除整個資源群組： 

```azurepowershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

如果您需要只刪除 Data Factory，而非整個資源群組，請執行下列命令： 

```azurepowershell-interactive
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已使用 ARM 範本建立了 Azure Data Factory，並已驗證了該部署。 若要深入了解 Azure Data Factory 和 Azure Resource Manager，請繼續閱讀下列文章。

- [Azure Data Factory 文件](index.yml)
- 深入了解 [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- 取得其他 [Azure Data Factory ARM 範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datafactory&pageNumber=1&sort=Popular)