---
title: 在 Azure 監視器中使用 Log Analytics 代理程式收集自訂記錄
description: Azure 監視器可以從 Windows 和 Linux 電腦上的文字檔收集事件。  本文說明如何定義新的自訂記錄檔，以及它們在 Azure 監視器中建立的記錄詳細資料。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/21/2020
ms.openlocfilehash: 10a2ae71d8c26d82a4a730bab3ba16e7c62d1243
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95911731"
---
# <a name="collect-custom-logs-with-log-analytics-agent-in-azure-monitor"></a>在 Azure 監視器中使用 Log Analytics 代理程式收集自訂記錄

Azure 監視器中的 Log Analytics 代理程式的自訂記錄資料來源，可讓您從 Windows 和 Linux 電腦上的文字檔收集事件。 許多應用程式會將資訊記錄到文字檔而非標準的記錄服務，例如 Windows 事件記錄檔或 Syslog。 收集之後，您可以在查詢中，將資料剖析成個別的欄位，或將收集期間的資料擷取至個別的欄位。

> [!IMPORTANT]
> 本文說明如何使用 [Log Analytics 代理程式](log-analytics-agent.md) 收集自訂記錄，這是 Azure 監視器所使用的其中一個代理程式。 其他代理程式會收集不同的資料，並以不同的方式進行設定。 如需可用的代理程式清單和可收集的資料，請參閱 [Azure 監視器代理](agents-overview.md) 程式的總覽。

![自訂記錄檔收集](media/data-sources-custom-logs/overview.png)

要收集的記錄檔必須符合下列準則。

- 記錄檔必須是每行一個項目，或在每個項目開頭使用符合下列其中一種格式的時間戳記。

    YYYY-MM-DD HH:MM:SS <br>M/D/YYYY HH:MM:SS AM/PM<br>Mon DD, YYYY HH:MM:SS<br />yyMMdd HH:mm:ss<br />ddMMyy HH:mm:ss<br />MMM d hh:mm:ss<br />dd/MMM/yyyy:HH:mm:ss zzz<br />yyyy-MM-ddTHH:mm:ssK

- 記錄檔不得使用會以新的項目覆寫檔案的循環記錄或記錄輪替。
- 記錄檔必須使用 ASCII 或 UTF-8 編碼。  不支援其他格式，例如 UTF-16。
- 針對 Linux，記錄中的時間戳記不支援時區 converesion。

>[!NOTE]
> 如果記錄檔中有重複的項目，Azure 監視器會收集這些項目。 不過，查詢結果會不一致，篩選結果所顯示的事件會比結果計數更多。 您必須驗證記錄，以判定建立該記錄的應用程式是否導致此行為，若可以的話，請先處理此問題，再建立自訂記錄集合定義。  
>

>[!NOTE]
> Log Analytics 工作區支援下列限制：
> 
> * 只能建立 500 個自訂記錄。
> * 一個資料表最多只支援 500 個資料行。 
> * 資料行名稱的字元數目上限為 500。 
>

>[!IMPORTANT]
>自訂記錄檔收集要求寫入記錄檔的應用程式會定期將記錄內容排清到磁片。 這是因為自訂記錄檔收集需要追蹤記錄檔的檔案系統變更通知。

## <a name="defining-a-custom-log"></a>定義自訂記錄檔
使用下列程序來定義自訂記錄檔。  如需新增自訂記錄檔之範例的逐步解說，請捲動到本文結尾處。

### <a name="step-1-open-the-custom-log-wizard"></a>步驟 1： 開啟自訂記錄檔精靈
自訂記錄檔精靈會在 Azure 入口網站中執行，並可讓您定義要收集的新自訂記錄檔。

1. 在 Azure 入口網站中，選取 [Log Analytics 工作區] > 您的工作區 > [進階設定]。
2. 按一下 [**資料**  >  **自訂記錄**]。
3. 根據預設，所有組態變更都會自動發送給所有代理程式。 若是 Linux 代理程式，組態檔會傳送給 Fluentd 資料收集器。
4. 按一下 [新增+]  開啟自訂記錄檔精靈。

### <a name="step-2-upload-and-parse-a-sample-log"></a>步驟 2： 上傳和剖析範例記錄檔
一開始您要上傳自訂記錄檔的範例。  精靈會剖析並顯示此檔案中的項目以供您驗證。  Azure 監視器會使用您指定的分隔符號來識別每一筆記錄。

