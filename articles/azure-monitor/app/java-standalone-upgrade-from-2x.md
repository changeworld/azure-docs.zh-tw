---
title: 從 2.x Azure 監視器升級 Application Insights JAVA
description: 從 Azure 監視器 Application Insights JAVA 2.x 升級
ms.topic: conceptual
ms.date: 11/25/2020
ms.openlocfilehash: 9a0e8237d81428b1ecab95627fe106a563d2090c
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532401"
---
# <a name="upgrading-from-application-insights-java-2x-sdk"></a>從 Application Insights JAVA 2.x SDK 升級

如果您已經在應用程式中使用 Application Insights JAVA 2.x SDK，就不需要將它移除。
JAVA 3.0 代理程式會偵測到它，並與您透過 2.x SDK 傳送的任何自訂遙測進行抓取和相互關聯，同時隱藏 2.x SDK 所執行的任何自動收集以防止重複的遙測。

如果您使用 Application Insights 2.x 代理程式，則必須移除 `-javaagent:` 指向2.x 代理程式的 JVM 參數。

本檔的其餘部分將說明從2.x 升級到3.0 時可能會遇到的限制和變更，以及一些您可能會覺得有用的因應措施。

## <a name="telemetryinitializers-and-telemetryprocessors"></a>TelemetryInitializers 和 TelemetryProcessors

使用3.0 代理程式時，將不會執行 2.x SDK TelemetryInitializers 和 TelemetryProcessors。
許多先前需要這些案例的使用案例，可以藉由設定 [自訂維度](./java-standalone-config.md#custom-dimensions) 或設定 [遙測處理器](./java-standalone-telemetry-processors.md)來在3.0 中解決。

## <a name="multiple-applications-in-a-single-jvm"></a>單一 JVM 中的多個應用程式

3.0 目前只支援每個執行中進程的單一 [連接字串和角色名稱](./java-standalone-config.md#connection-string-and-role-name) 。 尤其是，在相同的 tomcat 部署中，您不能使用不同的連接字串或不同的角色名稱來建立多個 tomcat web 應用程式。

## <a name="operation-names"></a>作業名稱

3.0 中的作業名稱已變更為，通常會在 Application Insights 入口網站 U/X 中提供更好的匯總視圖。

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-3-0.png" alt-text="3.0 中的作業名稱":::

不過，針對某些應用程式，您可能仍會偏好在先前的作業名稱所提供的 U/X 中的匯總視圖，在這種情況下，您可以使用3.0 中的 [遙測處理器](./java-standalone-telemetry-processors.md) (preview) 功能來複寫先前的行為。

### <a name="prefix-the-operation-name-with-the-http-method-get-post-etc"></a>以 HTTP 方法為作業名稱加上前置詞 (`GET` 、等等 `POST` ) 

在 2.x SDK 中，作業名稱前面會加上 HTTP 方法 (`GET` 、 `POST` 等等 ) ，例如

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-prefixed-by-http-method.png" alt-text="以 HTTP 方法為首碼的作業名稱":::

下列程式碼片段會設定3個結合的遙測處理器來複寫先前的行為。
遙測處理器會依序執行下列動作 () ：

1. 第一個遙測處理器是 (具有類型) 的範圍處理器 `span` ，這表示它適用于 `requests` 和 `dependencies` 。

   它會比對具有名為之屬性的任何範圍， `http.method` 並具有以開頭的範圍名稱 `/` 。

   然後，它會將該範圍名稱解壓縮至名為的屬性 `tempName` 。

2. 第二個遙測處理器也是 span 處理器。

   它會比對具有名為之屬性的任何範圍 `tempName` 。

   然後，它會串連兩個屬性， `http.method` 並 `tempName` 以空格分隔，藉以更新範圍名稱。

3. 最後一個遙測處理器是屬性處理器 (具有類型 `attribute`) ，這表示它會套用至目前有屬性 (`requests` 和) 的所有 `dependencies` 遙測 `traces` 。

   它會比對具有名為之屬性的任何遙測 `tempName` 。

   然後，它會刪除名為的屬性 `tempName` ，如此就不會將它報告為自訂維度。

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

### <a name="set-the-operation-name-to-the-full-path"></a>將作業名稱設定為完整路徑

此外，在 2.x SDK 中，在某些情況下，作業名稱包含完整的路徑，例如

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-with-full-path.png" alt-text="具有完整路徑的作業名稱":::

下列程式碼片段會設定4個結合的遙測處理器來複寫先前的行為。
遙測處理器會依序執行下列動作 () ：

1. 第一個遙測處理器是 (具有類型) 的範圍處理器 `span` ，這表示它適用于 `requests` 和 `dependencies` 。

   它會比對具有名為之屬性的任何範圍 `http.url` 。

   然後，它會以屬性值更新範圍名稱 `http.url` 。

   這會是它的結尾，但 `http.url` 看起來像這樣 `http://host:port/path` ，但您可能只想要 `/path` 部分。

2. 第二個遙測處理器也是 span 處理器。

   它會比對具有名為 (之屬性的任何範圍，也就是 `http.url` 第一個處理器符合) 的任何範圍。

   然後，它會將範圍名稱的路徑部分解壓縮至名為的屬性 `tempName` 。

3. 第三個遙測處理器也是 span 處理器。

   它會比對具有名為之屬性的任何範圍 `tempPath` 。

   然後，它會從屬性更新範圍名稱 `tempPath` 。

4. 最後一個遙測處理器是屬性處理器 (具有類型 `attribute`) ，這表示它會套用至目前有屬性 (`requests` 和) 的所有 `dependencies` 遙測 `traces` 。

   它會比對具有名為之屬性的任何遙測 `tempPath` 。

   然後，它會刪除名為的屬性 `tempPath` ，如此就不會將它報告為自訂維度。

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
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

## <a name="dependency-names"></a>相依性名稱

3.0 中的相依性名稱也已變更，因此通常會在 Application Insights 入口網站 U/X 中提供更好的匯總視圖。

同樣地，在某些應用程式中，您可能還是偏好在先前的相依性名稱所提供的 U/X 中的匯總視圖，在這種情況下，您可以使用上述類似的技巧來複寫先前的行為。

## <a name="operation-name-on-dependencies"></a>相依性的作業名稱

先前在 2.x SDK 中，來自要求遙測的作業名稱也會設定在相依性遙測上。
Application Insights JAVA 3.0 不再填入相依性遙測的作業名稱。
如果您想要查看相依性遙測父系之要求的作業名稱，您可以撰寫記錄 (Kusto) 查詢，從相依性資料表聯結至要求資料表。
