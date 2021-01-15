---
title: 管理具有關聯性的對應項圖形
titleSuffix: Azure Digital Twins
description: 瞭解如何藉由與關聯性連接來管理數位 twins 的圖形。
author: baanders
ms.author: baanders
ms.date: 11/03/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 54001cde76bd89305eb77544c6e6858defda6096
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222542"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>使用關聯性管理數位 twins 的圖形

Azure 數位 Twins 的核心是代表整個環境的對應項 [圖形](concepts-twins-graph.md) 。 對應項圖形是由透過 **關聯** 性連接的個別數位 twins 所組成。 

一旦您有運作中的 [Azure 數位 Twins 實例](how-to-set-up-instance-portal.md) ，並已在用戶端應用程式中設定 [驗證](how-to-authenticate-client.md) 程式碼，您就可以使用 [**DigitalTwins api**](/rest/api/digital-twins/dataplane/twins) 來建立、修改和刪除 Azure 數位 Twins 實例中的數位 Twins 及其關聯性。 您也可以使用 [.net (c # ) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)或 [AZURE 數位 Twins CLI](how-to-use-cli.md)。

本文著重于管理關聯性和整個圖形;若要使用個別的數位 twins，請參閱 how [*to：管理數位 twins*](how-to-manage-twin.md)。

## <a name="prerequisites"></a>先決條件

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-graph"></a>管理圖表的方式

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

您也可以使用 Azure 數位 Twins (ADT) Explorer 範例來變更您的圖形，這可讓您將 Twins 和圖形視覺化，並在幕後使用 SDK。 下一節將詳細說明此範例。

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>建立關聯性

關聯性會說明不同數位 twins 彼此連接的方式，以構成對應項圖形的基礎。

關聯性是使用呼叫建立的 `CreateOrReplaceRelationshipAsync()` 。 

若要建立關聯性，您必須指定：
* 下列程式碼範例中的來源對應項識別碼 (`srcId`) ：關聯性來源之對應項的識別碼。
* 下列程式碼範例中的目標對應項識別碼 (`targetId`) ：關聯性抵達之對應項的識別碼。
* 下列程式碼範例中 (的關聯性名稱 `relName`) ：關聯性的泛型型別，類似于 _contains_。
* 下列程式碼範例中 (的關聯性識別碼 `relId`) ：此關聯性的特定名稱，類似 _Relationship1_。

關聯性識別碼在給定的來源對應項內必須是唯一的。 它不需要是全域唯一的。
例如，針對對應項 *foo*，每個特定的關聯性識別碼都必須是唯一的。 但是，另一個對應項 *列可以有* 符合 *foo* 關聯性相同識別碼的外寄關聯性。

下列程式碼範例說明如何在您的 Azure 數位 Twins 實例中建立關聯性。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="CreateRelationshipMethod":::

在您的 main 方法中，您現在可以呼叫函式 `CreateRelationship()` 來建立 _包含_ 關聯性的關聯性，如下所示： 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseCreateRelationship":::

如果您想要建立多個關聯性，您可以重複呼叫相同的方法，將不同的關聯性類型傳遞給引數。 

如需 helper 類別的詳細資訊 `BasicRelationship` ，請參閱作法 [*：使用 Azure 數位 Twins Api 和 sdk*](how-to-use-apis-sdks.md#serialization-helpers)。

### <a name="create-multiple-relationships-between-twins"></a>在 twins 之間建立多個關聯性

關聯性可分類為： 

* 外寄關聯性：屬於這個對應項的關聯性，指向外部，以連接到其他 twins。 `GetRelationshipsAsync()`方法是用來取得對應項的外寄關聯性。
* 連入關聯性：屬於其他 twins 的關聯性，指向此對應項以建立「連入」連結。 `GetIncomingRelationshipsAsync()`方法是用來取得對應項的傳入關聯性。

您可以有兩個 twins 之間的關聯性數目沒有限制，您可以視需要在 twins 之間擁有任意數量的關聯性。 

這表示您可以一次表達兩個 twins 之間的幾種不同類型的關聯性。 例如，對應項 *a* 可以同時具有 *預存* 關聯性和對應項 *B* 的 *製造* 關聯性。

您甚至可以在相同的兩個 twins 之間建立多個相同類型之關聯性的實例（如有需要）。 在此範例中，對應項 *A* 可以有兩個與對應項 *B* 不同的 *預存* 關聯性，只要關聯性具有不同的關聯性識別碼即可。

## <a name="list-relationships"></a>列出關聯性

若要存取圖形中指定對應項的 **外寄** 關聯性清單，您可以使用 `GetRelationships()` 如下所示的方法：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

這會傳回 `Azure.Pageable<T>` 或 `Azure.AsyncPageable<T>` ，取決於您使用的是同步或非同步版本的呼叫。

以下是可抓取關聯性清單的範例：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod":::

您現在可以呼叫這個方法來查看 twins 的傳出關聯性，如下所示：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindOutgoingRelationships":::

您可以使用已抓取的關聯性來流覽至您圖形中的其他 twins。 若要這樣做，請 `target` 從傳回的關聯性中讀取欄位，然後使用它做為下一個呼叫的識別碼 `GetDigitalTwin()` 。

### <a name="find-incoming-relationships-to-a-digital-twin"></a>尋找數位對應項的連入關聯性

Azure 數位 Twins 也有 API 可尋找指定對應項的所有連 **入** 關聯性。 這通常適用于反向導覽或刪除對應項。

先前的程式碼範例著重于尋找對應項的傳出關聯性。 下列範例的結構類似，但會改為尋找對應項的連 *入* 關聯性。

請注意， `IncomingRelationship` 呼叫不會傳回關聯性的完整主體。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindIncomingRelationshipsMethod":::

您現在可以呼叫這個方法來查看 twins 的連入關聯性，如下所示：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindIncomingRelationships":::

### <a name="list-all-twin-properties-and-relationships"></a>列出所有對應項屬性和關聯性

使用上述方法來列出對應項的外寄和內送關聯性，您可以建立會列印完整對應項資訊的方法，包括對應項的屬性，以及這兩種類型的關聯性。 以下是稱為的範例方法，示範 `FetchAndPrintTwinAsync()` 如何進行這項作業。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FetchAndPrintMethod":::

您現在可以在 main 方法中呼叫此函式，如下所示： 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFetchAndPrint":::

## <a name="delete-relationships"></a>刪除關聯性

第一個參數會指定來源對應項 (關聯性源自) 的對應項。 另一個參數是關聯性識別碼。 您需要對應項識別碼和關聯性識別碼，因為關聯性識別碼只有在對應項的範圍內是唯一的。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="DeleteRelationshipMethod":::

您現在可以呼叫這個方法來刪除關聯性，如下所示：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseDeleteRelationship":::

## <a name="runnable-twin-graph-sample"></a>可執行檔對應項圖形範例

下列可執行檔程式碼片段會使用本文中的關聯性作業，從數位 twins 和關聯性建立對應項圖形。

### <a name="set-up-the-runnable-sample"></a>設定可執行檔範例

此程式碼片段會在教學課程中使用模型定義上的 [*Room.json*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) 和 [*Floor.js*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) [*：探索 Azure 數位 Twins 與範例用戶端應用程式*](tutorial-command-line-app.md)。 您可以使用這些連結直接移至檔案，或在 [這裡](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)將其下載為完整的端對端範例專案的一部分。 

執行範例之前，請執行下列動作：
1. 下載模型檔案，將它們放在您的專案中，並取代 `<path-to>` 下列程式碼中的預留位置，告訴您的程式要在哪裡找到它們。
2. 將預留位置取代 `<your-instance-hostname>` 為您的 Azure 數位 Twins 實例的主機名稱。
3. 將兩個相依性加入至您的專案，以使用 Azure 數位 Twins。 您可以使用下列連結瀏覽至 NuGet 上的套件，在其中找到主控台命令 (包括 .NET CLI 可用的命令)，將每個套件的最新版本新增至您的專案。
    * [**Azure.DigitalTwins.Core**](https://www.nuget.org/packages/Azure.DigitalTwins.Core)。 這是適用於[適用於 .NET 的 Azure Digital Twins SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) 的套件。
    * [**Azure.Identity**](https://www.nuget.org/packages/Azure.Identity). 此程式庫會提供協助驗證 Azure 的工具。

如果您想要直接執行範例，您也必須設定本機認證。 下一節將逐步解說這一點。
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>執行範例

完成上述步驟之後，您可以直接執行下列範例程式碼。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs":::

以下是上述程式的主控台輸出： 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="顯示對應項詳細資料的主控台輸出、twins 的連入和連出關聯性。" lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> 對應項圖形是在 twins 之間建立關聯性的概念。 如果您想要查看對應項圖形的視覺標記法，請參閱本文的 [視覺 [*效果*](how-to-manage-graph.md#visualization) ] 區段。 

## <a name="create-graph-from-a-csv-file"></a>從 CSV 檔案建立圖形

在實際使用案例中，對應項階層通常會從儲存在不同資料庫中的資料建立，或可能在試算表或 CSV 檔案中建立。 本節說明如何從 CSV 檔案讀取資料，並從中建立對應項圖形。

請考慮下列資料表，其中描述一組數位 twins 和關聯性。

|  模型識別碼    | 對應項識別碼 (必須是唯一的)  | 關聯性名稱  | 目標對應項識別碼  | 對應項初始化資料 |
| --- | --- | --- | --- | --- |
| dtmi：範例： Floor; 1    | Floor1 | contains | Room1 | |
| dtmi：範例： Floor; 1    | Floor0 | contains | Room0 | |
| dtmi：範例：房間; 1    | Room1 | | | {"溫度"： 80} |
| dtmi：範例：房間; 1    | Room0 | | | {"溫度"： 70} |

將此資料放入 Azure 數位 Twins 的其中一種方法是將資料表轉換為 CSV 檔案，並撰寫程式碼將檔案轉譯成命令以建立 Twins 和關聯性。 下列程式碼範例說明如何從 CSV 檔案讀取資料，並在 Azure 數位 Twins 中建立對應項圖形。

在下列程式碼中，會將 CSV 檔案稱為 *data.csv*，且有一個預留位置代表您 Azure 數位 Twins 實例的 **主機名稱** 。 此範例也會使用您可以新增至專案的數個套件，以協助處理此程式。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graphFromCSV.cs":::

## <a name="next-steps"></a>後續步驟

瞭解如何查詢 Azure 數位 Twins 對應項圖形：
* [*概念：查詢語言*](concepts-query-language.md)
* [*How to：查詢對應項圖形*](how-to-query-graph.md)