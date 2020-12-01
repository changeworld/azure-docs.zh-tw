---
title: 從 2.x Azure 監視器升級 Application Insights JAVA
description: 從 Azure 監視器 Application Insights JAVA 2.x 升級
ms.topic: conceptual
ms.date: 11/25/2020
ms.openlocfilehash: d1d09c09afbabd40a32cbb80f1901112c37ac3da
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96354983"
---
# <a name="upgrading-from-application-insights-java-sdk-2x"></a>從 Application Insights JAVA SDK 2.x 升級

如果您已經在應用程式中使用 Application Insights JAVA SDK 2.x，則不需要將它移除。
JAVA 3.0 代理程式會偵測到它，並將您透過 JAVA SDK 2.x 傳送的任何自訂遙測進行抓取和相互關聯，同時隱藏 JAVA SDK 2.x 所執行的任何自動收集，以避免重複的遙測。

如果您使用 Application Insights 2.x 代理程式，則必須移除 `-javaagent:` 指向2.x 代理程式的 JVM 參數。

## <a name="telemetryinitializers-and-telemetryprocessors"></a>TelemetryInitializers 和 TelemetryProcessors

使用3.0 代理程式時，將不會執行 JAVA SDK 2.x TelemetryInitializers 和 TelemetryProcessors。
許多先前需要這些案例的使用案例，可以藉由設定 [自訂維度](./java-standalone-config.md#custom-dimensions) 或設定 [遙測處理器](./java-standalone-telemetry-processors.md)來在3.0 中解決。

## <a name="multiple-applications-in-a-single-jvm"></a>單一 JVM 中的多個應用程式

3.0 目前只支援每個執行中進程的單一 [連接字串和角色名稱](./java-standalone-config.md#connection-string-and-role-name) 。 尤其是，在相同的 tomcat 部署中，您不能使用不同的連接字串或不同的角色名稱來建立多個 tomcat web 應用程式。

## <a name="http-request-telemetry-names"></a>HTTP 要求遙測名稱

3.0 中的 HTTP 要求遙測名稱已變更為，通常會在 Application Insights 入口網站 U/X 中提供更好的匯總視圖。

不過，針對某些應用程式，您可能還是偏好在先前的遙測名稱所提供的 U/X 中的匯總視圖，在此情況下，您可以使用3.0 中的遙測處理器預覽功能來返回先前的名稱。

### <a name="to-prefix-the-telemetry-name-with-the-http-method-get-post-etc"></a>若要在遙測名稱前面加上 HTTP 方法 (`GET` 、等等 `POST` ) ：

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "attributes": [
            { "key": "http.method", "value": "" }
          ],
          "spanNames": [ "^/" ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "^(?<tempName>.*)$" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.method", "tempName" ],
          "separator": " "
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "actions": [
          { "key": "tempName", "action": "delete" }
        ]
      }
    ]
  }
}
```

### <a name="to-set-the-telemetry-name-to-the-full-url-path"></a>將遙測名稱設定為完整 URL 路徑

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.method" },
            { "key": "http.url" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.url" ]
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.method" },
            { "key": "http.url" }
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "https?://[^/]+(?<tempPath>/[^?]*)" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "name": {
          "fromAttributes": [ "tempPath" ]
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "actions": [
          { "key": "tempPath", "action": "delete" }
        ]
      }
    ]
  }
}
```
