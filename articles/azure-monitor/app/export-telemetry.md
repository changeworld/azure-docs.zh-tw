---
title: 從 Application Insights 連續匯出遙測 | Microsoft Docs
description: 匯出診斷和使用量資料至 Microsoft Azure 中的儲存體，並從那裡下載。
ms.topic: conceptual
ms.date: 05/26/2020
ms.openlocfilehash: a6f636ce9fe30c666f08935d5830eb0c12e6cb5e
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97674132"
---
# <a name="export-telemetry-from-application-insights"></a>從 Application Insights 匯出遙測
想要讓遙測保留比標準保留期限還久的時間？ 或以某些特殊方式處理它？ 連續匯出很適合此用途。 在 Application Insights 入口網站中看見的事件，可以使用 JSON 格式匯出到 Microsoft Azure 中的儲存體。 從那裡，您可以下載資料並編寫處理所需的任何程式碼。  

> [!NOTE]
> 只有傳統 Application Insights 資源支援連續匯出。 [以工作區為基礎的 Application Insights 資源](./create-workspace-resource.md)必須使用[診斷設定](./create-workspace-resource.md#export-telemetry)。
>

在設定連續匯出之前，您可能要考慮某些替代作法︰

* 計量或搜尋索引標籤頂端的 [匯出] 按鈕，可讓您傳送資料表和圖表到 Excel 試算表。

* [分析](../log-query/log-query-overview.md) 可提供功能強大的遙測查詢語言。 它也可以匯出結果。
* 如果您想要 [在 Power BI 中探索資料](./export-power-bi.md)，不需要用到「連續匯出」也可以這麼做。
* [資料存取 REST API](https://dev.applicationinsights.io/) 可讓您以程式設計方式存取您的遙測。
* 您也可以透過 PowerShell 存取安裝程式 [連續匯出](/powershell/module/az.applicationinsights/new-azapplicationinsightscontinuousexport)。

在「連續匯出」將您的資料複製到儲存體 (資料可在此依您喜好的時間長短存放) 之後，資料仍然會在 Application Insights 中依一般的[保留期間](./data-retention-privacy.md)可供使用。

## <a name="continuous-export-advanced-storage-configuration"></a>連續匯出進階儲存體設定

連續匯出 **不支援** 下列 Azure 儲存體功能/設定：

* 使用 [VNET/Azure 儲存體防火牆](../../storage/common/storage-network-security.md)搭配 Azure Blob 儲存體。

* [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md)。

## <a name="create-a-continuous-export"></a><a name="setup"></a> 建立連續匯出

> [!NOTE]
> 應用程式無法每天匯出超過3TB 的資料。 如果匯出的每日超過3TB，將會停用匯出。 若要在沒有限制的情況下匯出，請使用 [診斷設定型匯出](#diagnostic-settings-based-export)。

1. 在您應用程式的 Application Insights 資源中，於左側 [設定] 下開啟 [連續匯出]，然後選擇 [新增]：

2. 選擇您想要匯出的遙測資料類型。

3. 建立或選取要用來儲存資料的 [Azure 儲存體帳戶](../../storage/common/storage-introduction.md)。 如需有關儲存體價格選項的詳細資訊，請瀏覽[官方定價頁面](https://azure.microsoft.com/pricing/details/storage/)。

     按一下 [新增]、[匯出目的地]、[儲存體帳戶]，然後建立新儲存區或選擇現有儲存區。

    > [!Warning]
    > 根據預設，儲存體位置將設為與您 Application Insights 資源相同的地理區域。 如果您儲存在不同的區域中，可能會產生傳輸費用。

4. 建立或選取儲存體中的容器。

> [!NOTE]
> 一旦您建立了匯出，新內嵌的資料就會開始流向 Azure Blob 儲存體。 連續匯出只會在啟用連續匯出之後，傳輸所建立/內嵌的新遙測資料。 在啟用連續匯出前已存在的所有資料都不會匯出，也不支援使用連續匯出功能來追溯匯出先前建立的資料。

資料出現在儲存體中之前可能有大約一小時的延遲。

第一次匯出完成後，您會在 Azure Blob 儲存體容器中找到類似下列的結構：(這會根據您所收集的資料而有所不同。)

|名稱 | 描述 |
|:----|:------|
| [可用性](export-data-model.md#availability) | 回報 [可用性 Web 測試](./monitor-web-app-availability.md)。  |
| [事件](export-data-model.md#events) | [TrackEvent()](./api-custom-events-metrics.md#trackevent)產生的自訂事件。 
| [例外狀況](export-data-model.md#exceptions) |回報在伺服器和瀏覽器中的 [例外狀況](./asp-net-exceptions.md) 。
| [訊息](export-data-model.md#trace-messages) | 由 [TrackTrace](./api-custom-events-metrics.md#tracktrace) 及[記錄配接器](./asp-net-trace-logs.md)傳送。
| [計量](export-data-model.md#metrics) | 由計量 API 呼叫所產生。
| [PerformanceCounters](export-data-model.md) | Application Insights 所收集的效能計數器。
| [要求](export-data-model.md#requests)| 由 [TrackRequest](./api-custom-events-metrics.md#trackrequest)傳送。 標準模組使用它回報在伺服器上測量的伺服器回應時間。| 

### <a name="to-edit-continuous-export"></a>若要編輯連續匯出

按一下 [連續匯出]，然後選取要編輯的儲存體帳戶。

### <a name="to-stop-continuous-export"></a>若要停止連續匯出

若要停止匯出，請按一下 [停用]。 再次按一下 [啟用] 時，匯出將會以新資料重新啟動。 您無法取得在停用匯出時送抵入口網站的資料。

若要永久停止匯出，請刪除它。 這麼做不會將您的資料從儲存體刪除。

### <a name="cant-add-or-change-an-export"></a>無法加入或變更匯出？
* 若要新增或變更匯出，您需要擁有者、參與者或 Application Insights 參與者存取權。 [了解角色][roles]。

## <a name="what-events-do-you-get"></a><a name="analyze"></a> 您取得什麼事件？
匯出的資料是我們從您的應用程式接收的原始遙測，只不過我們新增了從用戶端 IP 位址計算的位置資料。

[取樣](./sampling.md) 已捨棄的資料不會包含在匯出的資料中。

未包含其他計算的度量。 例如，我們不會匯出平均 CPU 使用率，但我們會匯出用以計算平均的原始遙測。

該資料也包含您曾設定之 [可用性 Web 測試](./monitor-web-app-availability.md) 的任何結果。

> [!NOTE]
> **取樣** 如果應用程式會傳送大量資料，取樣功能或許會運作，並只傳送一小部分產生的遙測。 [深入了解取樣。](./sampling.md)
>
>

## <a name="inspect-the-data"></a><a name="get"></a> 檢查資料
您可以直接在入口網站中檢查儲存體。 按一下最左側功能表中的主資料夾，在頂端顯示的 [Azure 服務]，選取 [儲存體帳戶]，選取儲存體帳戶名稱，在概觀頁面上選取服務底下的 [Blob]，最後選取容器名稱。

若要在 Visual Studio 中檢查 Azure 儲存體，請依序開啟 [檢視]、[Cloud Explorer]。 (如果您沒有該功能表命令，您需要安裝 Azure SDK：開啟 [新增專案] 對話方塊，展開 Visual C# / Cloud 並選擇 [取得 Microsoft Azure SDK for .NET]。)

當您開啟 Blob 存放區時，您會看到含有一組 Blob 檔案的容器。 衍生自您 Application Insights 的資源名稱、其檢測金鑰、遙測-類型/日期/時間之每個檔案的 URI。 (資源名稱全部小寫，而檢測金鑰會省略連字號。)

![使用適合的工具檢查 Blob 儲存區](./media/export-telemetry/04-data.png)

日期和時間為 UTC，並且是遙測存放在儲存區的時間 - 而不是產生的時間。 因此，如果您編寫程式碼來下載資料，它可以透過資料線性地移動。

以下是路徑的格式︰

```console
$"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"
```

Where

* `blobCreationTimeUtc` 是在內部暫存儲存體中建立 Blob 的時間
* `blobDeliveryTimeUtc` 是將 Blob 複製到匯出目的地儲存體的時間

## <a name="data-format"></a><a name="format"></a> 資料格式
* 每個 Blob 是包含多個以 '\n' 分隔的列的文字檔案。 它包含大約半分鐘的時間內所處理的遙測。
* 每個資料列都代表遙測資料點，例如要求或頁面檢視。
* 每列是未格式化的 JSON 文件。 如果您想要查看資料列，請在 Visual Studio 中開啟 blob，然後選擇 [**編輯**  >  **Advanced**  >  **Format File**：

   ![使用合適的工具檢視遙測](./media/export-telemetry/06-json.png)

時間期間依刻度為單位，10000 刻度 = 1 毫秒。 例如，這些值顯示從瀏覽器傳送要求用了 1 毫秒的時間，接收它用了 3 毫秒，以及在瀏覽器中處理頁面用了 1.8 秒：

```json
"sendRequest": {"value": 10000.0},
"receiveRequest": {"value": 30000.0},
"clientProcess": {"value": 17970000.0}
```

[屬性類型和值的詳細資料模型參考。](export-data-model.md)

## <a name="processing-the-data"></a>處理資料
就小型規模而言，您可以編寫一些程式碼來取出您的資料，將它讀取為試算表等等。 例如：

```csharp
private IEnumerable<T> DeserializeMany<T>(string folderName)
{
   var files = Directory.EnumerateFiles(folderName, "*.blob", SearchOption.AllDirectories);
   foreach (var file in files)
   {
      using (var fileReader = File.OpenText(file))
      {
         string fileContent = fileReader.ReadToEnd();
         IEnumerable<string> entities = fileContent.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
         foreach (var entity in entities)
         {
            yield return JsonConvert.DeserializeObject<T>(entity);
         }
      }
   }
}
```

如需較大型的程式碼範例，請參閱[使用背景工作角色][exportasa]。

## <a name="delete-your-old-data"></a><a name="delete"></a>刪除舊資料
您負責管理儲存容量，以及在必要時刪除舊資料。

## <a name="if-you-regenerate-your-storage-key"></a>如果您重新產生儲存體金鑰...
如果您變更儲存體的金鑰，連續匯出將停止運作。 您將在 Azure 帳戶中看到通知。

開啟 [連續匯出] 索引標籤，然後編輯您的匯出。 編輯 [匯出目的地]，但只保留選取相同的儲存體。 按一下 [確定] 以確認。

連續匯出將重新開始。

## <a name="export-samples"></a>匯出範例

* [使用串流分析匯出至 SQL][exportasa]
* [串流分析範例 2](export-stream-analytics.md)

就更大型規模而言，請考慮 [HDInsight](https://azure.microsoft.com/services/hdinsight/) - 雲端中的 Hadoop 叢集。 HDInsight 提供各種管理和分析巨量資料的技術，您可以使用它來處理已從 Application Insights 匯出的資料。

## <a name="q--a"></a>問答集
* *但我想要的只是一次性下載圖表。*  

    是的，您可以這麼做。 在索引標籤頂端，按一下 [匯出資料]。
* *我設定匯出，但我的儲存區中沒有資料。*

    自從設定匯出之後，Application Insights 是否從您的應用程式收到任何遙測？ 您將只會收到新資料。
* *我嘗試設定匯出，但被拒絕存取*

    如果帳戶是組織所擁有，您必須是擁有者或參與者群組的成員。
* *我是否能直接匯出到我自己的內部部署儲存區？*

    否，抱歉。 我們的匯出引擎目前僅適用於 Azure 儲存體。  
* *放置在我的儲存區中的資料量有任何限制？*

    否。 我們將持續送入資料，直到刪除匯出為止。 如果我們到達 Blob 儲存體的外部限制，將會停止，但那個限制很大。 您可以自行控制使用的儲存體數量。  
* *應該在儲存體中看到多少 Blob？*

  * 針對您選取要匯出的每個資料類型，會每分鐘建立一個新的 Blob (如果有可用的資料)。
  * 此外，針對具有高流量的應用程式，則會配置額外的分割單位。 在此情況下，每個單位會每分鐘建立一個 Blob。
* *我對我的儲存體重新產生了金鑰，或變更了容器的名稱，現在匯出沒有作用。*

    編輯匯出並開啟匯出目的地索引標籤。照舊保留選取相關的儲存體，並按一下 [確定] 來確認。 匯出將重新開始。 如果變更是在最近幾天內，您不會遺失資料。
* *我可以暫停匯出嗎？*

    是。 按一下 [停用]。

## <a name="code-samples"></a>程式碼範例

* [串流分析範例](export-stream-analytics.md)
* [使用串流分析匯出至 SQL][exportasa]
* [屬性類型和值的詳細資料模型參考。](export-data-model.md)

## <a name="diagnostic-settings-based-export"></a>以診斷設定為基礎的匯出

以診斷設定為基礎的匯出會使用與連續匯出不同的架構。 它也支援連續匯出不喜歡的功能：

* 具有 vnet、防火牆和私用連結的 Azure 儲存體帳戶。
* 匯出至事件中樞。

若要遷移至以診斷設定為基礎的匯出：

1. 停用目前的連續匯出。
2. [將應用程式遷移到以工作區為基礎](convert-classic-resource.md)。
3. [啟用診斷設定匯出](create-workspace-resource.md#export-telemetry)。 選取 [診斷設定] > 從您的 Application Insights 資源內 **新增診斷設定** 。

<!--Link references-->

[exportasa]: ./code-sample-export-sql-stream-analytics.md
[roles]: ./resources-roles-access-control.md

