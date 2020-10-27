---
title: Azure API 管理 Dapr 整合原則 |Microsoft Docs
description: 深入瞭解與 Dapr 微服務延伸模組互動的 Azure API 管理原則。
author: vladvino
ms.author: vlvinogr
ms.date: 10/23/2020
ms.topic: article
ms.service: api-management
ms.openlocfilehash: 2bf9c4d233cfad454d63da4dce30a38af80d24ab
ms.sourcegitcommit: d3c3f2ded72bfcf2f552e635dc4eb4010491eb75
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92558392"
---
# <a name="api-management-dapr-integration-policies"></a>API 管理 Dapr 整合原則

本主題提供 Dapr 整合 API 管理原則的參考。 Dapr 是可移植的執行時間，可使用任何語言或架構建立無狀態和具狀態的微服務型應用程式。 它會會制訂常見的微服務模式（例如服務探索和使用內建重試邏輯的調用）、使用至少一次的傳遞語義來發佈和訂閱，或可插入的系結資源，以使用外部服務輕鬆撰寫。 如需有關如何開始使用 Dapr 的詳細資訊和指示，請移至 [dapr.io](https://dapr.io) 。 如需有關新增和設定原則的資訊，請參閱 [API 管理中的原則](api-management-howto-policies.md)。

> [!CAUTION]
> 本主題所參考的原則處於公開預覽狀態，受限於 [Microsoft Azure 預覽版的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

> [!IMPORTANT]
> 本主題所參考的原則僅適用于已啟用 Dapr 支援 [之 API 管理閘道的自我裝載版本](self-hosted-gateway-overview.md) 。

## <a name="enable-dapr-support-in-the-self-hosted-gateway"></a>在自我裝載閘道中啟用 Dapr 支援

若要在自我裝載閘道中開啟 Dapr 支援，請將下列 [Dapr 注釋](https://github.com/dapr/docs/blob/master/howto/configure-k8s/README.md) 新增至 [Kubernetes 部署範本](how-to-deploy-self-hosted-gateway-kubernetes.md) ，並將 "app-name" 取代為所需的名稱。 您可以 [在這裡](https://aka.ms/apim/dapr/walkthru)找到使用 DAPR 進行 API 管理設定和使用的完整逐步解說。
```yml
template:
    metadata:
      labels:
        app: app-name
      annotations:
        dapr.io/enabled: "true"
        dapr.io/app-id: "app-name"
```


## <a name="distributed-application-runtime-dapr-integration-policies"></a>分散式應用程式執行階段 (Dapr) 整合原則

-  將[要求傳送至服務](api-management-dapr-policies.md#invoke)：使用 Dapr 執行時間來找出並與 Dapr 微服務可靠地通訊。 若要深入瞭解 Dapr 中的服務調用，請參閱此 [自述](https://github.com/dapr/docs/blob/master/concepts/service-invocation/README.md#service-invocation) 檔中的描述。
-  [將訊息傳送至 Pub/Sub 主題](api-management-dapr-policies.md#pubsub)：使用 Dapr runtime 將訊息發行至發佈/訂閱主題。 若要深入瞭解 Dapr 中的發佈/訂閱訊息，請參閱此 [自述](https://github.com/dapr/docs/blob/master/concepts/publish-subscribe-messaging/README.md) 檔中的描述。
-  [觸發程式輸出](api-management-dapr-policies.md#bind)系結：使用 Dapr 執行時間透過輸出系結叫用外部系統。 若要深入瞭解 Dapr 中的系結，請參閱此 [自述](https://github.com/dapr/docs/blob/master/concepts/bindings/README.md) 檔中的描述。

## <a name="send-request-to-a-service"></a><a name="invoke"></a> 將要求傳送至服務

此原則會設定目前要求的目標 URL，以將 `http://localhost:3500/v1.0/invoke/{app-id}/method/{method-name}` 範本參數取代為原則語句中指定的值。

此原則假設 Dapr 在與閘道相同的 pod 中執行于側車容器內。 收到要求時，Dapr 執行時間會執行服務探索和實際的調用，包括 HTTP 與 gRPC 之間可能的通訊協定轉譯、重試、分散式追蹤和錯誤處理。

### <a name="policy-statement"></a>原則陳述式

```xml
<set-backend-service backend-id="dapr" dapr-app-id="app-id" dapr-method="method-name" />
```

### <a name="examples"></a>範例

#### <a name="example"></a>範例

下列範例示範在名為 "echo" 的微服務上叫用名為 "back" 的方法。 `set-backend-service`原則會設定目的地 URL。 `forward-request`原則會將要求分派至 Dapr 執行時間，以將其傳遞至微服務。

`forward-request`為了清楚起見，此原則如下所示。 原則通常是透過關鍵詞從全域範圍「繼承」 `base` 。

```xml
<policies>
    <inbound>
        <base />
        <set-backend-service backend-id="dapr" dapr-app-id="echo" dapr-method="back" />
    </inbound>
    <backend>
        <forward-request />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
    </on-error>
</policies>
```

### <a name="elements"></a>元素

| 元素             | 描述  | 必要 |
|---------------------|--------------|----------|
| set-backend-service | 根元素 | 是      |

### <a name="attributes"></a>屬性

| 屬性        | 描述                     | 必要 | Default |
|------------------|---------------------------------|----------|---------|
| backend-id       | 必須設定為 "dapr"           | 是      | N/A     |
| dapr-應用程式識別碼      | 目標微服務的名稱。 對應至 Dapr 中的 [appId](https://github.com/dapr/docs/blob/master/reference/api/service_invocation_api.md) 參數。| 是 | N/A |
| dapr-方法      | 要在目標微服務上叫用的方法名稱或 URL。 對應至 Dapr 中的 [方法名稱](https://github.com/dapr/docs/blob/master/reference/api/service_invocation_api.md) 參數。| 是 | N/A |

### <a name="usage"></a>使用量

此原則可用於下列原則[區段](./api-management-howto-policies.md#sections)和[範圍](./api-management-howto-policies.md#scopes)。

- **原則區段︰** inbound
- **原則範圍：** 所有範圍

## <a name="send-message-to-pubsub-topic"></a><a name="pubsub"></a> 將訊息傳送至 Pub/Sub 主題

此原則會指示 API 管理閘道將訊息傳送至 Dapr 發佈/訂閱主題。 這項原則會藉由提出 HTTP POST 要求來 `http://localhost:3500/v1.0/publish/{{pubsub-name}}/{{topic}}` 取代範本參數，並新增在 policy 語句中指定的內容來完成這項工作。

此原則假設 Dapr 執行時間是在與閘道相同的 pod 中的側車容器中執行。 Dapr 執行時間會實行 Pub/Sub 語義。

### <a name="policy-statement"></a>原則陳述式

```xml
<publish-to-dapr pubsub-name="pubsub-name" topic=”topic-name” ignore-error="false|true" response-variable-name="resp-var-name" timeout="in seconds" template=”Liquid” content-type="application/json">
    <!-- message content -->
</publish-to-dapr>
```

### <a name="examples"></a>範例

#### <a name="example"></a>範例

下列範例將示範如何將目前要求的本文傳送至「訂單」 Pub/Sub[元件](https://github.com/dapr/docs/blob/master/reference/api/pubsub_api.md#url-parameters)的「新」[主題](https://github.com/dapr/docs/blob/master/reference/api/pubsub_api.md#url-parameters)。 從 Dapr 執行時間收到的回應會儲存在 [內容](api-management-policy-expressions.md#ContextVariables) 物件中 Variables 集合的 "Dapr-response" 專案中。

例如，如果 Dapr 執行時間找不到目標主題，並以錯誤回應，則會觸發「錯誤」區段。 從 Dapr 執行時間收到的回應會逐字傳回給呼叫者。 否則 `200 OK` 會傳回預設回應。

「後端」區段是空的，而且不會將要求轉送至後端。

```xml
<policies>
     <inbound>
        <base />
        <publish-to-dapr
           pubsub-name="orders"
               topic="new"
               response-variable-name="dapr-response">
            @(context.Request.Body.As<string>())
        </publish-to-dapr>
    </inbound>
    <backend>
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
        <return-response response-variable-name="pubsub-response" />
    </on-error>
</policies>
```

### <a name="elements"></a>元素

| 元素             | 描述  | 必要 |
|---------------------|--------------|----------|
| 發行至 dapr     | 根元素 | 是      |

### <a name="attributes"></a>屬性

| 屬性        | 描述                     | 必要 | Default |
|------------------|---------------------------------|----------|---------|
| pubsub-名稱      | 目標 PubSub 元件的名稱。 對應至 Dapr 中的 [pubsubname](https://github.com/dapr/docs/blob/master/reference/api/pubsub_api.md) 參數。 如果不存在， __主題__ 屬性值必須為的形式 `pubsub-name/topic-name` 。    | 否       | None    |
| 主題            | 主題名稱。 對應至 Dapr 中的 [主題](https://github.com/dapr/docs/blob/master/reference/api/pubsub_api.md) 參數。               | 是      | N/A     |
| ignore-error     | 如果設定為， `true` 則會在從 Dapr 執行時間收到錯誤時，指示原則不要觸發「 [發生錯誤](api-management-error-handling-policies.md) 」區段 | 否 | `false` |
| response-variable-name | 要用來儲存 Dapr 執行時間回應的 [變數](api-management-policy-expressions.md#ContextVariables) 集合專案名稱 | 否 | None |
| timeout | 等候 Dapr 執行時間回應的時間 (秒) 。 的範圍可以從1到240秒。 | 否 | 5 |
| template | 用於轉換訊息內容的範本引擎。 「液體」是唯一支援的值。 | 否 | None |
| Content-Type | 訊息內容的類型。 "application/json" 是唯一支援的值。 | 否 | None |

### <a name="usage"></a>使用量

此原則可用於下列原則[區段](./api-management-howto-policies.md#sections)和[範圍](./api-management-howto-policies.md#scopes)。

- **原則區段︰** 輸入、輸出、錯誤
- **原則範圍：** 所有範圍

## <a name="trigger-output-binding"></a><a name="bind"></a> 觸發程式輸出系結

此原則會指示 API 管理閘道觸發 [輸出 Dapr 系](https://github.com/dapr/docs/blob/master/concepts/bindings/README.md)結。 這項原則會藉由提出 HTTP POST 要求來 `http://localhost:3500/v1.0/bindings/{{bind-name}}` 取代範本參數，並新增在 policy 語句中指定的內容來完成這項工作。

此原則假設 Dapr 執行時間是在與閘道相同的 pod 中的側車容器中執行。 Dapr 執行時間負責叫用系結所表示的外部資源。

### <a name="policy-statement"></a>原則陳述式

```xml
<invoke-dapr-binding name=”bind-name" operation="op-name" ignore-error="false|true" response-variable-name="resp-var-name" timeout="in seconds" template=”Liquid content-type="application/json">
    <metadata>
        <item key=”item-name”><!-- item-value --></item>
    </metadata>
    <data>
        <!-- message content -->
    </data>
</invoke-dapr-binding>
```

### <a name="examples"></a>範例

#### <a name="example"></a>範例

下列範例示範如何觸發名為「外部系統」的輸出系結，其作業名稱為「建立」、由兩個名為「來源」和「用戶端 ip」的索引鍵/值專案所組成的中繼資料，以及來自原始要求的主體。 從 Dapr 執行時間收到的回應是在 [內容](api-management-policy-expressions.md#ContextVariables) 物件中，于 Variables 集合的「系結-回應」專案中捕捉。

如果 Dapr 執行時間因某些原因而失敗，並以錯誤回應，則會觸發「錯誤」區段，並將從 Dapr 執行時間收到的回應逐字傳回給呼叫者。 否則 `200 OK` 會傳回預設回應。

「後端」區段是空的，而且不會將要求轉送至後端。

```xml
<policies>
     <inbound>
        <base />
        <invoke-dapr-binding
                      name="external-system"
                      operation="create"
                      response-variable-name="bind-response">
            <metadata>
                <item key="source">api-management</item>
                <item key="client-ip">@( context.Request.IpAddress )</item>
            </metadata>
            <data>
                @( context.Request.Body.As<string>() )
            </data>
        </invoke-dapr-binding>
    </inbound>
    <backend>
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
        <return-response response-variable-name="bind-response" />
    </on-error>
</policies>
```

### <a name="elements"></a>元素

| 元素             | 描述  | 必要 |
|---------------------|--------------|----------|
| invoke-dapr-binding | 根元素 | 是      |
| 中繼資料            | 以索引鍵/值組的形式系結特定的中繼資料。 對應至 Dapr 中的 [中繼資料](https://github.com/dapr/docs/blob/master/reference/api/bindings_api.md#invoking-output-bindings) 屬性。 | 否 |
| 資料            | 訊息內容。 對應至 Dapr 中的 [資料](https://github.com/dapr/docs/blob/master/reference/api/bindings_api.md#invoking-output-bindings) 屬性。 | 否 |


### <a name="attributes"></a>屬性

| 屬性        | 描述                     | 必要 | Default |
|------------------|---------------------------------|----------|---------|
| name            | 目標系結名稱。 必須符合 Dapr 中 [定義](https://github.com/dapr/docs/blob/master/reference/api/bindings_api.md#bindings-structure) 的系結名稱。           | 是      | N/A     |
| 作業       | 目標操作名稱 (系結特定) 。 對應至 Dapr 中的 [operation](https://github.com/dapr/docs/blob/master/reference/api/bindings_api.md#invoking-output-bindings) 屬性。 | 否 | None |
| ignore-error     | 如果設定為， `true` 則會在從 Dapr 執行時間收到錯誤時，指示原則不要觸發「 [發生錯誤](api-management-error-handling-policies.md) 」區段 | 否 | `false` |
| response-variable-name | 要用來儲存 Dapr 執行時間回應的 [變數](api-management-policy-expressions.md#ContextVariables) 集合專案名稱 | 否 | None |
| timeout | 等候 Dapr 執行時間回應的時間 (秒) 。 的範圍可以從1到240秒。 | 否 | 5 |
| template | 用於轉換訊息內容的範本引擎。 「液體」是唯一支援的值。 | 否 | None |
| Content-Type | 訊息內容的類型。 "application/json" 是唯一支援的值。 | 否 | None |

### <a name="usage"></a>使用量

此原則可用於下列原則[區段](./api-management-howto-policies.md#sections)和[範圍](./api-management-howto-policies.md#scopes)。

- **原則區段︰** 輸入、輸出、錯誤
- **原則範圍：** 所有範圍
