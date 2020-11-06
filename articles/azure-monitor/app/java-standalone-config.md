---
title: 設定選項-Azure 監視器 Application Insights JAVA
description: Azure 監視器 Application Insights JAVA 的設定選項
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: devx-track-java
ms.openlocfilehash: 7165afd77e3f60af5e00b92c1063247325897f9f
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "94331901"
---
# <a name="configuration-options-for-azure-monitor-application-insights-java"></a>Azure 監視器 Application Insights JAVA 的設定選項

> [!WARNING]
> **如果您要從 3.0 Preview 升級**
>
> 請仔細檢查下列所有的設定選項，因為 json 結構已完全變更，除了檔案名本身的所有小寫。

## <a name="connection-string-and-role-name"></a>連接字串和角色名稱

連接字串和角色名稱是開始使用所需的最常見設定：

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  }
}
```

連接字串是必要的，而且當您將資料從不同的應用程式傳送到相同的 Application Insights 資源時，角色名稱很重要。

您會在下方找到更多詳細資料和其他設定選項。

## <a name="configuration-file-path"></a>設定檔案路徑

根據預設，Application Insights JAVA 3.0 會要求將設定檔命名為 `applicationinsights.json` ，而且位於與相同的目錄中 `applicationinsights-agent-3.0.0.jar` 。

您可以使用下列其中一項來指定您自己的設定檔路徑

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE` 環境變數，或
* `applicationinsights.configuration.file` JAVA 系統屬性

如果指定相對路徑，則會將其解析為相對於所在的目錄 `applicationinsights-agent-3.0.0.jar` 。

## <a name="connection-string"></a>連接字串

這是必填欄位。 您可以在 Application Insights 資源中找到您的連接字串：

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights 連接字串":::


```json
{
  "connectionString": "InstrumentationKey=..."
}
```

您也可以使用環境變數來設定連接字串 `APPLICATIONINSIGHTS_CONNECTION_STRING` 。

若未設定連接字串，將會停用 JAVA 代理程式。

## <a name="cloud-role-name"></a>雲端角色名稱

雲端角色名稱可用來標記應用程式對應上的元件。

如果您想要設定雲端角色名稱：

```json
{
  "role": {   
    "name": "my cloud role name"
  }
}
```

如果未設定雲端角色名稱，Application Insights 資源的名稱將會用來標示應用程式對應上的元件。

您也可以使用環境變數來設定雲端角色名稱 `APPLICATIONINSIGHTS_ROLE_NAME` 。

## <a name="cloud-role-instance"></a>雲端角色執行個體

雲端角色實例預設為電腦名稱稱。

如果您想要將雲端角色實例設定為不同的名稱，而不是電腦名稱稱：

```json
{
  "role": {
    "name": "my cloud role name",
    "instance": "my cloud role instance"
  }
}
```

您也可以使用環境變數來設定雲端角色實例 `APPLICATIONINSIGHTS_ROLE_INSTANCE` 。

## <a name="sampling"></a>取樣

如果您需要降低成本，取樣會很有説明。
取樣是以作業識別碼上的函式來執行 (也稱為追蹤識別碼) ，因此相同的作業識別碼一律會產生相同的取樣決策。 這可確保您不會取得取樣中的分散式交易的一部分，而是將部分的部分取樣出來。

例如，如果您將取樣設定為10%，您只會看到10% 的交易，但這10% 的每一個都有完整的端對端交易詳細資料。

以下範例說明如何設定取樣，以取得大約 **1/3 的所有交易** -請確定您已為您的使用案例設定正確的取樣率：

```json
{
  "sampling": {
    "percentage": 33.333
  }
}
```

您也可以使用環境變數來設定取樣百分比 `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE` 。

> [!NOTE]
> 針對取樣百分比，選擇接近 100/N 的百分比，其中 N 是整數。 目前取樣並不支援其他值。

## <a name="jmx-metrics"></a>JMX 計量

如果您想要收集一些額外的 JMX 計量：

```json
{
  "jmxMetrics": [
    {
      "name": "JVM uptime (millis)",
      "objectName": "java.lang:type=Runtime",
      "attribute": "Uptime"
    },
    {
      "name": "MetaSpace Used",
      "objectName": "java.lang:type=MemoryPool,name=Metaspace",
      "attribute": "Usage.used"
    }
  ]
}
```

`name` 這是將指派給此 JMX 計量 (可以是任何) 的度量名稱。

