---
title: 隨時隨地監視 JAVA 應用程式-Azure 監視器 Application Insights
description: 針對在任何環境中執行的 JAVA 應用程式無程式碼應用程式效能監視，而不需要檢測應用程式。 使用分散式追蹤和應用程式對應找出問題 d 的根本原因。
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: devx-track-java
ms.openlocfilehash: 9b90f8b9336111438b4b832d557d448470959255
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91537652"
---
# <a name="configuration-options---java-standalone-agent-for-azure-monitor-application-insights"></a>設定選項-適用于 Azure 監視器 Application Insights 的 JAVA 獨立代理程式



## <a name="connection-string-and-role-name"></a>連接字串和角色名稱

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
    "preview": {
      "roleName": "my cloud role name"
    }
  }
}
```

連接字串是必要的，而且當您將資料從不同的應用程式傳送到相同的 Application Insights 資源時，角色名稱很重要。

您會在下方找到更多詳細資料和其他設定選項，以取得詳細資料。

## <a name="configuration-file-path"></a>設定檔案路徑

根據預設，Application Insights JAVA 3.0 Preview 需要命名設定檔 `ApplicationInsights.json` ，且與位於相同的目錄中 `applicationinsights-agent-3.0.0-PREVIEW.5.jar` 。

您可以使用下列其中一項來指定您自己的設定檔路徑

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE` 環境變數，或
* `applicationinsights.configurationFile` JAVA 系統屬性

如果指定相對路徑，則會將其解析為相對於所在的目錄 `applicationinsights-agent-3.0.0-PREVIEW.5.jar` 。

## <a name="connection-string"></a>連接字串

這是必填欄位。 您可以在 Application Insights 資源中找到您的連接字串：

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights 連接字串":::


```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
}
```

您也可以使用環境變數來設定連接字串 `APPLICATIONINSIGHTS_CONNECTION_STRING` 。

若未設定連接字串，將會停用 JAVA 代理程式。

## <a name="cloud-role-name"></a>雲端角色名稱

雲端角色名稱可用來標記應用程式對應上的元件。

如果您想要設定雲端角色名稱：

```json
{
  "instrumentationSettings": {
    "preview": {   
      "roleName": "my cloud role name"
    }
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
  "instrumentationSettings": {
    "preview": {
      "roleInstance": "my cloud role instance"
    }
  }
}
```

您也可以使用環境變數來設定雲端角色實例 `APPLICATIONINSIGHTS_ROLE_INSTANCE` 。

## <a name="application-log-capture"></a>應用程式記錄檔捕獲

Application Insights JAVA 3.0 Preview 會自動透過 Log4j、Logback 和 util 來捕捉應用程式記錄。

依預設，它會捕獲在層級或更新版本上執行的所有記錄 `INFO` 。

如果您想要變更此閾值：

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "logging": {
          "threshold": "WARN"
        }
      }
    }
  }
}
```

您也可以使用環境變數來設定記錄閾值 `APPLICATIONINSIGHTS_LOGGING_THRESHOLD` 。

這些是 `threshold` 您可以在檔案中指定的有效值 `ApplicationInsights.json` ，以及它們如何對應至不同記錄架構之間的記錄層級：

| 臨界值   | Log4j  | Logback | 7 月     |
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

## <a name="jmx-metrics"></a>JMX 計量

如果您有想要捕捉的一些 JMX 計量：

```json
{
  "instrumentationSettings": {
    "preview": {
      "jmxMetrics": [
        {
          "objectName": "java.lang:type=Runtime",
          "attribute": "Uptime",
          "display": "JVM uptime (millis)"
        },
        {
          "objectName": "java.lang:type=MemoryPool,name=Metaspace",
          "attribute": "Usage.used",
          "display": "MetaSpace Used"
        }
      ]
    }
  }
}
```

您也可以使用環境變數來設定 JMX 計量 `APPLICATIONINSIGHTS_JMX_METRICS` 。

此環境變數內容必須是符合上述結構的 json 資料，例如 `[{"objectName": "java.lang:type=Runtime", "attribute": "Uptime", "display": "JVM uptime (millis)"}, {"objectName": "java.lang:type=MemoryPool,name=Metaspace", "attribute": "Usage.used", "display": "MetaSpace Used"}]`

## <a name="micrometer-including-metrics-from-spring-boot-actuator"></a>Micrometer (包括來自彈簧開機傳動) 的計量

如果您的應用程式使用 [Micrometer](https://micrometer.io)，Application Insights 3.0 (從預覽開始。 2) 現在會捕獲傳送至 Micrometer global 登錄的計量。

如果您的應用程式使用 [春季開機傳動](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html)程式，Application Insights 3.0 (從預覽開始。 4) 現在會使用 Micrometer，而不是使用 Micrometer 的全域登錄) ，來捕獲彈簧開機 (傳動器所設定的計量。

如果您想要停用這些功能：

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "micrometer": {
          "enabled": false
        }
      }
    }
  }
}
```

