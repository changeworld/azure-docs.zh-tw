---
title: 在 Log Analytics 工作區中收集 Azure 資源記錄
description: 瞭解如何將 Azure 資源記錄串流至 Azure 監視器中的 Log Analytics 工作區。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 36bd464624118b7671a3879bcc1d34114bba9ce3
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78391051"
---
# <a name="collect-azure-platform-logs-in-log-analytics-workspace-in-azure-monitor"></a>在 Azure 監視器的 Log Analytics 工作區中收集 Azure 平臺記錄
Azure 中的[平臺記錄](platform-logs-overview.md)，包括 azure 活動記錄和資源記錄，可提供 azure 資源的詳細診斷和審核資訊，以及它們所依賴的 azure 平臺。 本文說明如何在 Log Analytics 工作區中收集資源記錄，讓您可以使用功能強大的記錄查詢在 Azure 監視器記錄中收集的其他監視資料進行分析，也可以利用警示和之類的其他 Azure 監視器功能項. 


## <a name="what-you-can-do-with-platform-logs-in-a-workspace"></a>您可以使用工作區中的平臺記錄來執行的動作
將平臺記錄檔收集到 Log Analytics 工作區，可讓您同時分析所有 Azure 資源的記錄，並利用[Azure 監視器記錄](data-platform-logs.md)可用的所有功能，包括下列各項：

* **記錄查詢**-使用強大的查詢語言來建立[記錄查詢](../log-query/log-query-overview.md)，以快速分析診斷資料並取得其深入解析，並使用 Azure 監視器中的其他來源所收集的資料進行分析。
* **警示**-使用[Azure 監視器中的記錄警示](alerts-log.md)，取得資源記錄中所識別之重要條件和模式的主動式通知。
* **視覺效果**-將記錄查詢的結果釘選到 Azure 儀表板，或將它包含在活頁簿中作為互動式報表的一部分。

## <a name="prerequisites"></a>Prerequisites
您必須[建立新的工作區](../learn/quick-create-workspace.md)（如果您還沒有的話）。 工作區不一定要與資源傳送記錄位於相同的訂用帳戶中，前提是設定此設定的使用者具有這兩個訂用帳戶的適當 RBAC 存取權。

## <a name="create-a-diagnostic-setting"></a>建立診斷設定
藉由建立 Azure 資源的診斷設定，將平臺記錄傳送到 Log Analytics 工作區和其他目的地。 如需詳細資訊，請參閱[建立診斷設定以收集 Azure 中的記錄和計量](diagnostic-settings.md)。


## <a name="activity-log-collection"></a>活動記錄集合
您可以將任何單一訂用帳戶的活動記錄傳送到最多五個 Log Analytics 工作區。 在 Log Analytics 工作區中收集的資源記錄資料會儲存在**AzureActivity**資料表中。 

## <a name="resource-log-collection-mode"></a>資源記錄收集模式
在 Log Analytics 工作區中收集的資源記錄資料會儲存在資料表中，如[Azure 監視器記錄的結構](../log-query/logs-structure.md)中所述。 資源記錄所使用的資料表取決於資源所使用的集合類型：

- Azure 診斷-寫入的所有資料都是_AzureDiagnostics_資料表。
- 資源特有的資料會針對資源的每個類別目錄寫入個別的資料表。

### <a name="azure-diagnostics-mode"></a>Azure 診斷模式 
在此模式中，任何[診斷設定](diagnostic-settings.md)中的所有資料都會收集到_AzureDiagnostics_資料表中。 這是現今大部分 Azure 服務所使用的傳統方法。

由於有多個資源類型會將資料傳送至相同的資料表，因此其架構為所收集之所有不同資料類型的架構超集合。

請考慮下列範例，其中會針對下列資料類型，在相同的工作區中收集診斷設定：

- 服務1的審核記錄（具有包含 A、B 和 C 資料行的架構）  
- 服務1的錯誤記錄檔（具有由 D、E 和 F 資料行組成的架構）  
- 服務2的審核記錄（具有包含 G、H 和 I 資料行的架構）  

AzureDiagnostics 資料表看起來會像這樣：  

