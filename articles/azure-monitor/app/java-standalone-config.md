---
title: 隨時隨地監視 JAVA 應用程式-Azure 監視器 Application Insights
description: 無程式碼應用程式效能監視，適用于在任何環境中執行的 JAVA 應用程式，而不需要檢測應用程式。 使用分散式追蹤和應用程式對應，找出問題 d 的根本原因。
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 3a01f2e9339f3ba4f8f50fc41617fabf9da9d8cd
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87014196"
---
# <a name="configuration-options---java-standalone-agent-for-azure-monitor-application-insights"></a>設定選項-適用于 Azure 監視器的 JAVA 獨立代理程式 Application Insights



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

需要連接字串，而且當您從不同的應用程式將資料傳送至相同的 Application Insights 資源時，角色名稱很重要。

如需更多詳細資料，您可以在下方找到更多詳細資料和其他設定選項。

## <a name="configuration-file-path"></a>設定檔案路徑

根據預設，Application Insights JAVA 3.0 Preview 會預期要將設定檔命名為 `ApplicationInsights.json` ，而且必須與位於相同的目錄中 `applicationinsights-agent-3.0.0-PREVIEW.5.jar` 。

您可以使用下列其中一種方式來指定您自己的設定檔路徑

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE`環境變數，或
* `applicationinsights.configurationFile`JAVA 系統屬性

如果您指定相對路徑，它會相對於所在的目錄來解析 `applicationinsights-agent-3.0.0-PREVIEW.5.jar` 。

## <a name="connection-string"></a>連接字串

這是必填欄位。 您可以在 Application Insights 資源中找到您的連接字串：

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights 連接字串":::

您也可以使用環境變數來設定連接字串 `APPLICATIONINSIGHTS_CONNECTION_STRING` 。

## <a name="cloud-role-name"></a>雲端角色名稱

雲端角色名稱是用來標示應用程式對應上的元件。

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

Application Insights JAVA 3.0 Preview 會自動透過 Log4j、Logback 和 util 來捕獲應用程式記錄。

根據預設，它會捕獲在 `WARN` 層級或以上執行的所有記錄。

如果您想要變更此閾值：

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "logging": {
          "threshold": "ERROR"
        }
      }
    }
  }
}
```

這些是 `threshold` 您可以在檔案中指定的有效值 `ApplicationInsights.json` ，以及這些值如何對應至不同記錄架構的記錄層級：

| `threshold`  | Log4j  | Logback | 7 月     |
|--------------|--------|---------|---------|
| OFF          | OFF    | OFF     | OFF     |
| 時發生        | 時發生  | ERROR   | SEVERE  |
| 錯誤/嚴重 | ERROR  | ERROR   | SEVERE  |
| 警告/警告 | 警告   | 警告    | WARNING |
| INFO         | INFO   | INFO    | INFO    |
| CONFIG       | DEBUG  | DEBUG   | CONFIG  |
| DEBUG/精細   | DEBUG  | DEBUG   | FINE    |
| FINER        | DEBUG  | DEBUG   | FINER   |
| 追蹤/最佳 | TRACE  | TRACE   | FINEST  |
| ALL          | ALL    | ALL     | ALL     |

## <a name="jmx-metrics"></a>JMX 計量

如果您有想要捕捉的一些 JMX 計量：

```json
{
  "instrumentationSettings": {
    "preview": {
        "jmxMetrics": [
        {
          "objectName": "java.lang:type=ClassLoading",
          "attribute": "LoadedClassCount",
          "display": "Loaded Class Count"
        },
        {
          "objectName": "java.lang:type=MemoryPool,name=Code Cache",
          "attribute": "Usage.used",
          "display": "Code Cache Used"
        }
      ]
    }
  }
}
```

## <a name="micrometer-including-metrics-from-spring-boot-actuator"></a>Micrometer （包括來自春季開機傳動的計量）

如果您的應用程式使用[Micrometer](https://micrometer.io)，Application Insights 3.0 （從 Preview 開始），現在會捕獲傳送至 Micrometer 全域登錄的計量。

如果您的應用程式使用[春季開機傳動](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html)器，Application Insights 3.0 （從預覽開始），現在會捕獲春季開機傳動器所設定的計量（使用 Micrometer，但不使用 Micrometer 全域登錄）。

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

根據預設，Application Insights JAVA 3.0 Preview 每隔15分鐘就會傳送一次「心跳標準」。 如果您使用 [心跳標準] 來觸發警示，您可以增加此信號的頻率：

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
> 您無法減少此信號的頻率，因為也會使用心跳資料來追蹤 Application Insights 使用狀況。

## <a name="sampling"></a>取樣

如果您需要降低成本，取樣會很有説明。
取樣會當做作業識別碼（也稱為追蹤識別碼）的函式來執行，因此相同的作業識別碼一律會產生相同的取樣決策。 這可確保您不會取得在中取樣的分散式交易部分，而是會取樣其他部分。

例如，如果您將取樣設定為10%，您只會看到10% 的交易，但這10% 的每一個都有完整的端對端交易詳細資料。

以下範例說明如何將取樣設定為**所有交易的 10%** -請務必為您的使用案例設定正確的取樣率：

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

## <a name="http-proxy"></a>HTTP Proxy

如果您的應用程式位於防火牆後方，而且無法直接連線到 Application Insights （請參閱[Application Insights 所使用的 IP 位址](./ip-addresses.md)），您可以將 Application Insights JAVA 3.0 Preview 設定為使用 HTTP proxy：

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

根據預設，它會使用層級來記錄與 `warn` 此設定相對應的主控台：

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

有效的層級為 `OFF` 、 `ERROR` 、 `WARN` 、 `INFO` 、 `DEBUG` 和 `TRACE` 。

如果您想要記錄到檔案，而不是記錄至主控台：

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

使用檔案記錄時，一旦檔案到達之後， `maxSizeMB` 它就會變換，只保留最近完成的記錄檔，以及目前的記錄檔。