## <a name="heartbeat"></a>活動訊號

依預設，Application Insights JAVA 3.0 Preview 每隔15分鐘傳送一次「心跳」度量。 如果您使用 [統計] 計量來觸發警示，則可以增加此信號的頻率：

```json
{
  "instrumentationSettings": {
    "preview": {
      "heartbeat": {
        "intervalSeconds": 60
      }
    }
  }
}
```

> [!NOTE]
> 您無法減少此信號的頻率，因為也會使用心跳資料來追蹤 Application Insights 的使用量。

## <a name="sampling"></a>取樣

如果您需要降低成本，取樣會很有説明。
取樣是以作業識別碼上的函式來執行 (也稱為追蹤識別碼) ，因此相同的作業識別碼一律會產生相同的取樣決策。 這可確保您不會取得取樣中的分散式交易的一部分，而是將部分的部分取樣出來。

例如，如果您將取樣設定為10%，您只會看到10% 的交易，但這10% 的每一個都有完整的端對端交易詳細資料。

以下範例說明如何將取樣設定為 **所有交易的 10%** -請務必為您的使用案例設定正確的取樣率：

```json
{
  "instrumentationSettings": {
    "preview": {
      "sampling": {
        "fixedRate": {
          "percentage": 10
        }
      }
    }
  }
}
```

您也可以使用環境變數來設定取樣百分比 `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE` 。

## <a name="http-proxy"></a>HTTP Proxy

如果您的應用程式位於防火牆後方，但無法直接連線至 Application Insights (請參閱 Application Insights) 所 [使用的 IP 位址](./ip-addresses.md) ，您可以設定 Application Insights JAVA 3.0 Preview 來使用 HTTP proxy：

```json
{
  "instrumentationSettings": {
    "preview": {
      "httpProxy": {
        "host": "myproxy",
        "port": 8080
      }
    }
  }
}
```

## <a name="self-diagnostics"></a>自我診斷

「自我診斷」指的是來自 Application Insights JAVA 3.0 Preview 的內部記錄。

這有助於找出和診斷 Application Insights 本身的問題。

依預設，它會記錄層級的主控台 `warn` ，對應于此設定：

```json
{
  "instrumentationSettings": {
    "preview": {
      "selfDiagnostics": {
        "destination": "console",
        "level": "WARN"
      }
    }
  }
}
```

有效層級為 `OFF` 、、 `ERROR` `WARN` 、 `INFO` 、 `DEBUG` 和 `TRACE` 。

如果您想要記錄至檔案，而不是記錄到主控台：

```json
{
  "instrumentationSettings": {
    "preview": {
      "selfDiagnostics": {
        "destination": "file",
        "directory": "/var/log/applicationinsights",
        "level": "WARN",
        "maxSizeMB": 10
      }
    }
  }
}
```

使用檔案記錄時，一旦檔案叫用，就 `maxSizeMB` 會進行換用，除了目前的記錄檔，也會保留最近完成的記錄檔。
