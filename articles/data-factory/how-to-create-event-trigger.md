---
title: 在 Azure 資料工廠中創建基於事件的觸發器
description: 了解如何在 Azure Data Factory 中建立會執行管線來回應事件的觸發程序。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 10/18/2018
ms.openlocfilehash: e4301afafb48fb9a1b0c9e36dde9800e2b8390f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443928"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>建立會執行管線來回應事件的觸發程序

本文說明可在 Data Factory 管線中建立的事件型觸發程序。

事件驅動架構 (EDA) 是常見的資料整合模式，所涉及的環節包括生產、偵測、取用和事件反應。 資料整合方案通常要求資料工廠客戶根據 Azure 存儲帳戶中的檔到達或刪除等事件觸發管道。 Data Factory 現在與 [Azure 事件方格](https://azure.microsoft.com/services/event-grid/)整合，可讓您觸發事件上的管線。

如需此功能的 10 分鐘簡介與示範，請觀看下列影片：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> 本文章中說明的整合，仰賴 [Azure 事件方格](https://azure.microsoft.com/services/event-grid/)。 請確認您的訂用帳戶已向事件方格資源提供者註冊。 如需詳細資訊，請參閱[資源提供者和類型](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)。

## <a name="data-factory-ui"></a>Data Factory UI

本節介紹如何在 Azure 資料工廠使用者介面中創建事件觸發器。

1. 轉到**創作畫布**

1. 在左下角，按一下 **"觸發器"** 按鈕

1. 按一下 **+ 新建**，這將打開創建觸發器側導航

1. 選擇觸發器類型**事件**

    ![建立新的事件觸發程序](media/how-to-create-event-trigger/event-based-trigger-image1.png)

1. 從 Azure 訂閱下拉清單中選擇存儲帳戶，或使用其存儲帳戶資源識別碼 手動選擇存儲帳戶。 選擇要在哪個容器上發生的事件。 容器選擇是可選的，但請注意，選擇所有容器可能會導致大量事件。

   > [!NOTE]
   > 事件觸發器目前僅支援 Azure 資料存儲庫存儲第 2 代和通用版本 2 存儲帳戶。 由於 Azure 事件網格限制，Azure 資料工廠僅支援每個存儲帳戶最多 500 個事件觸發器。

1. **Blob 路徑以"Blob"路徑開頭****，Blob 路徑以屬性結尾**，允許您指定要為其接收事件的容器、資料夾和 Blob 名稱。 事件觸發器至少需要定義其中一個屬性。 您可以針對 **Blob path begins with** 和 **Blob path ends with** 屬性使用各種不同的模式，如本文稍後的範例所示。

    * **Blob 路徑從：** Blob 路徑必須以資料夾路徑開頭。 有效值包括 `2018/` 和 `2018/april/shoes.csv`。 如果未選擇容器，則無法選擇此欄位。
    * **Blob 路徑以：** blob 路徑必須以檔案名或副檔名結尾。 有效值包括 `shoes.csv` 和 `.csv`。 容器和資料夾名稱是可選的，但指定時，它們必須由段`/blobs/`分隔。 例如，名為"訂單"的容器的值可以為`/orders/blobs/2018/april/shoes.csv`。 要在任何容器中指定資料夾，請省略前導的"/"字元。 例如，`april/shoes.csv`將在資料夾中名為`shoes.csv`"april"的任何檔中觸發任何容器中名為"april"的事件。 

1. 選擇觸發器是回應**Blob 創建**的事件 **、Blob 已刪除**的事件還是兩者兼而有之。 在指定的存儲位置中，每個事件都將觸發與觸發器關聯的資料工廠管道。

    ![設定事件觸發程序](media/how-to-create-event-trigger/event-based-trigger-image2.png)

1. 選擇觸發器是否忽略零位元組的 Blob。

1. 配置觸發器後，按一下"**下一步：資料預覽**"。 此螢幕顯示事件觸發器配置匹配的現有 blob。 請確保您有特定的篩選器。 配置過於寬泛的篩選器可以匹配大量創建/刪除的檔，並可能顯著影響您的成本。 驗證篩選準則後，按一下 **"完成**"。

    ![事件觸發資料預覽](media/how-to-create-event-trigger/event-based-trigger-image3.png)

1. 要將管道附加到此觸發器，請轉到管道畫布，然後按一下"**添加觸發器**"並選擇 **"新建/編輯**"。 當側導航出現時，按一下 **"選擇觸發器..."** 下拉清單並選擇您創建的觸發器。 按一下 **"下一步：資料預覽**"以確認配置正確，然後**按一下"下一步**"以驗證資料預覽是否正確。

1. 如果管道具有參數，則可以在觸發器運行參數側導航上指定它們。 事件觸發器將 blob 的資料夾路徑和檔案名捕獲到 屬性`@triggerBody().folderPath`和`@triggerBody().fileName`中。 若要在管線中使用這些屬性的值，您必須將屬性對應到管線參數。 在將屬性對應到參數之後，您可在整個管線中透過 `@pipeline().parameters.parameterName` 運算式存取觸發程序所擷取的值。 完成後，按一下"**完成**"。

    ![將屬性對應到管線參數](media/how-to-create-event-trigger/event-based-trigger-image4.png)

在前面的示例中，觸發器配置為在容器示例資料中的資料夾事件測試中創建以 .csv 結尾的 Blob 路徑時觸發。 **資料夾 Path**和**檔案名**屬性捕獲新 Blob 的位置。 例如，當 MoviesDB.csv 添加到路徑示例資料/事件測試時`@triggerBody().folderPath`，其值 和`sample-data/event-testing``@triggerBody().fileName`的值為`moviesDB.csv`。 這些值在示例中`sourceFolder`映射到管道參數`sourceFile`，並且可以`@pipeline().parameters.sourceFolder``@pipeline().parameters.sourceFile`分別在整個管道中使用。

## <a name="json-schema"></a>JSON 結構描述

下表提供與事件型觸發程序相關的結構描述元素概觀：

| **JSON 元素** | **描述** | **類型** | **允許的值** | **必要** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **範圍** | 儲存體帳戶的 Azure Resource Manager 資源識別碼。 | String | Azure Resource Manager 識別碼 | 是 |
| **事件** | 會導致引發此觸發程序的事件類型。 | Array    | Microsoft.Storage.BlobCreated、Microsoft.Storage.BlobDeleted | 是，這些值的任意組合。 |
| **blobPathBeginsWith** | Blob 路徑的開頭必須是提供來引發觸發程序的模式。 例如，`/records/blobs/december/` 只會針對 `records` 容器下 `december` 資料夾中的 Blob 引發觸發程序。 | String   | | 您必須為下列屬性中的至少一個屬性提供值：`blobPathBeginsWith` 或 `blobPathEndsWith`。 |
| **blobPathEndsWith** | Blob 路徑的結尾必須是提供來引發觸發程序的模式。 例如，`december/boxes.csv` 只會針對 `december` 資料夾中名為 `boxes` 的 Blob 引發觸發程序。 | String   | | 您必須為下列屬性中的至少一個屬性提供值：`blobPathBeginsWith` 或 `blobPathEndsWith`。 |
| **忽略空Blobs** | 零位元組 blob 是否會觸發管道運行。 預設情況下，這設置為 true。 | Boolean | true 或 false | 否 |

## <a name="examples-of-event-based-triggers"></a>事件型觸發程序的範例

本節提供事件型觸發程序設定的範例。

> [!IMPORTANT]
> 每當您指定容器與資料夾、容器與檔案，或容器、資料夾與檔案時，都必須包含路徑的 `/blobs/` 區段，如下列範例所示。 對於**blobPath 開始，** 資料工廠 UI 將自動`/blobs/`在觸發器 JSON 中的資料夾和容器名稱之間添加。

| 屬性 | 範例 | 描述 |
|---|---|---|
| **Blob 路徑開頭** | `/containername/` | 接收容器中任何 Blob 的事件。 |
| **Blob 路徑開頭** | `/containername/blobs/foldername/` | 接收 `containername` 容器與 `foldername` 資料夾中任何 Blob 的事件。 |
| **Blob 路徑開頭** | `/containername/blobs/foldername/subfoldername/` | 您也可以參考子資料夾。 |
| **Blob 路徑開頭** | `/containername/blobs/foldername/file.txt` | 接收 `containername` 容器下 `foldername` 資料夾中名為 `file.txt` 之 Blob 的事件。 |
| **Blob 路徑結尾** | `file.txt` | 接收任何路徑中名為 `file.txt` 之 Blob 的事件。 |
| **Blob 路徑結尾** | `/containername/blobs/file.txt` | 接收容器 `containername` 下名為 `file.txt` 之 Blob 的事件。 |
| **Blob 路徑結尾** | `foldername/file.txt` | 接收任何容器下 `foldername` 資料夾中名為 `file.txt` 之 Blob 的事件。 |

## <a name="next-steps"></a>後續步驟
如需有關觸發程序的詳細資訊，請參閱[管線執行和觸發程序](concepts-pipeline-execution-triggers.md#triggers)。
