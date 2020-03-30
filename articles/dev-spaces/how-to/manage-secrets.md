---
title: 如何在使用 Azure 開發人員空間時管理祕密
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: 瞭解如何在使用 Azure 開發人員空間開發應用程式時在運行時使用 Kubernetes 機密或生成時間
keywords: Docker、Kubernetes、Azure、AKS、Azure Container Service、容器
ms.openlocfilehash: d9dd0de348612bbb3baf5fb351c1c9af1c228c1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438471"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>如何在使用 Azure 開發人員空間時管理祕密

您的服務可能需要特定密碼、連接字串和其他祕密，例如資料庫或其他安全的 Azure 服務。 藉由在組態檔中設定這些祕密的值，您可以使用這些值作為程式碼中的環境變數。  必須小心處理這些設定檔，以避免危及機密的安全性。

## <a name="storing-and-using-runtime-secrets"></a>存儲和使用運行時機密

Azure 開發人員空間提供了兩個建議的簡化選項，用於在 Azure 開發人員空間用戶端工具生成的 Helm 圖表中存儲`values.dev.yaml`機密：在檔中，並直接`azds.yaml`在 中內聯。 不建議在 中`values.yaml`存儲機密。

> [!NOTE]
> 以下方法演示如何存儲和使用用戶端工具生成的 Helm 圖表的機密。 如果您創建自己的 Helm 圖表，則可以直接使用 Helm 圖表來管理和存儲機密。

### <a name="using-valuesdevyaml"></a>使用值.dev.yaml

在已使用 Azure 開發人員空間準備的專案中，在同一資料夾中創建`values.dev.yaml`一個檔`azds.yaml`，以定義金鑰和值。 例如：

```yaml
secrets:
  redis:
    port: "6380"
    host: "contosodevredis.redis.cache.windows.net"
    key: "secretkeyhere"
```

使用`azds.yaml`驗證檔`values.dev.yaml`引用為可選。 `?` 例如：

```yaml
install:
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
```

如果您有其他機密檔，也可以在此處添加它們。

更新或驗證服務將機密引用為環境變數。 例如：

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
使用`azds up`運行更新的服務。

```console
azds up
```
 
用於`kubectl`驗證已創建您的機密。

```console
kubectl get secret --namespace default -o yaml 
```

> [!IMPORTANT]
> 不建議將機密存儲在原始程式碼管理中。 如果使用 Git，請`values.dev.yaml`添加到`.gitignore`檔中以避免在原始程式碼管理中提交機密。

### <a name="using-azdsyaml"></a>使用 azds.yaml

在已使用 Azure 開發人員空間準備的專案中，請使用*配置.dev.install.set*中的`azds.yaml`*$PLACEHOLDER*語法添加金鑰和值。 例如：

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
> 無需在 中使用`azds.yaml`*$PLACEHOLDER*語法，即可直接輸入機密值。 但是，不建議使用此方法，因為`azds.yaml`此方法存儲在原始程式碼管理中。
     
在同一`.env`資料夾中創建一個檔`azds.yaml`，以定義 *$PLACEHOLDER*值。 例如：

```
REDIS_PORT=3333
REDIS_HOST=myredishost
REDIS_KEY=myrediskey
```

> [!IMPORTANT]
> 不建議將機密存儲在原始程式碼管理中。 如果使用 Git，請`.env`添加到`.gitignore`檔中以避免在原始程式碼管理中提交機密。

更新或驗證服務將機密引用為環境變數。 例如：

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
使用`azds up`運行更新的服務。

```console
azds up
```
 
用於`kubectl`驗證已創建您的機密。

```console
kubectl get secret --namespace default -o yaml 
```

## <a name="using-secrets-as-build-arguments"></a>使用機密作為生成參數

上一節演示如何存儲和使用在容器運行時使用的秘密。 您還可以在容器生成時使用任何機密，例如使用`azds.yaml`的專用 NuGet 的密碼。

在`azds.yaml`中，使用語法設置*配置.開發.build.args*中的`<variable name>: ${secret.<secret name>.<secret key>}`生成時間機密。 例如：

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

在上面的示例中 *，mynuget 秘密*是現有機密，*而圓形圖尼*是現有金鑰。

>[!NOTE]
> 機密名稱和鍵可能包含該`.`字元。 用於`\`在將`.`機密作為生成參數傳遞時進行轉義。 例如，傳遞一個名稱為*foo.bar*的金鑰，*token*該金鑰`MYTOKEN: ${secret.foo\.bar.token}`具有權杖 ： 。 此外，可以使用首碼和尾碼文本電腦密。 例如： `MYURL: eus-${secret.foo\.bar.token}-version1` 。 此外，父級和父級空間中可用的機密可以作為生成參數傳遞。

在 Dockerfile 中，使用*ARG*指令使用機密，然後在稍後的 Dockerfile 中使用同一變數。 例如：

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
 