| ResourceProvider    | 類別     | A  | B  | C  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Service1 | AuditLogs    | x1 | y1 | z1 |    |    |    |    |    |    |
| Service1 | ErrorLogs    |    |    |    | 起 | w1 | e1 |    |    |    |
| Service2 | AuditLogs    |    |    |    |    |    |    | j1 | k1 | l1 |
| Service1 | ErrorLogs    |    |    |    | q2 | w2 | e2 |    |    |    |
| Service2 | AuditLogs    |    |    |    |    |    |    | j3 接 | k3 | 級 |
| Service1 | AuditLogs    | x5 | y5 | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>資源特定
在此模式中，會針對 [診斷] 設定中所選取的每個類別，建立所選工作區中的個別資料表。 建議使用這個方法，因為它可讓您更輕鬆地使用記錄查詢中的資料、提供更好的架構和其結構的探索能力、改善內建延遲和查詢時間的效能，以及授與的 RBAC 許可權。特定的資料表。 所有 Azure 服務最後都會遷移至資源特定模式。 

上述範例會建立三個數據表：
 
- 資料表*Service1AuditLogs* ，如下所示：

    | 資源提供者 | 類別 | A | B | C |
    | -- | -- | -- | -- | -- |
    | Service1 | AuditLogs | x1 | y1 | z1 |
    | Service1 | AuditLogs | x5 | y5 | z5 |
    | ... |

- 資料表*Service1ErrorLogs* ，如下所示：  

    | 資源提供者 | 類別 | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Service1 | ErrorLogs |  起 | w1 | e1 |
    | Service1 | ErrorLogs |  q2 | w2 | e2 |
    | ... |

- 資料表*Service2AuditLogs* ，如下所示：  

    | 資源提供者 | 類別 | G | H | I |
    | -- | -- | -- | -- | -- |
    | Service2 | AuditLogs | j1 | k1 | l1|
    | Service2 | AuditLogs | j3 接 | k3 | 級|
    | ... |



### <a name="select-the-collection-mode"></a>選取收集模式
大部分的 Azure 資源會在**Azure 診斷**或**資源特定模式下**將資料寫入工作區，而不會提供您選擇。 請參閱[每個服務的檔](diagnostic-logs-schema.md)，以取得其所使用模式的詳細資料。 所有 Azure 服務最終都會使用資源特定模式。 在此轉換過程中，有些資源可讓您選取診斷設定中的模式。 您應該為任何新的診斷設定指定資源特定模式，因為這可讓資料更容易管理，並可協助您在日後避免複雜的遷移。
  
   ![診斷設定模式選取器](media/resource-logs-collect-workspace/diagnostic-settings-mode-selector.png)




> [!NOTE]
> 目前，只有在 Azure 入口網站中設定診斷設定時，才可以選取**Azure 診斷**和**資源特定**模式。 如果您使用 CLI、PowerShell 或 Rest API 來進行設定，它會預設為**Azure 診斷**。

您可以將現有的診斷設定修改為資源特定模式。 在此情況下，已收集的資料會保留在_AzureDiagnostics_資料表中，直到根據您工作區的保留設定加以移除為止。 新的資料將會收集到專用資料表。 使用[union](https://docs.microsoft.com/azure/kusto/query/unionoperator)運算子查詢兩個數據表之間的資料。

繼續觀看[Azure 更新](https://azure.microsoft.com/updates/)blog，以取得支援資源特定模式之 azure 服務的相關公告。

### <a name="column-limit-in-azurediagnostics"></a>AzureDiagnostics 中的資料行限制
Azure 監視器記錄檔中的任何資料表都有500的屬性限制。 一旦達到此限制，任何包含資料的資料列，將會在內嵌時卸載第一個500以外的任何屬性。 *AzureDiagnostics*資料表特別容易受到此限制，因為它包含所有寫入它的 Azure 服務的屬性。

如果您要收集來自多個服務的資源記錄， _AzureDiagnostics_可能會超過此限制，而且將會遺漏資料。 在所有 Azure 服務都支援資源特定模式之前，您應該將資源設定為寫入多個工作區，以降低達到500資料行限制的可能性。

### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory，因為一組非常詳細的記錄，所以是一項服務，已知會寫入大量的資料行，而且可能會導致_AzureDiagnostics_超出其限制。 針對在啟用資源特定模式之前設定的任何診斷設定，將會針對任何活動，為每個唯一命名的使用者參數建立新的資料行。 因為活動輸入和輸出的詳細特性，所以會建立更多資料行。
 
您應該儘快遷移記錄以使用資源特定模式。 如果您無法立即這麼做，暫時的替代方法是將 Azure Data Factory 的記錄隔離到自己的工作區，以將這些記錄的機率降到最低，進而影響您工作區中所收集的其他記錄類型。


## <a name="next-steps"></a>後續步驟

* [深入瞭解資源記錄](platform-logs-overview.md)。
* [建立診斷設定以收集 Azure 中的記錄和計量](diagnostic-settings.md)。
