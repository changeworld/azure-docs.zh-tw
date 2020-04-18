---
title: 隨時隨地監視 Java 應用程式 - Azure 監視器應用程式見解
description: 無需檢測應用程式即可在任何環境中運行的 Java 應用程式的無代碼應用程式性能監視。 使用分散式跟蹤和應用程式映射查找問題 d 的根本原因。
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 5d930d349a2ab1efbd7a61904874bf6bdb411889
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641884"
---
# <a name="configuration-options---java-standalone-agent-for-azure-monitor-application-insights"></a>設定選項 - 用於 Azure 監視器應用程式來見解的 Java 獨立代理



## <a name="connection-string-and-role-name"></a>連接字串與角色名稱

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

連接字串是必需的,並且每當將不同應用程式的數據發送到同一應用程式見解資源時,角色名稱都很重要。

有關更多詳細資訊,請參閱下面的更多詳細資訊和其他配置選項。

## <a name="configuration-file-path"></a>設定檔案路徑

預設情況下,應用程式見解 Java 3.0 預覽版`ApplicationInsights.json`希望配置檔 被命名為`applicationinsights-agent-3.0.0-PREVIEW.jar`,並且應位於與的目錄中。

您可以使用以下任一

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE`環境變數,或
* `applicationinsights.configurationFile`Java 系統屬性

如果指定相對路徑,則會相對於所在的目錄解析該路徑`applicationinsights-agent-3.0.0-PREVIEW.jar`。

## <a name="connection-string"></a>連接字串

這是必填欄位。 您可以在應用程式見解資源中找到連接字串:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="應用程式見解連線字串":::

您還可以使用環境變數`APPLICATIONINSIGHTS_CONNECTION_STRING`設定連接字串。

## <a name="cloud-role-name"></a>雲角色名稱

雲角色名稱用於在應用程式映射上標記元件。

如果要設定雲角色名稱:

```json
{
  "instrumentationSettings": {
    "preview": {   
      "roleName": "my cloud role name"
    }
  }
}
```

如果未設置雲角色名稱,則應用程式見解資源的名稱將用於在應用程式映射上標記元件。

您還可以使用環境變數`APPLICATIONINSIGHTS_ROLE_NAME`設置雲角色名稱。

## <a name="cloud-role-instance"></a>雲端角色執行個體

雲角色實例預設為計算機名稱。

如果要將雲角色實例設置為不同內容,而不是計算機名稱:

```json
{
  "instrumentationSettings": {
    "preview": {
      "roleInstance": "my cloud role instance"
    }
  }
}
```

您還可以使用環境變數`APPLICATIONINSIGHTS_ROLE_INSTANCE`設置雲角色實例。

## <a name="application-log-capture"></a>應用程式記錄擷取

應用程式見解 Java 3.0 預覽版通過 Log4j、Logback 和 java.util.log 自動捕獲應用程式日誌記錄。

默認情況下,它將捕獲在`WARN`級別或以上級別執行的所有日誌記錄。

如果要變更此閾值:

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

這些是可以在`ApplicationInsights.json`檔案中`threshold`指定的有效值,以及它們如何對應於不同紀錄記錄框架中的紀錄紀錄等級:

| `threshold`  | Log4j  | Logback | 7 月     |
|--------------|--------|---------|---------|
| OFF          | OFF    | OFF     | OFF     |
| 致命        | 致命  | ERROR   | SEVERE  |
| 錯誤/嚴重 | ERROR  | ERROR   | SEVERE  |
| 警告/警告 | 警告   | 警告    | WARNING |
| INFO         | INFO   | INFO    | INFO    |
| CONFIG       | DEBUG  | DEBUG   | CONFIG  |
| 除錯/精細   | DEBUG  | DEBUG   | FINE    |
| FINER        | DEBUG  | DEBUG   | FINER   |
| 追蹤/精細 | TRACE  | TRACE   | FINEST  |
| ALL          | ALL    | ALL     | ALL     |

## <a name="jmx-metrics"></a>JMX 指標

如果您有一些 JMX 指標,您有興趣捕獲:

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

## <a name="micrometer"></a>Micrometer

默認情況下,如果應用程式使用[微數計](https://micrometer.io),應用程式見解 3.0(從 Preview.2 開始)現在將自己添加到微米全域註冊表並捕獲微米指標。

如果要關閉此功能:

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

## <a name="heartbeat"></a>Heartbeat

默認情況下,應用程式見解 Java 3.0 預覽每 15 分鐘發送一次檢測信號指標。 如果使用檢測信號指標觸發警報,則可以增加此檢測信號的頻率:

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
> 您不能降低此檢測信號的頻率,因為檢測信號數據也用於跟蹤應用程式見解使用方式。

## <a name="sampling"></a>取樣

如果需要降低成本,採樣非常有用。
採樣作為操作 ID(也稱為跟蹤 ID)上的函數執行,以便相同的操作 ID 始終導致相同的採樣決策。 這可確保在採樣分散式事務的其他部分時不會獲取該部分。

例如,如果將採樣設置為 10%,則只能看到 10% 的事務,但其中 10% 中的每一個都將具有完整的端到端事務詳細資訊。

下面是如何將取樣設定為**所有事務的 10%** 的範例 - 請確保設定適合使用方式的取樣率:

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

## <a name="http-proxy"></a>HTTP 代理

如果應用程式位於防火牆後面,並且無法直接連接到應用程式見解(請參閱[應用程式見解使用的 IP 位址](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses)),則可以將應用程式見解 Java 3.0 預覽設定為使用 HTTP 代理:

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

## <a name="self-diagnostics"></a>自診斷

"自診斷"是指來自應用程式見解 JAva 3.0 預覽的內部日誌記錄。

這有助於發現和診斷應用程式見解本身的問題。

預設情況下,它登入到與級別`warn`對應此 配置的級別 的主控台:

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

有效等級為`OFF``ERROR``WARN``INFO``DEBUG`、、、、、、`TRACE`和 。

如果要登入檔案而不是登入主控台:

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

使用檔日誌記錄時,一旦檔命中`maxSizeMB`,它將滾動,除了當前日誌檔之外,僅保留最近完成的日誌檔。
