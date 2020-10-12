---
title: 登錄 webhook 架構參考
description: 在 Azure container registry 中針對 webhook 要求的 JSON 承載的參考，會在 webhook 啟用成品推送或刪除事件時產生
ms.topic: article
ms.date: 03/05/2019
ms.openlocfilehash: 8354ef9db24d5825238155ac567d5d829f9b0d7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "74455969"
---
# <a name="azure-container-registry-webhook-reference"></a>Azure Container Registry Webhook 參考

當某些動作對產生事件的容器登錄執行時，您可以針對產生事件的容器登錄[設定 Webhook](container-registry-webhook.md)。 例如，啟用當容器映射或 Helm 圖表推送至登錄或刪除時所觸發的 webhook。 當 Webhook 觸發時，Azure Container Registry 就會發出 HTTP 或 HTTPS 要求，當中包含對您所指定端點之事件的相關資訊。 然後，您的端點就可以據此處理 webhook 並採取行動。

下列各節詳細說明受支援事件所產生的 webhook 要求之結構描述。 事件區段包含事件類型的承載結構描述、範例要求承載，以及一或多個會觸發 webhook 的範例命令。

如需設定 Azure Container Registry 的 Webhook 相關資訊，請參閱[使用 Azure Container Registry Webhook](container-registry-webhook.md)。

## <a name="webhook-requests"></a>Webhook 要求

### <a name="http-request"></a>HTTP 要求

當您設定 webhook 時，觸發的 Webhook 會使 HTTP `POST` 要求您指定的 URL 端點。

### <a name="http-headers"></a>HTTP 標頭

如果您尚未指定 webhook 的 `Content-Type` 自訂標頭，Webhook 要求就會包含 `application/json` 的 `Content-Type`。

不會有其他標頭新增至超出您可能已針對 webhook 指定的這些自訂標頭。

## <a name="push-event"></a>推送事件

當容器映像推送到存放庫時，會觸發 Webhook。

### <a name="push-event-payload"></a>推送事件裝載