`objectName` 這是您想要收集之 JMX MBean 的 [物件名稱](https://docs.oracle.com/javase/8/docs/api/javax/management/ObjectName.html) 。

`attribute` 是您想要收集之 JMX MBean 內的屬性名稱。

支援數值和布林值 JMX 度量值。 布林值的 JMX 計量會對應至 `0` false 和 `1` true。

[//]: # "注意：此處未記載 APPLICATIONINSIGHTS_JMX_METRICS"
[//]: # "在 env var 中內嵌的 json 很雜亂，而且只應記載給無程式碼 attach 案例"

## <a name="custom-dimensions"></a>自訂維度

如果您想要將自訂維度新增至所有遙測：

```json
{
  "customDimensions": {
    "mytag": "my value",
    "anothertag": "${ANOTHER_VALUE}"
  }
}
```

`${...}` 可以在啟動時用來從指定的環境變數中讀取值。

## <a name="telemetry-processors-preview"></a> (預覽版的遙測處理器) 

這是預覽功能。

它可讓您設定將套用至要求、相依性和追蹤遙測的規則，例如
 * 遮罩敏感性資料
 * 有條件地加入自訂維度
 * 更新用於匯總和顯示的遙測名稱

如需詳細資訊，請參閱 [遙測處理器](./java-standalone-telemetry-processors.md) 檔。

## <a name="auto-collected-logging"></a>自動收集的記錄

Log4j、Logback 和 util 會自動檢測記錄，並且會自動收集透過這些記錄架構執行的記錄。

依預設，只有在該記錄執行于 `INFO` 層級或以上時，才會收集記錄。

如果您想要變更此集合層級：

```json
{
  "instrumentation": {
    "logging": {
      "level": "WARN"
    }
  }
}
```

您也可以使用環境變數來設定閾值 `APPLICATIONINSIGHTS_INSTRUMENTATION_LOGGING_LEVEL` 。

這些是 `level` 您可以在檔案中指定的有效值 `applicationinsights.json` ，以及它們如何對應至不同記錄架構中的記錄層級：

| 等級             | Log4j  | Logback | 7 月     |
|-------------------|--------|---------|---------|
| OFF               | OFF    | OFF     | OFF     |
| 致命             | 致命  | ERROR   | SEVERE  |
| 錯誤 (或嚴重)  | ERROR  | ERROR   | SEVERE  |
| 警告 (或警告)  | 警告   | 警告    | WARNING |
| INFO              | INFO   | INFO    | INFO    |
| CONFIG            | DEBUG  | DEBUG   | CONFIG  |
| DEBUG (或精細)    | DEBUG  | DEBUG   | FINE    |
| FINER             | DEBUG  | DEBUG   | FINER   |
| 追蹤 (或最佳)  | TRACE  | TRACE   | FINEST  |
| ALL               | ALL    | ALL     | ALL     |

## <a name="auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics"></a>自動收集的 Micrometer 計量 (包括彈簧開機傳動標準) 

如果您的應用程式使用 [Micrometer](https://micrometer.io)，則會自動收集傳送至 Micrometer global 登錄的計量。

此外，如果您的應用程式使用 [彈簧開機傳動](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html)器，則也會自動收集由彈簧開機傳動器設定的計量。

若要停用自動收集 Micrometer 計量 (包括彈簧開機傳動程式計量) ：

```json
{
  "instrumentation": {
    "micrometer": {
      "enabled": false
    }
  }
}
```

## <a name="heartbeat"></a>活動訊號

依預設，Application Insights JAVA 3.0 每隔15分鐘傳送一次「心跳」度量。 如果您使用 [統計] 計量來觸發警示，則可以增加此信號的頻率：

```json
{
  "heartbeat": {
    "intervalSeconds": 60
  }
}
```

> [!NOTE]
> 您無法減少此信號的頻率，因為也會使用心跳資料來追蹤 Application Insights 的使用量。

## <a name="http-proxy"></a>HTTP Proxy

如果您的應用程式位於防火牆後方，但無法直接連線至 Application Insights (請參閱 Application Insights) 所 [使用的 IP 位址](./ip-addresses.md) ，您可以設定 Application Insights JAVA 3.0 來使用 HTTP proxy：

```json
{
  "proxy": {
    "host": "myproxy",
    "port": 8080
  }
}
```

[//]: # "請注意，在我們支援0.10.0 的情況下，不會提供 OpenTelemetry 支援的通知，這會導致0.9.0 的重大變更"

[//]: # "# # OpenTelemetry API 1.0 之前版本的支援"

[//]: # "因為 OpenTelemetry API 尚未穩定，所以 OpenTelemetry API 1.0 版的支援已加入"
[//]: # "因此，每個版本的代理程式只支援特定的1.0 版 OpenTelemetry API"
[//]: # " () 發行 OpenTelemetry API 1.0 之後，這項限制將不適用。"

[//]: # "' ' ' json"
[//]: # "{"
[//]: # "  \"預覽 \" ： {"
[//]: # "    \"openTelemetryApiSupport \" ： true"
[//]: # "  }"
[//]: # "}"
[//]: # "```"

## <a name="self-diagnostics"></a>自我診斷

「自我診斷」指的是 Application Insights JAVA 3.0 的內部記錄。

這有助於找出和診斷 Application Insights 本身的問題。

依預設，會在檔案和主控台的層級 Application Insights JAVA 3.0 記錄 `INFO` `applicationinsights.log` ，這會對應到此設定：

```json
{
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```

`destination` 可以是、或的其中一個 `file` `console` `file+console` 。

`level` 可以是、、、、或其中之一 `OFF` `ERROR` `WARN` `INFO` `DEBUG` `TRACE` 。

`path` 可以是絕對或相對路徑。 相對路徑是針對所在的目錄來解析 `applicationinsights-agent-3.0.0.jar` 。

`maxSizeMb` 這是記錄檔變換之前的大小上限。

`maxHistory` 這是除了目前的記錄檔) 之外， (保留的記錄檔數目。

## <a name="an-example"></a>範例

這只是一個範例，說明如何使用多個元件來顯示設定檔的樣子。
請根據您的需求設定特定選項。

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  },
  "sampling": {
    "percentage": 100
  },
  "jmxMetrics": [
  ],
  "customDimensions": {
  },
  "instrumentation": {
    "logging": {
      "level": "INFO"
    },
    "micrometer": {
      "enabled": true
    }
  },
  "httpProxy": {
  },
  "preview": {
    "processors": [
    ]
  },
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```