**新行字元** 是預設的分隔符號，並且會用於每行一個項目的記錄檔。  如果一行的開頭是其中一種可用格式的日期和時間，則您可以指定 **時間戳記** 分隔符號，其可支援跨越多行的多個項目。

如果使用時間戳記分隔符號，則儲存在 Azure 監視器中的每一筆記錄的 TimeGenerated 屬性將會填入針對記錄檔中的該項目指定的日期/時間。  如果使用新行字元分隔符號，則 TimeGenerated 會填入 Azure 監視器收集項目的日期和時間。

1. 按一下 [瀏覽]  並瀏覽至範例檔案。  請注意，在某些瀏覽器中，這個按鈕可能標示為 [選擇檔案]  。
2. 按 [下一步] 。
3. 自訂記錄檔精靈會上傳檔案並列出其識別的記錄。
4. 變更用來識別新記錄的分隔符號，並選取最能識別記錄檔中的記錄的分隔符號。
5. 按 [下一步] 。

### <a name="step-3-add-log-collection-paths"></a>步驟 3： 新增記錄檔收集路徑
您必須在代理程式上定義一個或多個它可以在其中找到自訂記錄檔的路徑。  您可以提供該記錄檔的特定路徑和名稱，或者您可以使用萬用字元為該名稱指定路徑。 這可支援每天會建立一個新檔案的應用程式或在一個檔案到達特定大小時提供支援。 您也可以為單一記錄檔提供多個路徑。

例如，應用程式可能會每天建立一個記錄檔，且名稱中會包含日期，如同 log20100316.txt。 這類記錄檔的模式可能是 *log\*.txt*，而這會套用到任何遵循應用程式命名配置的記錄檔。

下表提供可用來指定不同記錄檔的有效模式範例。

| 描述 | 路徑 |
|:--- |:--- |
| Windows 代理程式上的 *C:\Logs* 中，副檔名為 .txt 的所有檔案 |C:\Logs\\\*.txt |
| Windows 代理程式上的 *C:\Logs* 中，名稱開頭為 log 且副檔名為 .txt 的所有檔案 |C:\Logs\log\*.txt |
| Windows 代理程式上的 */var/log/audit* 中副檔名為 .txt 的所有檔案 |/var/log/audit/*.txt |
| Linux 代理程式上的 */var/log/audit* 中，名稱開頭為 log 且副檔名為 .txt 的所有檔案 |/var/log/audit/log\*.txt |

1. 選取 Windows 或 Linux 以指定您要新增的路徑格式。
2. 在路徑中輸入，然後按一下 **+** 按鈕。
3. 針對任何其他路徑重複此程序。

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>步驟 4： 提供記錄檔的名稱和描述
您指定的名稱將用於如上所述的記錄檔類型。  它一定會以 _CL 結尾以將自己辨別為自訂記錄檔。

1. 輸入記錄檔的名稱。  會自動提供 **\_ CL** 尾碼。
2. 新增選擇性的 [描述] 。
3. 按 [下一步]  來儲存自訂記錄檔的定義。

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>步驟 5。 驗證會收集自訂記錄
最多可能需要一小時的時間，新的自訂記錄檔中的初始資料才會出現在 Azure 監視器中。  從您定義自訂記錄之後，它就會開始從您指定的路徑中所找到的記錄收集項目。  它不會保留您在建立自訂記錄檔期間上傳的項目，但它會收集它所找出之記錄檔中既有的項目。

一旦 Azure 監視器開始從自訂記錄檔收集，就能透過記錄檔查詢取得其記錄。  請使用您提供給自訂記錄檔的名稱來做為查詢中的 [類型]  。

> [!NOTE]
> 如果查詢中缺少 RawData 屬性，您可能需要先關閉再重新開啟瀏覽器。

### <a name="step-6-parse-the-custom-log-entries"></a>步驟 6. 剖析自訂記錄檔項目
整個記錄檔項目會儲存在稱為 **RawData** 的單一屬性中。  您很可能會想要將每個項目中的不同資訊片段，分成每筆記錄的個別屬性。 請參閱 [在 Azure 監視器中剖析文字資料](../log-query/parse-text.md)以了解將 **RawData** 剖析成多個屬性的選項。

## <a name="removing-a-custom-log"></a>移除自訂記錄
在 Azure 入口網站中使用下列程序來移除您先前定義的自訂記錄。