|元素|類型|描述|
|-------------|----------|-----------|
|`id`|String|Webhook 事件的識別碼。|
|`timestamp`|Datetime|Webhook 事件觸發的時間。|
|`action`|String|觸發 webhook 事件的動作。|
|[目標](#target)|複雜類型|觸發 webhook 事件的事件目標。|
|[要求](#request)|複雜類型|產生 webhook 事件的要求。|

### <a name="target"></a><a name="target"></a>目標

|元素|類型|描述|
|------------------|----------|-----------|
|`mediaType`|String|參考物件的 MIME 類型。|
|`size`|Int32|內容的位元組數目。 與長度欄位相同。|
|`digest`|String|如 Registry V2 HTTP API 規格所定義的內容摘要。|
|`length`|Int32|內容的位元組數目。 [與大小相同] 欄位。|
|`repository`|String|存放庫名稱。|
|`tag`|String|映像標記名稱。|

### <a name="request"></a><a name="request"></a>請求

|元素|類型|描述|
|------------------|----------|-----------|
|`id`|String|起始事件之要求的識別碼。|
|`host`|String|登錄執行個體的外部可存取主機名稱，如內送要求上的 HTTP 主機標頭所指定。|
|`method`|String|產生事件的要求方法。|
|`useragent`|String|要求的使用者代理程式標頭。|

### <a name="payload-example-image-push-event"></a>承載範例：映射推送事件

```JSON
{
  "id": "cb8c3971-9adc-488b-xxxx-43cbb4974ff5",
  "timestamp": "2017-11-17T16:52:01.343145347Z",
  "action": "push",
  "target": {
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "size": 524,
    "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
    "length": 524,
    "repository": "hello-world",
    "tag": "v1"
  },
  "request": {
    "id": "3cbb6949-7549-4fa1-xxxx-a6d5451dffc7",
    "host": "myregistry.azurecr.io",
    "method": "PUT",
    "useragent": "docker/17.09.0-ce go/go1.8.3 git-commit/afdb6d4 kernel/4.10.0-27-generic os/linux arch/amd64 UpstreamClient(Docker-Client/17.09.0-ce \\(linux\\))"
  }
}
```

觸發映射**推送**事件 webhook 的範例[Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/)命令：

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="chart-push-event"></a>圖表推送事件

將 Helm 圖推送至存放庫時觸發的 Webhook。

### <a name="chart-push-event-payload"></a>圖表推送事件承載

|元素|類型|描述|
|-------------|----------|-----------|
|`id`|String|Webhook 事件的識別碼。|
|`timestamp`|Datetime|Webhook 事件觸發的時間。|
|`action`|String|觸發 webhook 事件的動作。|
|[目標](#helm_target)|複雜類型|觸發 webhook 事件的事件目標。|

### <a name="target"></a><a name="helm_target"></a>目標

|元素|類型|描述|
|------------------|----------|-----------|
|`mediaType`|String|參考物件的 MIME 類型。|
|`size`|Int32|內容的位元組數目。|
|`digest`|String|如 Registry V2 HTTP API 規格所定義的內容摘要。|
|`repository`|String|存放庫名稱。|
|`tag`|String|圖表標記名稱。|
|`name`|String|圖表名稱。|
|`version`|String|圖表版本。|

### <a name="payload-example-chart-push-event"></a>承載範例：圖表推送事件

```JSON
{
  "id": "6356e9e0-627f-4fed-xxxx-d9059b5143ac",
  "timestamp": "2019-03-05T23:45:31.2614267Z",
  "action": "chart_push",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

觸發**chart_push**事件 webhook 的範例[Azure CLI](/cli/azure/acr)命令：

```azurecli
az acr helm push wordpress-5.4.0.tgz --name MyRegistry
```

## <a name="delete-event"></a>刪除事件

刪除映射存放庫或資訊清單時，會觸發 Webhook。 刪除標記時不會觸發。

### <a name="delete-event-payload"></a>刪除事件裝載

|元素|類型|描述|
|-------------|----------|-----------|
|`id`|String|Webhook 事件的識別碼。|
|`timestamp`|Datetime|Webhook 事件觸發的時間。|
|`action`|String|觸發 webhook 事件的動作。|
|[目標](#delete_target)|複雜類型|觸發 webhook 事件的事件目標。|
|[要求](#delete_request)|複雜類型|產生 webhook 事件的要求。|

### <a name="target"></a><a name="delete_target"></a> 目標

|元素|類型|描述|
|------------------|----------|-----------|
|`mediaType`|String|參考物件的 MIME 類型。|
|`digest`|String|如 Registry V2 HTTP API 規格所定義的內容摘要。|
|`repository`|String|存放庫名稱。|

### <a name="request"></a><a name="delete_request"></a> 請求

|元素|類型|描述|
|------------------|----------|-----------|
|`id`|String|起始事件之要求的識別碼。|
|`host`|String|登錄執行個體的外部可存取主機名稱，如內送要求上的 HTTP 主機標頭所指定。|
|`method`|String|產生事件的要求方法。|
|`useragent`|String|要求的使用者代理程式標頭。|

### <a name="payload-example-image-delete-event"></a>承載範例：影像刪除事件

```JSON
{
    "id": "afc359ce-df7f-4e32-xxxx-1ff8aa80927b",
    "timestamp": "2017-11-17T16:54:53.657764628Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
      "repository": "hello-world"
    },
    "request": {
      "id": "3d78b540-ab61-4f75-xxxx-7ca9ecf559b3",
      "host": "myregistry.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  }
```

會觸發**刪除** 事件 webhook 的範例 [Azure CLI](/cli/azure/acr) 命令：

```azurecli
# Delete repository
az acr repository delete --name MyRegistry --repository MyRepository

# Delete image
az acr repository delete --name MyRegistry --image MyRepository:MyTag
```

## <a name="chart-delete-event"></a>圖表刪除事件

當刪除 Helm 圖或存放庫時，會觸發 Webhook。 

### <a name="chart-delete-event-payload"></a>圖表刪除事件裝載

|元素|類型|描述|
|-------------|----------|-----------|
|`id`|String|Webhook 事件的識別碼。|
|`timestamp`|Datetime|Webhook 事件觸發的時間。|
|`action`|String|觸發 webhook 事件的動作。|
|[目標](#chart_delete_target)|複雜類型|觸發 webhook 事件的事件目標。|

### <a name="target"></a><a name="chart_delete_target"></a> 目標

|元素|類型|描述|
|------------------|----------|-----------|
|`mediaType`|String|參考物件的 MIME 類型。|
|`size`|Int32|內容的位元組數目。|
|`digest`|String|如 Registry V2 HTTP API 規格所定義的內容摘要。|
|`repository`|String|存放庫名稱。|
|`tag`|String|圖表標記名稱。|
|`name`|String|圖表名稱。|
|`version`|String|圖表版本。|

### <a name="payload-example-chart-delete-event"></a>承載範例：圖表刪除事件

```JSON
{
  "id": "338a3ef7-ad68-4128-xxxx-fdd3af8e8f67",
  "timestamp": "2019-03-06T00:10:48.1270754Z",
  "action": "chart_delete",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

觸發**chart_delete**事件 webhook 的範例[Azure CLI](/cli/azure/acr)命令：

```azurecli
az acr helm delete wordpress --version 5.4.0 --name MyRegistry
```

## <a name="next-steps"></a>後續步驟

[使用 Azure Container Registry Webhook](container-registry-webhook.md)