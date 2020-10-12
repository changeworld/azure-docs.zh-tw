---
title: 透過 Logstash 將資料來源連線至 Azure Sentinel |Microsoft Docs
description: 瞭解如何使用 Logstash 將外部資料源中的記錄轉送至 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/10/2020
ms.author: yelevin
ms.openlocfilehash: 7fe47289dcc6b6d6af4d13b36b5c3b1dae3baaf5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89663908"
---
# <a name="use-logstash-to-connect-data-sources-to-azure-sentinel"></a>使用 Logstash 將資料來源連接到 Azure Sentinel

> [!IMPORTANT]
> 使用 Logstash 輸出外掛程式的資料內嵌目前處於公開預覽狀態。 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

針對 **Logstash 資料收集引擎**使用 Azure Sentinel 的新輸出外掛程式，您現在可以透過 Logstash 直接將您想要的任何類型記錄傳送至 Azure Sentinel 中的 log Analytics 工作區。 您的記錄會傳送至您將使用輸出外掛程式定義的自訂資料表。

若要深入瞭解如何使用 Logstash 資料收集引擎，請參閱 [Logstash](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html)使用者入門。

## <a name="overview"></a>概觀

### <a name="architecture-and-background"></a>架構和背景

![記錄檔隱藏架構的圖表。](./media/connect-logstash/logstash-architecture.png)

Logstash 引擎是由三個元件所組成：

- 輸入外掛程式：來自各種來源的自訂資料收集。
- 篩選外掛程式：根據指定的準則操作和正規化資料。
- 輸出外掛程式：自訂將收集和處理的資料傳送至不同的目的地。

> [!NOTE]
> Azure Sentinel 只支援它自己提供的輸出外掛程式。 它不支援 Azure Sentinel 的協力廠商輸出外掛程式，或任何類型的任何其他 Logstash 外掛程式。

適用于 Logstash 的 Azure Sentinel 輸出外掛程式會使用 Log Analytics HTTP 資料收集器 REST API，將 JSON 格式的資料傳送到您的 Log Analytics 工作區。 資料會內嵌至自訂記錄。