1. 從工作區 [進階設定] 的 [資料] 功能表中，選取 [自訂記錄] 以列出所有自訂記錄。
2. 按一下要移除之自訂記錄旁邊的 [移除]。

## <a name="data-collection"></a>資料收集
Azure 監視器會從每個自訂記錄檔收集新的項目，間隔大約為每 5 分鐘一次。  代理程式會記錄它在從中收集項目的每個記錄檔中的位置。  如果代理程式離線一段時間，Azure 監視器即會從上次停止的地方收集項目，即使這些項目是在代理程式離線時所建立亦同。

整個記錄檔項目的內容會寫入到稱為 **RawData** 的單一屬性。  請參閱[在 Azure 監視器中剖析文字資料](../log-query/parse-text.md)以了解將每個匯入的記錄項目剖析成多個屬性的方法。

## <a name="custom-log-record-properties"></a>自訂記錄檔記錄的屬性
自訂記錄檔記錄的類型具有您提供的記錄檔名稱和下表中的屬性。

| 屬性 | 描述 |
|:--- |:--- |
| TimeGenerated |Azure 監視器收集記錄的日期和時間。  如果記錄使用以時間為基礎的分隔符號，則這是從項目收集到的時間。 |
| SourceSystem |收集記錄的來源代理程式類型。 <br> OpsManager - Windows 代理程式，直接連線或由 System Center Operations Manager 管理 <br> Linux – 所有的 Linux 代理程式 |
| RawData |所收集項目的完整文字。 您很可能會想要[將此資料剖析成個別屬性](../log-query/parse-text.md)。 |
| ManagementGroupName |System Center Operations Manager 代理程式的管理群組名稱。  若為其他代理程式，此為 AOI-\<workspace ID\> |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>新增自訂記錄檔的範例逐步解說
下面的章節會逐步解說建立自訂記錄檔的範例。  所收集的範例記錄在每一行只有一個項目，其開頭為日期和時間，然後是以逗號分隔的程式碼、狀態及訊息欄位。  下面顯示了幾個範例項目。

```output
2019-08-27 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2019-08-27 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2019-08-27 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2019-08-27 01:38:22 302,Error,Application could not connect to database
2019-08-27 01:31:34 303,Error,Application lost connection to database
```

### <a name="upload-and-parse-a-sample-log"></a>上傳和剖析範例記錄檔
我們提供其中一個記錄檔，並可以看到將會收集的事件。  在此案例中，新行字元足以做為分隔符號。  但如果記錄檔中的單一項目跨越多行，就必須使用時間戳記分隔符號。

![上傳和剖析範例記錄檔](media/data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>新增記錄檔收集路徑
記錄將位於 *C:\MyApp\Logs*。  每一天都會建立一個新檔案，且其名稱中包括「appYYYYMMDD.log」 模式的日期。  此記錄檔的足夠模式將會 *是 \\ \* C:\MyApp\Logs*。

![記錄檔收集路徑](media/data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>提供記錄檔的名稱和描述
我們使用 *MyApp_CL* 這個名稱並輸入 [描述]。

![記錄檔名稱](media/data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>驗證會收集自訂記錄
我們使用 *MyApp_CL* 的簡單查詢來傳回所收集記錄檔中的所有記錄。

![無自訂欄位的記錄檔查詢](media/data-sources-custom-logs/query-01.png)


## <a name="alternatives-to-custom-logs"></a>自訂記錄的替代方案
當您的資料符合上述的準則時，自訂記錄會很有用，但在下列情況下，您需要另一種策略：

- 資料不符合所需的結構，例如有不同格式的時間戳記。
- 記錄檔不符合需求，例如檔案編碼或不受支援的資料夾結構。
- 資料需要前置處理或收集前篩選。 

在使用自訂記錄無法收集資料的情況下，請考慮下列的替代策略：

- 使用自訂指令碼或其他方法，將資料寫入至「Azure 監視器」所收集的 [Windows 事件](data-sources-windows-events.md)或 [Syslog](data-sources-syslog.md)。 
- 使用 [HTTP 資料收集器 API](data-collector-api.md) 將資料直接傳送給「Azure 監視器」。 

## <a name="next-steps"></a>後續步驟
* 請參閱[在 Azure 監視器中剖析文字資料](../log-query/parse-text.md)以了解將每個匯入的記錄項目剖析成多個屬性的方法。
* 了解[記錄查詢](../log-query/log-query-overview.md)，以分析從資料來源和解決方案收集到的資料。
