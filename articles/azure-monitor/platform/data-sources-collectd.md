---
title: 在 Azure 監視器中從 CollectD 收集資料 | Microsoft Docs
description: CollectD 是開放原始碼 Linux 精靈，可定期收集來自應用程式的資料和系統等級資訊。  本文提供如何在 Azure 監視器中從 CollectD 收集資料的相關資訊。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/27/2018
ms.openlocfilehash: 488f273336da05738609333f911fe3a90ba59496
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86111978"
---
# <a name="collect-data-from-collectd-on-linux-agents-in-azure-monitor"></a>在 Azure 監視器中，從 Linux 代理程式上的 CollectD 收集資料
[CollectD](https://collectd.org/) 是開放原始碼 Linux 精靈，可定期收集來自應用程式的效能計量和系統等級資訊。 範例應用程式包括 Java 虛擬機器 (JVM)、MySQL 伺服器和 Nginx。 本文提供如何在 Azure 監視器中從 CollectD 收集效能資料的相關資訊。

您可以在[外掛程式表](https://collectd.org/wiki/index.php/Table_of_Plugins)中找到可用之外掛程式的完整清單。

![CollectD 概觀](media/data-sources-collectd/overview.png)

Log Analytics Linux 代理程式包含下列 CollectD 設定，可將 CollectD 資料傳遞至 Log Analytics Linux 代理程式。

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

```xml
LoadPlugin write_http

<Plugin write_http>
   <Node "oms">
      URL "127.0.0.1:26000/oms.collectd"
      Format "JSON"
      StoreRates true
   </Node>
</Plugin>
```

此外，如果是使用 5.5 之前的 collectD 版本，請改為使用下列設定。

```xml
LoadPlugin write_http

<Plugin write_http>
   <URL "127.0.0.1:26000/oms.collectd">
      Format "JSON"
      StoreRates true
   </URL>
</Plugin>
```

CollectD 設定使用預設的 `write_http` 外掛程式，可將效能計量資料透過連接埠 26000 傳送至 Log Analytics Linux 代理程式。 

> [!NOTE]
> 如有需要，您可以將此連接埠設定為自行定義的連接埠。

Log Analytics Linux 代理程式也會在連接埠 26000 接聽 CollectD 計量，然後將它們轉換成 Azure 監視器結構描述計量。 以下是 Log Analytics Linux 代理程式組態 `collectd.conf`。

```xml
<source>
   type http
   port 26000
   bind 127.0.0.1
</source>

<filter oms.collectd>
   type filter_collectd
</filter>
```

> [!NOTE]
> CollectD 預設會設定為以10秒的[間隔](https://collectd.org/wiki/index.php/Interval)讀取值。 因為這會直接影響傳送至 Azure 監視器記錄的資料量，所以您可能需要在 CollectD 設定中微調此間隔，以在監視需求和相關成本與 Azure 監視器記錄的使用量之間取得良好平衡。

## <a name="versions-supported"></a>支援的版本
- Azure 監視器目前支援 CollectD 4.8 版和更新版本。
- 需要有 Log Analytics Linux 代理程式 v1.1.0-217 或以上才能收集 CollectD 計量。


## <a name="configuration"></a>設定
以下是在 Azure 監視器中設定收集 CollectD 資料的基本步驟。

1. 使用 write_http 外掛程式，設定 CollectD 將資料傳送至 Log Analytics Linux 代理程式。  
2. 設定 Log Analytics Linux 代理程式在適當的連接埠上接聽 CollectD 資料。
3. 重新啟動 CollectD 和 Log Analytics Linux 代理程式。

### <a name="configure-collectd-to-forward-data"></a>設定 CollectD 來轉送資料 

1. 若要將 CollectD 資料傳遞至 Log Analytics Linux 代理程式，需要將 `oms.conf` 新增至 CollectD 的設定目錄。 這個檔案的目的地取決於您電腦的 Linux 散發版本。

    如果您的 CollectD 設定目錄位於 /etc/collectd.d/：

    ```console
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf
    ```

    如果您的 CollectD 設定目錄位於 /etc/collectd/collectd.conf.d/：

    ```console
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf
    ```

    >[!NOTE]
    >如果是 5.5 以前的 CollectD 版本，您就必須修改 `oms.conf` 中的標記，如上所示。
    >

2. 將 collectd.conf 複製到所需之工作區的 omsagent 設定目錄。

    ```console
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf
    ```

3. 使用下列命令重新啟動 CollectD 和 Log Analytics Linux 代理程式。

    ```console
    sudo service collectd restart
    sudo /opt/microsoft/omsagent/bin/service_control restart
    ```

## <a name="collectd-metrics-to-azure-monitor-schema-conversion"></a>CollectD 計量至 Azure 監視器的結構描述轉換
在 Log Analytics Linux 代理程式已收集的基礎結構計量和 CollectD 所收集的新計量之間，為了維持一種熟悉的模型，我們使用下列結構描述對應：

| CollectD 計量欄位 | [Azure 監視器] 欄位 |
|:--|:--|
| `host` | 電腦 |
| `plugin` | None |
| `plugin_instance` | 執行個體名稱<br>If **plugin_instance** is *null* then InstanceName="*_Total*" |
| `type` | ObjectName |
| `type_instance` | CounterName<br>If **type_instance** is *null* then CounterName=**blank** |
| `dsnames[]` | CounterName |
| `dstypes` | None |
| `values[]` | CounterValue |

## <a name="next-steps"></a>後續步驟
* 了解[記錄查詢](../log-query/log-query-overview.md)，以分析從資料來源和解決方案收集到的資料。 
* 使用 [自訂欄位](custom-fields.md) ，以將來自 syslog 記錄的資料剖析至個別欄位。