- 深入瞭解 [Log Analytics REST API](https://docs.microsoft.com/rest/api/loganalytics/create-request)。
- 深入瞭解 [自訂記錄](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-custom-logs)。

## <a name="deploy-the-azure-sentinel-output-plugin-in-logstash"></a>在 Logstash 中部署 Azure Sentinel 輸出外掛程式

### <a name="step-1-installation"></a>步驟1：安裝

Azure Sentinel 輸出外掛程式可在 Logstash 集合中取得。

- 遵循 Logstash [使用外掛程式](https://www.elastic.co/guide/en/logstash/current/working-with-plugins.html) 檔中的指示來安裝 ***Logstash-loganalytics*** 外掛程式。
   
- 如果您的 Logstash 系統沒有網際網路存取權，請遵循 Logstash [離線外掛程式管理](https://www.elastic.co/guide/en/logstash/current/offline-plugins.html) 檔中的指示來準備和使用離線外掛程式套件。  (此情況下，您必須建立另一個具有網際網路存取權的 Logstash 系統。 ) 

### <a name="step-2-configuration"></a>步驟2：設定

使用 [設定檔檔](https://www.elastic.co/guide/en/logstash/current/configuration-file-structure.html) 的 Logstash 結構中的資訊，並使用下列索引鍵和值，將 Azure Sentinel 輸出外掛程式新增至設定。  (正確的設定檔語法會顯示在資料表之後。 ) 

| 欄位名稱 | 資料類型 | 描述 |
|----------------|---------------|-----------------|
| `workspace_id` | 字串 | 輸入您的工作區識別碼 GUID。 * |
| `workspace_key` | 字串 | 輸入您的工作區主要金鑰 GUID。 * |
| `custom_log_table_name` | 字串 | 設定要內嵌記錄檔的資料表名稱。 每個輸出外掛程式只可設定一個資料表名稱。 記錄資料表將會出現 Azure Sentinel 在 [**記錄**] 底下的 [記錄檔] 中，並在 [**自訂記錄**] 分類的**資料表**中，加上 `_CL` 尾碼 |
| `endpoint` | 字串 | 選擇性欄位。 根據預設，這是 Log Analytics 端點。 使用此欄位來設定替代端點。 |
| `time_generated_field` | 字串 | 選擇性欄位。 這個屬性會覆寫 Log Analytics 中的預設 **TimeGenerated** 欄位。 在資料來源中輸入時間戳記欄位的名稱。 欄位中的資料必須符合 ISO 8601 格式的 (`YYYY-MM-DDThh:mm:ssZ`)  |
| `key_names` | array | 輸入 Log Analytics 輸出架構欄位的清單。 每個清單專案都應該以單引號括住，並以逗號分隔專案，以及以方括弧括住的整個清單。 請參閱以下的範例。 |
| `plugin_flush_interval` | 數字 | 選擇性欄位。 設定以定義訊息傳輸到 Log Analytics 之間的最大間隔 (以秒為單位) 。 預設值為 5。 |
    | `amount_resizing` | boolean | true 或 false。 啟用或停用自動調整機制，這會根據所接收的記錄資料量來調整訊息緩衝區大小。 |
| `max_items` | 數字 | 選擇性欄位。 只有在 `amount_resizing` 設為 "false" 時才適用。 用來設定訊息緩衝區大小的上限， (記錄) 。 預設值為 2000。  |

\* 您可以在工作區資源的 [ **代理程式管理**] 下，找到工作區識別碼和主要金鑰。

#### <a name="sample-configurations"></a>範例組態

以下是一些使用一些不同選項的範例設定。

- 使用 filebeat 輸入管道的基本設定：

   ```ruby
    input {
        beats {
            port => "5044"
        }
    }
    filter {
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
          workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
          workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
          custom_log_table_name => "tableName"
        }
    }
   ```
    
- 使用 tcp 輸入管道的基本設定：

   ```ruby
    input {
        tcp {
            port => "514"
            type => syslog #optional, will effect log type in table
        }
    }
    filter {
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
          workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
          workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
          custom_log_table_name => "tableName"
        }
    }
   ```
  
- Advanced configuration：

   ```ruby    
    input {
        tcp {
            port => 514
            type => syslog
        }
    }
    filter {
        grok {
            match => { "message" => "<%{NUMBER:PRI}>1 (?<TIME_TAG>[0-9]{4}-[0-9]{1,2}-[0-9]{1,2}T[0-9]{1,2}:[0-9]{1,2}:[0-9]{1,2})[^ ]* (?<HOSTNAME>[^ ]*) %{GREEDYDATA:MSG}" }
        }
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
            workspace_id => "<WS_ID>"
            workspace_key => "${WS_KEY}"
            custom_log_table_name => "logstashCustomTable"
            key_names => ['PRI','TIME_TAG','HOSTNAME','MSG']
            plugin_flush_interval => 5
        }
    } 
   ```

   > [!NOTE]
   > 請造訪輸出外掛程式 [GitHub 存放庫](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/microsoft-logstash-output-azure-loganalytics) ，以深入瞭解其內部運作、設定和效能設定。

### <a name="step-3-restart-logstash"></a>步驟3：重新開機 Logstash

### <a name="step-4-view-incoming-logs-in-azure-sentinel"></a>步驟4：在 Azure Sentinel 中查看傳入記錄

1. 確認訊息正在傳送到輸出外掛程式。

1. 在 Azure Sentinel 導覽功能表中，按一下 [ **記錄**]。 在 [ **資料表]** 標題下，展開 [ **自訂記錄** ] 類別。 在設定中，尋找並按一下您所指定的資料表名稱， (`_CL` 尾碼) 。

   :::image type="content" source="./media/connect-logstash/logstash-custom-logs-menu.png" alt-text="記錄檔隱藏自訂記錄檔的螢幕擷取畫面。":::

1. 若要查看資料表中的記錄，請使用資料表名稱做為架構來查詢資料表。

   :::image type="content" source="./media/connect-logstash/logstash-custom-logs-query.png" alt-text="記錄檔隱藏自訂記錄檔的螢幕擷取畫面。":::

## <a name="monitor-output-plugin-audit-logs"></a>監視輸出外掛程式的審核記錄

若要監視 Azure Sentinel 輸出外掛程式的連線能力和活動，請啟用適當的 Logstash 記錄檔。 請參閱 [Logstash 目錄](https://www.elastic.co/guide/en/logstash/current/dir-layout.html#dir-layout) 設定檔中的記錄檔位置。

如果您在此記錄檔中看不到任何資料，請透過輸入和篩選外掛程式在本機產生和傳送一些事件 (，) 以確定輸出外掛程式正在接收資料。 Azure Sentinel 只會支援與輸出外掛程式相關的問題。

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何使用 Logstash 將外部資料源連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 使用 [內建](tutorial-detect-threats-built-in.md) 或 [自訂](tutorial-detect-threats-custom.md) 規則開始偵測 Azure Sentinel 的威脅。
