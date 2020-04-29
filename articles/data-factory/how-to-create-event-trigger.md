---
title: 在 Azure Data Factory 中建立以事件為基礎的觸發程式
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
ms.openlocfilehash: d697fb8afe3e92dfe54eb5d89a2ef59425cb0cde
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414920"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>建立會執行管線來回應事件的觸發程序
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文說明可在 Data Factory 管線中建立的事件型觸發程序。

事件驅動架構 (EDA) 是常見的資料整合模式，所涉及的環節包括生產、偵測、取用和事件反應。 資料整合案例通常需要 Data Factory 客戶根據事件（例如，Azure 儲存體帳戶中的檔案抵達或刪除）來觸發管線。 Data Factory 現在與 [Azure 事件方格](https://azure.microsoft.com/services/event-grid/)整合，可讓您觸發事件上的管線。

如需此功能的 10 分鐘簡介與示範，請觀看下列影片：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> 本文章中說明的整合，仰賴 [Azure 事件方格](https://azure.microsoft.com/services/event-grid/)。 請確認您的訂用帳戶已向事件方格資源提供者註冊。 如需詳細資訊，請參閱[資源提供者和類型](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)。

## <a name="data-factory-ui"></a>Data Factory UI

本節說明如何在 Azure Data Factory 使用者介面中建立事件觸發程式。

1. 前往**撰寫畫布**

1. 按一下左下角的 [**觸發**程式] 按鈕

1. 按一下 [ **+ 新增**]，開啟 [建立觸發程式] 端導覽

1. 選取觸發程式類型**事件**

    ![建立新的事件觸發程序](media/how-to-create-event-trigger/event-based-trigger-image1.png)

1. 從 [Azure 訂用帳戶] 下拉式清單中選取您的儲存體帳戶，或使用其儲存體帳戶資源識別碼手動進行。 選擇您想要發生事件的容器。 容器選取是選擇性的，但請注意，選取所有容器可能會導致大量的事件。

   > [!NOTE]
   > 事件觸發程式目前僅支援 Azure Data Lake Storage Gen2 和一般用途的第2版儲存體帳戶。 由於 Azure 事件方格的限制，Azure Data Factory 只支援每個儲存體帳戶最多500個事件觸發程式。

1. **Blob 路徑的開頭為**， **blob 路徑結尾為**屬性，可讓您指定要接收事件的容器、資料夾和 blob 名稱。 您的事件觸發程式至少需要定義其中一個屬性。 您可以針對 **Blob path begins with** 和 **Blob path ends with** 屬性使用各種不同的模式，如本文稍後的範例所示。

    * **Blob 路徑開頭為：** Blob 路徑的開頭必須是資料夾路徑。 有效值包括 `2018/` 和 `2018/april/shoes.csv`。 如果未選取容器，就無法選取此欄位。
    * **Blob 路徑的結尾為：** Blob 路徑的結尾必須是檔案名或副檔名。 有效值包括 `shoes.csv` 和 `.csv`。 容器和資料夾名稱是選擇性的，但在指定時，必須以`/blobs/`區段分隔。 例如，名為 ' orders ' 的容器可以具有值`/orders/blobs/2018/april/shoes.csv`。 若要指定任何容器中的資料夾，請省略前置的 '/' 字元。 例如， `april/shoes.csv`會對任何容器中名為`shoes.csv` ' 四月 ' 的資料夾 a 中的任何檔案觸發事件。 

1. 選取您的觸發程式將會回應**blob 建立**的事件、 **blob 刪除**事件，或兩者。 在您指定的儲存位置中，每個事件都會觸發與觸發程式相關聯的 Data Factory 管線。

    ![設定事件觸發程序](media/how-to-create-event-trigger/event-based-trigger-image2.png)

1. 選取您的觸發程式是否忽略零位元組的 blob。

1. 設定好觸發程式之後，請按 **[下一步：資料預覽]**。 此畫面會顯示您的事件觸發程式設定所符合的現有 blob。 請確定您有特定的篩選準則。 設定太廣泛的篩選可能會比對建立/刪除的大量檔案，而且可能會大幅影響您的成本。 確認篩選準則之後，請按一下 **[完成]**。

    ![事件觸發程式資料預覽](media/how-to-create-event-trigger/event-based-trigger-image3.png)

1. 若要將管線附加到此觸發程式，請移至管線畫布，然後按一下 [**加入觸發**程式] 並選取 [**新增/編輯**]。 當側邊流覽出現時，按一下 [**選擇觸發程式 ...** ] 下拉式清單，然後選取您所建立的觸發程式。 按 **[下一步：資料預覽]** 確認設定正確，然後按一下 [驗證資料預覽正確 **]** 。

1. 如果您的管線具有參數，您可以在觸發程式的 [執行] 參數端導覽上指定它們。 事件觸發程式會將 blob 的資料夾路徑和檔案名，捕獲到屬性`@triggerBody().folderPath`和`@triggerBody().fileName`中。 若要在管線中使用這些屬性的值，您必須將屬性對應到管線參數。 在將屬性對應到參數之後，您可在整個管線中透過 `@pipeline().parameters.parameterName` 運算式存取觸發程序所擷取的值。 完成後，請按一下 **[完成]** 。

    ![將屬性對應到管線參數](media/how-to-create-event-trigger/event-based-trigger-image4.png)

在上述範例中，觸發程式設定為在容器範例-資料中的資料夾事件測試中建立以 .csv 結尾的 blob 路徑時引發。 **FolderPath**和**fileName**屬性會捕捉新 blob 的位置。 例如，將 MoviesDB 新增至路徑範例-資料/事件`@triggerBody().folderPath`測試時，的值為`sample-data/event-testing` ，且`@triggerBody().fileName`的值為。 `moviesDB.csv` 這些值`sourceFolder`會在範例中對應至管線參數，而且`sourceFile`可以分別在管線中當做`@pipeline().parameters.sourceFolder`和`@pipeline().parameters.sourceFile`使用。

## <a name="json-schema"></a>JSON 結構描述

下表提供與事件型觸發程序相關的結構描述元素概觀：

| **JSON 元素** | **說明** | **類型** | **允許的值** | **必要** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **範圍** | 儲存體帳戶的 Azure Resource Manager 資源識別碼。 | 字串 | Azure Resource Manager 識別碼 | 是 |
| **事件** | 會導致引發此觸發程序的事件類型。 | Array    | Microsoft.Storage.BlobCreated、Microsoft.Storage.BlobDeleted | 是，這些值的任意組合。 |
| **blobPathBeginsWith** | Blob 路徑的開頭必須是提供來引發觸發程序的模式。 例如，`/records/blobs/december/` 只會針對 `records` 容器下 `december` 資料夾中的 Blob 引發觸發程序。 | 字串   | | 您必須為下列屬性中的至少一個屬性提供值：`blobPathBeginsWith` 或 `blobPathEndsWith`。 |
| **blobPathEndsWith** | Blob 路徑的結尾必須是提供來引發觸發程序的模式。 例如，`december/boxes.csv` 只會針對 `december` 資料夾中名為 `boxes` 的 Blob 引發觸發程序。 | 字串   | | 您必須為下列屬性中的至少一個屬性提供值：`blobPathBeginsWith` 或 `blobPathEndsWith`。 |
| **ignoreEmptyBlobs** | 零位元組 blob 是否會觸發管線執行。 根據預設，這會設定為 true。 | 布林值 | true 或 false | 否 |

## <a name="examples-of-event-based-triggers"></a>事件型觸發程序的範例

本節提供事件型觸發程序設定的範例。

> [!IMPORTANT]
> 每當您指定容器與資料夾、容器與檔案，或容器、資料夾與檔案時，都必須包含路徑的 `/blobs/` 區段，如下列範例所示。 針對**blobPathBeginsWith**，Data Factory UI 會在觸發程式`/blobs/` JSON 中的資料夾與容器名稱之間自動新增。

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
如需有關觸發程序的詳細資訊，請參閱[管線執行和觸發程序](concepts-pipeline-execution-triggers.md#trigger-execution)。
