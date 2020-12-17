---
title: 在 Azure 監視器中使用 Log Analytics Linux 代理程式收集自訂 JSON 資料來源
description: 您可以使用 Log Analytics Linux 代理程式將自訂 JSON 資料來源收集到 Azure 監視器中。  這些自訂資料來源可以是會傳回 JSON 的簡單指令碼，例如 curl 或 FluentD 的 300 個以上的外掛程式之一。 本文說明此資料收集所需的設定。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: aa6931fc91838173a856ef500145fa49f2606271
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655198"
---
# <a name="collecting-custom-json-data-sources-with-the-log-analytics-agent-for-linux-in-azure-monitor"></a>在 Azure 監視器中使用 Log Analytics Linux 代理程式收集自訂 JSON 資料來源
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

您可以使用 Log Analytics Linux 代理程式將自訂 JSON 資料來源收集到 [Azure 監視器](data-platform.md)中。  這些自訂資料來源可以是傳回 JSON 的簡單腳本，例如 [捲曲](https://curl.haxx.se/) 或 [FluentD 的300個以上的外掛程式](https://www.fluentd.org/plugins/all)之一。 本文說明此資料收集所需的設定。


> [!NOTE]
> 需要有 Log Analytics Linux 代理程式 v1.1.0-217+ 才能收集自訂 JSON 資料

## <a name="configuration"></a>組態

### <a name="configure-input-plugin"></a>設定輸入外掛程式

若要在 Azure 監視器中收集 JSON 資料，請將 `oms.api.` 新增至輸入外掛程式中的 FluentD 標記開頭。

例如，以下是 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` 中的個別設定檔 `exec-json.conf`。  這會使用 FluentD 外掛程式 `exec` 每隔 30 秒執行一次 curl 命令。  此命令的輸出由 JSON 輸出外掛程式所收集。

```xml
<source>
  type exec
  command 'curl localhost/json.output'
  format json
  tag oms.api.httpresponse
  run_interval 30s
</source>

<match oms.api.httpresponse>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api_httpresponse*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

對於在 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` 下新增的設定檔，必須使用下列命令變更其擁有權。

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>設定輸出外掛程式 
將下列輸出外掛程式設定新增至 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` 中的主要設定，或新增為個別的設定檔再放入 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` 中

```xml
<match oms.api.**>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

### <a name="restart-log-analytics-agent-for-linux"></a>重新啟動 Log Analytics Linux 代理程式
使用下列命令重新啟動 Log Analytics Linux 代理程式服務。

```console
sudo /opt/microsoft/omsagent/bin/service_control restart 
```

## <a name="output"></a>輸出
資料將收集到 Azure 監視器中，記錄類型為 `<FLUENTD_TAG>_CL`。

例如，Azure 監視器中的自訂標籤 `tag oms.api.tomcat`，記錄類型為 `tomcat_CL`。  您可以使用下列記錄查詢來擷取此類型的所有記錄。

```console
Type=tomcat_CL
```

支援巢狀 JSON 資料來源，但編製索引是以父欄位作為基礎。 例如，下列 JSON 資料從記錄查詢傳回為 `tag_s : "[{ "a":"1", "b":"2" }]`。

```json
{
    "tag": [{
      "a":"1",
      "b":"2"
    }]
}
```


## <a name="next-steps"></a>後續步驟
* 了解[記錄查詢](../log-query/log-query-overview.md)，以分析從資料來源和解決方案收集到的資料。 
