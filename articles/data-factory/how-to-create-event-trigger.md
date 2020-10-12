---
title: 在 Azure Data Factory 中建立事件型觸發程序
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
ms.openlocfilehash: 10f0079f47e5d2fd99b358fcc5cfb4c80aa9bd91
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84508891"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>建立會執行管線來回應事件的觸發程序
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文說明可在 Data Factory 管線中建立的事件型觸發程序。

事件驅動架構 (EDA) 是常見的資料整合模式，所涉及的環節包括生產、偵測、取用和事件反應。 資料整合案例通常需要 Data Factory 客戶根據事件 (例如 Azure 儲存體帳戶中的檔案送達或刪除) 來觸發管線。 Data Factory 現在與 [Azure 事件方格](https://azure.microsoft.com/services/event-grid/)整合，可讓您觸發事件上的管線。

如需此功能的 10 分鐘簡介與示範，請觀看下列影片：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> 本文章中說明的整合，仰賴 [Azure 事件方格](https://azure.microsoft.com/services/event-grid/)。 請確認您的訂用帳戶已向事件方格資源提供者註冊。 如需詳細資訊，請參閱[資源提供者和類型](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)。 您必須能夠執行 *EventGrid/eventSubscriptions/** 動作。 此動作是 EventGrid EventSubscription 參與者內建角色的一部分。

## <a name="data-factory-ui"></a>Data Factory UI

本節說明如何在 Azure Data Factory 使用者介面中建立事件觸發程序。

1. 移到 [撰寫畫布]

1. 按一下左下角的 [觸發程序] 按鈕

1. 按一下 [+ 新增]，這會開啟建立觸發程序側邊導覽

1. 選取觸發程序類型 [事件]

    ![建立新的事件觸發程序](media/how-to-create-event-trigger/event-based-trigger-image1.png)

1. 從 Azure 訂用帳戶下拉式清單中選取您的儲存體帳戶，或使用其儲存體帳戶資源識別碼手動進行。 選擇您希望發生事件的容器。 容器選取是選擇性作業，但請注意，選取所有容器可能會導致大量的事件。

   > [!NOTE]
   > 事件觸發程序目前僅支援 Azure Data Lake Storage Gen2 和一般用途的第 2 版儲存體帳戶。 您必須至少擁有儲存體帳戶的 *擁有* 者存取權。  由於 Azure 事件方格的限制，Azure Data Factory 只支援每個儲存體帳戶最多 500 個事件觸發程序。

1. [Blob 路徑開頭] 和 [Blob 路徑結尾] 屬性可讓您指定要收到事件的容器、資料夾和 Blob 名稱。 您的事件觸發程序至少需要定義其中一個屬性。 您可以針對 **Blob path begins with** 和 **Blob path ends with** 屬性使用各種不同的模式，如本文稍後的範例所示。

    * **Blob 路徑開頭：** Blob 路徑的開頭必須是資料夾路徑。 有效值包括 `2018/` 和 `2018/april/shoes.csv`。 如果未選取容器，就無法選取此欄位。
    * **Blob 路徑結尾：** Blob 路徑的結尾必須是檔案名稱或副檔名。 有效值包括 `shoes.csv` 和 `.csv`。 容器和資料夾名稱均為選擇性項目，但若已指定，則必須以 `/blobs/` 區段分隔。 例如，名為 'orders' 的容器可以有 `/orders/blobs/2018/april/shoes.csv`值。 若要指定任何容器中的資料夾，請省略前置的 '/' 字元。 例如，`april/shoes.csv` 將會在任何容器中名為 'april' 的資料夾中，對任何名為 `shoes.csv` 的檔案觸發事件。 

1. 選取您的觸發程序是否會回應 [已建立 Blob] 事件、[已刪除 Blob] 事件，或回應兩者。 在您指定的儲存位置中，每個事件都會觸發與觸發程序相關聯的 Data Factory 管線。

    ![設定事件觸發程序](media/how-to-create-event-trigger/event-based-trigger-image2.png)

1. 選取您的觸發程序是否忽略零位元組的 Blob。

1. 設定好觸發程序之後，請按 [下一步：資料預覽]。 此畫面會顯示您的事件觸發程序設定所符合的現有 Blob。 請確定您有特定的篩選條件。 設定太廣泛的篩選條件可能會比對大量已建立/刪除的檔案，並可能大幅影響您的成本。 確認篩選條件之後，請按一下 [完成]。

    ![事件觸發程序資料預覽](media/how-to-create-event-trigger/event-based-trigger-image3.png)

1. 若要將管線連結到此觸發程序，請移至管線畫布，然後按一下 [新增觸發程序]，然後選取 [新增/編輯]。 當側邊導覽出現時，按一下 [選擇觸發程序...] 下拉式清單，然後選取您所建立的觸發程序。 按 [下一步：資料預覽] 以確認設定正確無誤，然後按 [下一步] 來驗證資料預覽是否正確。

1. 如果您的管線具有參數，即可在觸發程序的 runs 參數側邊導覽上加以指定。 事件觸發程序會將 Blob 的資料夾路徑和檔案名稱擷取到 `@trigger().outputs.body.folderPath` 和 `@trigger().outputs.body.fileName` 屬性中。 若要在管線中使用這些屬性的值，您必須將屬性對應到管線參數。 在將屬性對應到參數之後，您可在整個管線中透過 `@pipeline().parameters.parameterName` 運算式存取觸發程序所擷取的值。 當您完成時按一下 [完成]。

    ![將屬性對應到管線參數](media/how-to-create-event-trigger/event-based-trigger-image4.png)

在上述範例中，觸發程序已設定為在 ample-data 容器的 event-testing 資料夾中建立以 .csv 結尾的 Blob 路徑時引發。 [folderPath] 和 [fileName] 屬性都會擷取新 Blob 的位置。 例如，將 MoviesDB.csv 新增至 sample-data/event-testing 路徑時，`@trigger().outputs.body.folderPath` 的值為 `sample-data/event-testing`，而 `@trigger().outputs.body.fileName` 的值為 `moviesDB.csv`。 這些值會在範例中對應至管線參數 `sourceFolder` 和 `sourceFile`，其在管線中可分別作為 `@pipeline().parameters.sourceFolder` 和 `@pipeline().parameters.sourceFile` 使用。

## <a name="json-schema"></a>JSON 結構描述

下表提供與事件型觸發程序相關的結構描述元素概觀：

| **JSON 元素** | **說明** | **型別** | **允許的值** | **必要** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **範圍 (scope)** | 儲存體帳戶的 Azure Resource Manager 資源識別碼。 | String | Azure Resource Manager 識別碼 | 是 |
| **events** | 會導致引發此觸發程序的事件類型。 | Array    | Microsoft.Storage.BlobCreated、Microsoft.Storage.BlobDeleted | 是，這些值的任意組合。 |
| **blobPathBeginsWith** | Blob 路徑的開頭必須是提供來引發觸發程序的模式。 例如，`/records/blobs/december/` 只會針對 `records` 容器下 `december` 資料夾中的 Blob 引發觸發程序。 | String   | | 您必須為下列屬性中的至少一個屬性提供值：`blobPathBeginsWith` 或 `blobPathEndsWith`。 |
| **blobPathEndsWith** | Blob 路徑的結尾必須是提供來引發觸發程序的模式。 例如，`december/boxes.csv` 只會針對 `december` 資料夾中名為 `boxes` 的 Blob 引發觸發程序。 | String   | | 您必須為下列屬性中的至少一個屬性提供值：`blobPathBeginsWith` 或 `blobPathEndsWith`。 |
| **ignoreEmptyBlobs** | 零位元組 Blob 是否會觸發管線執行。 此值會預設為 True。 | Boolean | true 或 false | 否 |

## <a name="examples-of-event-based-triggers"></a>事件型觸發程序的範例

本節提供事件型觸發程序設定的範例。

> [!IMPORTANT]
> 每當您指定容器與資料夾、容器與檔案，或容器、資料夾與檔案時，都必須包含路徑的 `/blobs/` 區段，如下列範例所示。 針對 **blobPathBeginsWith**，Data Factory UI 會自動在觸發程序 JSON 中的資料夾與容器名稱之間新增 `/blobs/`。

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
