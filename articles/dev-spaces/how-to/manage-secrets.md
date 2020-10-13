---
title: 如何在使用 Azure 開發人員空間時管理祕密
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: 瞭解如何在使用 Azure Dev Spaces 開發應用程式時，于執行或建立時使用 Kubernetes 秘密
keywords: Docker、Kubernetes、Azure、AKS、Azure Container Service、容器
ms.custom: devx-track-js
ms.openlocfilehash: b9a9ef2592e7b2aa3630f19e2bc1a47b2b9ef0f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91308719"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>如何在使用 Azure 開發人員空間時管理祕密

您的服務可能需要特定密碼、連接字串和其他祕密，例如資料庫或其他安全的 Azure 服務。 藉由在組態檔中設定這些祕密的值，您可以使用這些值作為程式碼中的環境變數。  這些設定檔必須謹慎處理，以避免危及秘密的安全性。

## <a name="storing-and-using-runtime-secrets"></a>儲存和使用執行時間秘密

Azure Dev Spaces 提供兩個建議的簡化選項，可將秘密儲存在 Azure Dev Spaces 用戶端工具所產生的 Helm 圖表中：在檔案中 `values.dev.yaml` ，直接內嵌在中 `azds.yaml` 。 不建議將秘密儲存在中 `values.yaml` 。

> [!NOTE]
> 下列方法示範如何針對用戶端工具所產生的 Helm 圖表儲存和使用秘密。 如果您建立自己的 Helm 圖，您可以直接使用 Helm 圖表來管理和儲存秘密。

### <a name="using-valuesdevyaml"></a>使用 yaml 的值

在您已經備妥 Azure Dev Spaces 的專案中，建立與 `values.dev.yaml` 相同資料夾中的檔案， `azds.yaml` 以定義您的秘密金鑰和值。 例如：

```yaml
secrets:
  redis:
    port: "6380"
    host: "contosodevredis.redis.cache.windows.net"
    key: "secretkeyhere"
```

使用，將檔案 `azds.yaml` 參考驗證 `values.dev.yaml` 為選擇性 `?` 。 例如：

```yaml
install:
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
```

如果您有其他秘密檔案，也可以在這裡新增。

更新或驗證您的服務是否以環境變數的形式參考您的秘密。 例如：

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
使用執行更新的服務 `azds up` 。

```console
azds up
```
 
用 `kubectl` 來驗證您的秘密是否已建立。

```console
kubectl get secret --namespace default -o yaml 
```

> [!IMPORTANT]
> 不建議將秘密儲存在原始檔控制中。 如果使用 Git，請將新增至檔案， `values.dev.yaml` `.gitignore` 以避免在原始檔控制中認可秘密。

### <a name="using-azdsyaml"></a>使用 azds. yaml

在您已經備妥 Azure Dev Spaces 的專案中，請使用 [*設定*] 下的 *$PLACEHOLDER*語法來新增秘密金鑰和 `azds.yaml` 值。在中設定。 例如：

```yaml
configurations:
  develop:
    ...
    install:
      set:
        secrets:
          redis:
            port: "$REDIS_PORT"
            host: "$REDIS_HOST"
            key: "$REDIS_KEY"
```

> [!NOTE]
> 您可以直接輸入秘密值，而不需要在中使用 *$PLACEHOLDER* 語法 `azds.yaml` 。 不過，由於 `azds.yaml` 儲存在原始檔控制中，因此不建議使用這個方法。
     
在與 `.env` 相同的資料夾中建立檔案 `azds.yaml` ，以定義您的 *$PLACEHOLDER* 值。 例如：

```
REDIS_PORT=3333
REDIS_HOST=myredishost
REDIS_KEY=myrediskey
```

> [!IMPORTANT]
> 不建議將秘密儲存在原始檔控制中。 如果使用 Git，請將新增至檔案， `.env` `.gitignore` 以避免在原始檔控制中認可秘密。

更新或驗證您的服務是否以環境變數的形式參考您的秘密。 例如：

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
使用執行更新的服務 `azds up` 。

```console
azds up
```
 
用 `kubectl` 來驗證您的秘密是否已建立。

```console
kubectl get secret --namespace default -o yaml 
```

## <a name="using-secrets-as-build-arguments"></a>使用秘密作為組建引數

上一節示範如何儲存和使用秘密，以在容器執行時間使用。 您也可以使用在容器組建時使用任何秘密，例如私人 NuGet 的密碼 `azds.yaml` 。

在中 `azds.yaml` ，使用語法設定 build time *configurations.develop.build.args*的密碼。 `<variable name>: ${secret.<secret name>.<secret key>}` 例如：

```yaml
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
        MYTOKEN: ${secret.mynugetsecret.pattoken}
```

在上述範例中， *mynugetsecret* 是現有的秘密，而 *pattoken* 是現有的金鑰。

>[!NOTE]
> 秘密名稱和金鑰可能包含 `.` 字元。 將 `\` `.` 秘密傳遞為組建引數時，請使用來進行 escape。 例如，若要將名為 *foo* 的秘密與 *token*的金鑰一起傳遞： `MYTOKEN: ${secret.foo\.bar.token}` 。 此外，也可以使用前置和後置文字來評估秘密。 例如： `MYURL: eus-${secret.foo\.bar.token}-version1` 。 此外，父系和祖系空間中可用的秘密可作為組建引數傳遞。

在您的 Dockerfile 中，使用 *ARG* 指示詞來取用秘密，之後在 Dockerfile 中使用該相同的變數。 例如：

```dockerfile
...
ARG MYTOKEN
...
ARG NUGET_EXTERNAL_FEED_ENDPOINTS="{'endpointCredentials': [{'endpoint':'PRIVATE_NUGET_ENDPOINT', 'password':'${MYTOKEN}'}]}"
...
```

以這些變更來更新叢集中執行的服務。 在命令列上執行命令：

```
azds up
```

## <a name="next-steps"></a>後續步驟

現在，您可以透過這些方法安全地連線到資料庫、Azure Cache for Redis 或存取安全的 Azure 服務。
 
