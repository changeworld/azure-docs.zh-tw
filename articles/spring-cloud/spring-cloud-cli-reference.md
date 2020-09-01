---
title: az 春季 cloud
description: 使用 Azure CLI 管理 Azure 春天雲端
author: bmitchell287
ms.service: spring-cloud
ms.topic: reference
ms.date: 10/03/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 10f3856a7c0c0a98ab8b23e20342d745b1966b9b
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89263190"
---
# <a name="az-spring-cloud"></a>az 春季-雲端

## <a name="manage-azure-spring-cloud-using-the-azure-cli"></a>使用 Azure CLI 管理 Azure 春天雲端

| az 春季-雲端 | 命令 |
|------|------:|
| [az 春季-cloud create](#az-spring-cloud-create) | 建立 Azure 春季 Cloud 實例。 |
| [az 彈簧-雲端刪除](#az-spring-cloud-delete) | 刪除 Azure 春季 Cloud 實例。 |
| [az 春季-cloud 清單](#az-spring-cloud-list) | 列出指定資源群組中所有的 Azure 春季雲端實例，否則會列出訂用帳戶識別碼。 |
| [az 春季-cloud show](#az-spring-cloud-show) | 顯示 Azure 春季雲端的詳細資料。 |

| az 春季-cloud app | 在 Azure 春季雲端中管理應用程式的命令。  |
| ---- | ----: |
| [az 春季-cloud app create](#az-spring-cloud-app-create) | 使用 Azure 春季雲端中的預設部署來建立新的應用程式。 |
| [az 春季-cloud app delete](#az-spring-cloud-app-delete) | 刪除 Azure 春季雲端中的應用程式。 |
| [az 春季-cloud app deploy](#az-spring-cloud-app-deploy) | 從原始程式碼或預先建立的二進位檔部署到應用程式，並更新相關的設定。 |
| [az 春季-cloud app list](#az-spring-cloud-app-list) | 列出 Azure 春季雲端中的所有應用程式。 |
| [az 春季-雲端應用程式重新開機](#az-spring-cloud-app-restart) | 使用生產部署預設值重新開機應用程式的實例。 |
| [az 春季-雲端應用程式規模](#az-spring-cloud-app-scale) | 手動調整應用程式或其部署。 |
| [az 春季-cloud app set-部署](#az-spring-cloud-app-set-deployment) | 設定應用程式的生產環境部署。 |
| [az 春季-cloud app show](#az-spring-cloud-app-show) | 顯示 Azure 春季雲端中的應用程式詳細資料。 |
| [az 春季-cloud app show-deploy-log](#az-spring-cloud-app-show-deploy-log) | 顯示來源中最新部署的組建記錄檔。 預設為生產部署。 |
| [az 春季-雲端應用程式啟動](#az-spring-cloud-app-start) | 使用生產部署預設值啟動應用程式的實例。 |
| [az 春季-cloud app stop](#az-spring-cloud-app-stop) | 使用生產部署預設值停止應用程式的實例。 |
| [az 春季-cloud app update](#az-spring-cloud-app-update) | 更新指定的應用程式設定。 |

| az 春季-cloud app binding | 使用 Azure 資料服務管理系結的命令。  應用程式必須重新開機，這些設定才會生效。 |
| --- | ---: |
| [az 春季-cloud app binding 清單](#az-spring-cloud-app-binding-list) | 列出應用程式中的所有服務系結。 |
| [az 春季-cloud app binding remove](#az-spring-cloud-app-binding-remove) | 從應用程式移除服務系結。 |
| [az 春季-cloud app binding show](#az-spring-cloud-app-binding-show) | 顯示服務系結的詳細資料。 |
| [az 春季-cloud app binding cosmos add](#az-spring-cloud-app-binding-cosmos-add) | 系結 Azure CosmosDB 與應用程式。 |
| [az 春季-cloud app binding cosmos update](#az-spring-cloud-app-binding-cosmos-update) | 更新 Azure CosmosDB 服務系結。 |
| [az 春季-cloud app binding mysql add](#az-spring-cloud-app-binding-mysql-add) | 將適用於 MySQL 的 Azure 資料庫系結到應用程式。 |
| [az 春季-cloud app binding mysql update](#az-spring-cloud-app-binding-mysql-update) | 更新適用於 MySQL 的 Azure 資料庫服務系結。 |
| [az 春季-cloud app binding redis add](#az-spring-cloud-app-binding-redis-add) | 將 Azure Cache for Redis 系結到應用程式。 |
| [az 春季-cloud app binding redis update](#az-spring-cloud-app-binding-redis-update) | 更新 Azure Cache for Redis 服務系結。 |

| az 春季-雲端應用程式部署 | 在 Azure 春季雲端中管理應用程式部署生命週期的命令。 |
| --- | ---: |
| [az 春季-cloud app deployment create](#az-spring-cloud-app-deployment-create) | 建立應用程式的預備環境部署。 |
| [az 春季-雲端應用程式部署刪除](#az-spring-cloud-app-deployment-delete) | 刪除應用程式的部署。 |
| [az 春季-雲端應用程式部署清單](#az-spring-cloud-app-deployment-list) | 列出應用程式中的所有部署。 |
| [az 春季-cloud app deployment show](#az-spring-cloud-app-deployment-show) | 顯示部署的詳細資料。 |

| az 春季-cloud config-server | 用來管理 Azure 春季 Cloud Config Server 的命令。 |
| --- | ---: |
| [az 春季-cloud config-伺服器 clear](#az-spring-cloud-config-server-clear) | 清除 Config Server 中的所有設定。 |
| [az 春季-cloud config-server set](#az-spring-cloud-config-server-set) | 從 YAML 檔定義 Config Server。 |
| [az 春季-cloud config-server show](#az-spring-cloud-config-server-show) | 顯示佈建服務器設定。 |
| [az 春季-cloud config server git set](#az-spring-cloud-config-server-git-set) | 定義 Config Server 的 git 屬性。  將會覆寫先前的值。 |
| [az 春季-cloud config server git 存放庫新增](#az-spring-cloud-config-server-git-repo-add) | 將新的 git 存放庫設定新增至 Config Server。 |
| [az 春季-cloud config server git 存放庫清單](#az-spring-cloud-config-server-git-repo-list) | 列出 Config Server 的所有 git 存放庫設定。 |
| [az 春季-cloud config server git 存放庫移除](#az-spring-cloud-config-server-git-repo-remove) | 從 Config Server 中移除指定的 git 存放庫。 |

| az 春季-雲端測試-端點 | 在 Azure 春季雲端中管理端點測試的命令 |
| --- | ---: |
| [az 春季-cloud test-endpoint disable](#az-spring-cloud-test-endpoint-disable) | 停用測試端點。 |
| [az 春季-雲端測試-啟用端點](#az-spring-cloud-test-endpoint-enable) | 啟用測試端點。 |
| [az 春季-雲端測試-端點清單](#az-spring-cloud-test-endpoint-list) | 列出測試端點金鑰。 |
| [az 春季-雲端測試-端點更新金鑰](#az-spring-cloud-test-endpoint-renew-key) | 重新產生測試端點金鑰。 |

## <a name="az-spring-cloud-create"></a>az 春季-cloud create

使用 Azure 春季雲端中的預設部署來建立新的應用程式。

```azurecli
az spring-cloud create  --name -n
                        --resource-group -g
                        --location -l
                        --no-wait
```

| 必要參數 | 說明 |
| --- | :--- |
| --name-n | 此 Azure 春季雲端實例的名稱。 |
| --resource-group -g | 指定此應用程式的資源群組。  使用設定預設群組 `az configure --defaults group=<name>` |

| 選擇性參數 | 說明 |
| --- | :--- |
| --location -l | 指定此應用程式的伺服器位置。  使用尋找有效的位置 `az account list-locations` |
| --no-wait | 請勿完成長時間執行的作業。

### <a name="examples"></a>範例

在 WestUS 中建立新的 Azure 春季雲端

```azurecli
az spring-cloud create -n MyService -g MyResourceGroup -l westus
```

## <a name="az-spring-cloud-delete"></a>az 彈簧-雲端刪除

刪除 Azure 春季 Cloud 實例。

```azurecli
az spring cloud --name -n
                --resource-group -g
                --no-wait
```

| 必要參數 | 說明 |
| --- | :--- |
| --name-n | 要刪除的 Azure 春季雲端實例名稱。 |
| --resource-group -g | Azure 春季雲端所屬的資源組名。 |

| 選擇性參數 | 說明 |
| --- | :--- |
| -否-等候 | 請勿等待長時間執行的作業完成。 |

### <a name="example"></a>範例

從 ' MyResourceGroup ' 刪除名為 ' MyService ' 的 Azure 春季 Cloud 實例。

```azurecli
az spring-cloud delete -n MyService -g MyResourceGroup
```

## <a name="az-spring-cloud-list"></a>az 春季-cloud 清單

列出與指定資源群組相關聯的所有 Azure 春季雲端實例。 如果未指定任何資源群組，請列出訂用帳戶識別碼。

```azurecli
az spring-cloud list --resource-group -g
```

| 必要參數 | 說明 |
| --- | :--- |
| --resource-group -g | 資源群組的名稱。 |

## <a name="az-spring-cloud-show"></a>az 春季-cloud show

顯示指定 Azure 春季雲端實例的詳細資料。

```azurecli
az spring-cloud show --name -n
                     -- resource-group -g
```

| 必要參數 | 說明 |
| --- | :--- |
| --name-n | Azure 春季雲端實例的名稱。 |
| --resource-group -g | Azure 春季雲端實例所屬的資源組名。

## <a name="az-spring-cloud-app-create"></a>az 春季-cloud app create

在 Azure 春季雲端中建立新的應用程式。

```azurecli
az spring-cloud app create --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --enable-persistent-storage
                            --instance-count
                            --is-public
                            --memory
```

| 必要參數 | 說明 |
| --- | :--- |
| --name-n | 應用程式的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --service-s | Azure 春季雲端的名稱。  您可以使用設定預設服務 `az configure --defaults spring-cloud=<name>` 。 |

| 選擇性參數 | 說明 |
| --- | :--- |
| --cpu | 每個實例的虛擬核心數目。  預設值：1。 |
| --啟用-持續性儲存體 | 布林值。  若為 true，則裝載具有預設路徑的50GB 磁片。 |
| --instance-count | 實例的數目。  預設值：1。 |
| --為-public | 布林值。  若為 true，則指派公用網域。 |
| --memory | 每個實例的記憶體 GB 數。  預設值：1。 |

### <a name="examples"></a>範例

使用預設設定建立應用程式。

```azurecli
az spring-cloud app create -n MyApp -s MyService
```

建立具有3個實例的可公開存取應用程式。  每個實例都有 3 GB 的記憶體和2個 CPU 核心。

```azurecli
az spring-cloud app create -n MyApp -s MyService --is-public true --cpu 2 --memory 3
```

## <a name="az-spring-cloud-app-delete"></a>az 春季-cloud app delete

刪除 Azure 春季雲端中的應用程式。

```azurecli
az spring cloud app delete  --name -n
                            --resource-group -g
                            --service -s
```

| 必要參數 | 說明 |
| --- | :--- |
| --name-n | 應用程式的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --service-s | Azure 春季雲端的名稱。  您可以使用設定預設服務 `az configure --defaults spring-cloud=<name>` 。 |

## <a name="az-spring-cloud-app-deploy"></a>az 春季-cloud app deploy

從原始程式碼或預先建立的二進位檔將應用程式部署到 Azure 春季雲端，並更新相關的設定。

```azurecli
az spring cloud app deploy  --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --deployment -d
                            --env
                            --instance-count
                            --jar-path
                            --jvm-options
                            --memory
                            --no-wait
                            --runtime-version
                            --target-module
                            --version
```

| 必要參數 | 說明 |
| --- | :--- |
| --name-n | 應用程式的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --service-s | Azure 春季雲端的名稱。  您可以使用設定預設服務 `az configure --defaults spring-cloud=<name>` 。 |

| 選擇性參數 | 說明 |
| --- | :--- |
| --cpu | 每個實例的虛擬 CPI 核心數目。 |
| --部署-d | 現有應用程式部署的名稱。  如果未指定，則預設為生產部署。 |
| --env | 以空格分隔的環境變數（以 ' key [= value] ' 格式表示）。 |
| --instance-count | 實例數目。 |
| --jar-path | 如果有提供，請從指定的路徑部署 jar。 否則，請將目前的資料夾部署為 tar。 |
| --jvm-選項 | 包含 JVM 選項的字串。  請使用 ' = ' 而不是 ' '，以避免發生 shell 剖析錯誤。 例如，  `--jvm-options='-Xms1024m -Xmx2048m` 。 |
| --memory | 每個實例的記憶體 GB 數。 |
| --no-wait | 請勿等待長時間執行的作業完成。 |
| --執行階段版本 | 應用程式中所使用之語言的執行階段版本。  允許的值： `Java_11` 、 `Java_8` 。 |
| --目的模組 | 要部署的子模組。  從原始程式碼建立多個 jar 套件時需要。 |
| --version | 部署版本。  如果未設定，則為未變更。 |

### <a name="examples"></a>範例

將原始程式碼部署到應用程式。 這將會封裝目前的目錄、使用 Pivotal 組建服務建立二進位檔，然後部署到應用程式。

```azurecli
az spring-cloud app deploy -n MyApp -s MyService
```

使用 JVM 選項和環境變數，將預先建立的 jar 部署到應用程式。

```azurecli
az spring-cloud app deploy -n MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseG1GC -XX:+UseStringDeduplication" --env foo=bar
```

將原始程式碼部署到應用程式的特定部署。

```azurecli
az spring-cloud app deploy -n MyApp -s Myspring-cloud -d green-deployment
```

## <a name="az-spring-cloud-app-list"></a>az 春季-cloud app list

列出 Azure 春季雲端實例中的所有應用程式。

```azurecli
az spring-cloud app list --resource-group -g
                         --service -s
```

|必要參數 | 說明 |
| --- | :--- |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --service-s | Azure 春季雲端的名稱。  您可以使用設定預設服務 `az configure --defaults spring-cloud=<name>` 。 |

## <a name="az-spring-cloud-app-restart"></a>az 春季-雲端應用程式重新開機

重新開機應用程式的實例。  預設為生產部署。

```azurecli
az spring-cloud app restart --name -n
                            --resource-group -g
                            --service -s
                            --deployment -d
                            --no-wait
```

| 必要參數 | 說明 |
| --- | :--- |
| --name-n | 應用程式的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --service-s | Azure 春季雲端的名稱。  您可以使用設定預設服務 `az configure --defaults spring-cloud=<name>` 。 |

| 選擇性參數 | 說明 |
| --- | :--- |
| --部署-d | 應用程式現有部署的名稱。  如果未指定，則預設為生產部署。 |
| --no-wait | 請勿等待長時間執行的作業完成。 |

## <a name="az-spring-cloud-app-scale"></a>az 春季-雲端應用程式規模

手動調整應用程式或其部署。

```azurecli
az spring-cloud app scale --name -n
                          --resource-group -g
                          --service -s
                          --cpu
                          --deployment -d
                          --instance-count
                          --memory
                          --no-wait
```

| 必要參數 | 說明 |
| --- | :--- |
| --name-n | 應用程式的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --service-s | Azure 春季雲端的名稱。  您可以使用設定預設服務 `az configure --defaults spring-cloud=<name>` 。 |

| 選擇性參數 | 說明 |
| --- | :--- |
| --cpu | 每個應用程式實例的虛擬 CPU 核心數目。 |
| --部署-d | 應用程式現有部署的名稱。  如果未指定，則預設為生產部署。 |
| --instance-count | 此應用程式的實例數目。 |
| --memory | 每個應用程式實例的記憶體 GB 數。 |
| --no-wait | 請勿等待長時間執行的作業完成。 |

### <a name="examples"></a>範例

將應用程式擴大至4個 CPU 核心，並將每個實例 8 GB 的記憶體擴大。

```azurecli
az spring-cloud app scale -n MyApp -s MyService --cpu 3 --memory 8
```

將應用程式的部署向外延展至5個實例。

```azurecli
az spring-cloud app scale -n MyApp -s MyService -d green-deployment --instance-count 5
```

## <a name="az-spring-cloud-app-set-deployment"></a>az 春季-cloud app set-部署

設定應用程式生產環境部署的設定選項。

```azurecli
az spring-cloud app set-deployment --deployment -d
                                   --name -n
                                   --resource-group -g
                                   --service -s
                                   --no-wait
```

| 必要參數 | 說明 |
| --- | :--- |
| --部署-d | 應用程式現有部署的名稱。 |
| --name-n | 應用程式的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --service-s | Azure 春季雲端的名稱。  您可以使用設定預設服務 `az configure --defaults spring-cloud=<name>` 。 |

| 選擇性參數 | 說明 |
| --- | :--- |
| --no-wait | 請勿等待長時間執行的作業完成。 |

### <a name="examples"></a>範例

將應用程式的預備環境部署交換至生產環境。

```azurecli
az spring-cloud app set-deployment -d green-deployment -n MyApp -s MyService
```

## <a name="az-spring-cloud-app-show"></a>az 春季-cloud app show

顯示 Azure 春季雲端中的應用程式詳細資料。

```azurecli
az spring-cloud app show --name -n
                         --resource-group -g
                         --service -s
```

| 必要參數 | 說明 |
| --- | :--- |
| --name-n | 應用程式的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --service-s | Azure 春季雲端的名稱。  您可以使用設定預設服務 `az configure --defaults spring-cloud=<name>` 。 |

## <a name="az-spring-cloud-app-show-deploy-log"></a>az 春季-cloud app show-deploy-log

顯示上次從原始程式碼部署的組建記錄檔。  預設為生產環境。

```azurecli
az spring-cloud app show-deploy-log --name -n
                                    --resource-group -g
                                    --service -s
                                    --deployment -d
```

| 必要參數 | 說明 |
| --- | :--- |
| --name-n | 應用程式的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --service-s | Azure 春季雲端的名稱。  您可以使用設定預設服務 `az configure --defaults spring-cloud=<name>` 。 |

| 選擇性參數 | 說明 |
| --- | :--- |
| --部署-d | 應用程式現有部署的名稱。  預設為生產環境。 |

## <a name="az-spring-cloud-app-start"></a>az 春季-雲端應用程式啟動

啟動應用程式的實例。  預設為生產環境。

```azurecli
az spring-cloud app start --name -n
                          --resource-group -g
                          --service -s
                          --deployment -d
                          --no-wait
```

| 必要參數 | 說明 |
| --- | :--- |
| --name-n | 應用程式的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --service-s | Azure 春季雲端的名稱。  您可以使用設定預設服務 `az configure --defaults spring-cloud=<name>` 。 |

| 選擇性參數 | 說明 |
| --- | :--- |
| --部署-d | 應用程式現有部署的名稱。  預設為生產環境。 |
| --no-wait | 請勿等待長時間執行的作業完成。 |

## <a name="az-spring-cloud-app-stop"></a>az 春季-cloud app stop

停止應用程式的實例。  預設為生產環境。

```azurecli
az spring-cloud app stop --name -n
                         --resource-group -g
                         --service -s
                         --deployment -d
                         --no-wait
```

| 必要參數 | 說明 |
| --- | :--- |
| --name-n | 應用程式的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --service-s | Azure 春季雲端的名稱。  您可以使用設定預設服務 `az configure --defaults spring-cloud=<name>` 。 |

| 選擇性參數 | 說明 |
| --- | :--- |
| --部署-d | 應用程式現有部署的名稱。  預設為生產環境。 |
| --no-wait | 請勿等待長時間執行的作業完成。 |

## <a name="az-spring-cloud-app-update"></a>az 春季-cloud app update

更新應用程式的預存設定。

```azurecli
az spring-cloud app update --name -n
                           --resource-group -g
                           --service -s
                           --deployment -d
                           --enable-persistent-storage
                           --env
                           --is-public
                           --jvm-options
                           --no-wait
                           --runtime-version
```

| 必要參數 | 說明 |
| --- | :--- |
| --name-n | 應用程式的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --service-s | Azure 春季雲端的名稱。  您可以使用設定預設服務 `az configure --defaults spring-cloud=<name>` 。 |

| 選擇性參數 | 說明 |
| --- | :--- |
| --部署-d | 應用程式現有部署的名稱。  預設為生產環境。 |
| --啟用-持續性儲存體 | 布林值。  若為 true，則使用預設路徑掛接50GB 磁片。 |
| --env | 以空格分隔的環境變數（以 ' key [= value] ' 格式表示）。 |
| --為-public | 布林值。  若為 true，則將公用網域指派給應用程式。 |
| --jvm-選項 | 包含 JVM 選項的字串。  請使用 ' = ' 而不是 ' '，以避免發生 shell 剖析錯誤。 例如，  `--jvm-options='-Xms1024m -Xmx2048m` 。 |
| --no-wait | 請勿等待長時間執行的作業完成。 |
| --執行階段版本 | 應用程式中所使用之語言的執行階段版本。  允許的值： `Java_11` 、 `Java_8` 。 |

### <a name="example"></a>範例

新增應用程式的環境變數。

```azurecli
az spring-cloud app update --env foo=bar
```

## <a name="az-spring-cloud-app-binding-list"></a>az 春季-cloud app binding 清單

列出應用程式中的所有服務系結。

```azurecli
az spring-cloud app binding list --app
                                 --resource-group -g
                                 --service -s
```

| 必要參數 | 說明 |
| --- | :--- |
| --應用程式 | 應用程式的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --service-s | Azure 春季雲端的名稱。  您可以使用設定預設服務 `az configure --defaults spring-cloud=<name>` 。 |

## <a name="az-spring-cloud-app-binding-remove"></a>az 春季-cloud app binding remove

從應用程式移除服務系結。

```azurecli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| 必要參數 | 說明 |
| --- | :--- |
| --應用程式 | 應用程式的名稱。 |
| --name | 要移除之服務系結的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --service-s | Azure 春季雲端的名稱。  您可以使用設定預設服務 `az configure --defaults spring-cloud=<name>` 。 |

## <a name="az-spring-cloud-app-binding-show"></a>az 春季-cloud app binding show

顯示服務系結的詳細資料。

```azurecli
az spring-cloud app binding show --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| 必要參數 | 說明 |
| --- | :--- |
| --應用程式 | 應用程式的名稱。 |
| --name | 服務系結的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --service-s | Azure 春季雲端的名稱。  您可以使用設定預設服務 `az configure --defaults spring-cloud=<name>` 。 |

## <a name="az-spring-cloud-app-binding-cosmos-add"></a>az 春季-cloud app binding cosmos add

將 Azure Cosmos DB 系結到應用程式。

```azurecli
az spring-cloud app binding cosmos add --api-type
                                       --app
                                       --name -n
                                       --resource-group -g
                                       --resource-id
                                       --service -s
                                       --collection-name
                                       --database-name
                                       --key-space
```

```azurecli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| 必要參數 | 說明 |
| --- | :--- |
| --api 類型 | 使用下列其中一個值來指定 API 類型： cassandra、gremlin、mongo、sql、table。 |
| --應用程式 | 應用程式的名稱。 |
| --name | 服務系結的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --service-s | Azure 春季雲端的名稱。  您可以使用設定預設服務 `az configure --defaults spring-cloud=<name>` 。 |

|選擇性參數 | 說明 |
| --- | :--- |
| --collection-name | 集合的名稱。  使用 Gremlin 時的必要參數。 |
| --資料庫-名稱 | 資料庫的名稱。  使用 Mongo、SQL 和 Gremlin 時的必要參數。 |
| --索引鍵-空間 | Cassandra 索引鍵空間。  使用 Cassandra 時的必要參數。 |

## <a name="az-spring-cloud-app-binding-cosmos-update"></a>az 春季-cloud app binding cosmos update

```azurecli
az spring-cloud app binding cosmos update --app
                                          --name -n
                                          --resource-group -g
                                          --service -s
                                          --collection-name
                                          --database-name
                                          --key-space
```

| 必要參數 | 說明 |
| --- | :--- |
| --應用程式 | 應用程式的名稱。 |
| --name | 服務系結的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --service-s | Azure 春季雲端的名稱。  您可以使用設定預設服務 `az configure --defaults spring-cloud=<name>` 。 |

|選擇性參數 | 說明 |
| --- | :--- |
| --collection-name | 集合的名稱。  使用 Gremlin 時的必要參數。 |
| --資料庫-名稱 | 資料庫的名稱。  使用 Mongo、SQL 和 Gremlin 時的必要參數。 |
| --索引鍵-空間 | Cassandra 索引鍵空間。  使用 Cassandra 時的必要參數。 |

## <a name="az-spring-cloud-app-binding-mysql-add"></a>az 春季-cloud app binding mysql add

```azurecli
az spring-cloud app binding mysql add --app
                                      --database-name
                                      --key
                                      --name -n
                                      --resource-group -g
                                      --service -s
                                      --username
```

| 必要參數 | 說明 |
| --- | :--- |
| --應用程式 | 應用程式的名稱。 |
| --資料庫-名稱 | 資料庫的名稱。 |
| --key | 服務的 API 金鑰。 |
| --name | 服務系結的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --資源-識別碼 | 要系結之服務的 Azure 資源識別碼。 |
| --service-s | Azure 春季雲端的名稱。  您可以使用設定預設服務 `az configure --defaults spring-cloud=<name>` 。 |
| --username | 用來存取資料庫的使用者名稱。 |

## <a name="az-spring-cloud-app-binding-mysql-update"></a>az 春季-cloud app binding mysql update

將應用程式的服務系結連線更新至適用於 MySQL 的 Azure 資料庫。

```azurecli
az spring-cloud app binding mysql update --add
                                         --name -n
                                         --resource-group -g
                                         --service -s
                                         --database-name
                                         --key
                                         --username
```

| 必要參數 | 說明 |
| --- | :--- |
| --應用程式 | 應用程式的名稱。 |
| --name | 服務系結的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --service-s | Azure 春季雲端的名稱。  您可以使用設定預設服務 `az configure --defaults spring-cloud=<name>` 。 |

| 選擇性參數 | 說明 |
| --- | :--- |
| --資料庫-名稱 | 資料庫的名稱。 |
| --key | 服務的 API 金鑰。 |
| --username | 用來存取資料庫的使用者名稱。 |

## <a name="az-spring-cloud-app-binding-redis-add"></a>az 春季-cloud app binding redis add

將 Azure Cache for Redis 系結到應用程式。

```azurecli
az spring-cloud app binding redis add --app
                                      --name -n
                                      --resource-group -g
                                      --resource-id
                                      --service -s
                                      --disable-ssl
```

| 必要參數 | 說明 |
| --- | :--- |
| --應用程式 | 應用程式的名稱。 |
| --name | 服務系結的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --資源-識別碼 | 您要系結之服務的 Azure 資源識別碼。 |
| --service-s | Azure 春季雲端的名稱。  您可以使用設定預設服務 `az configure --defaults spring-cloud=<name>` 。 |

| 選擇性參數 | 說明 |
| --- | :--- |
| --disable-ssl | 停用 TLS。 |

## <a name="az-spring-cloud-app-binding-redis-update"></a>az 春季-cloud app binding redis update

更新 Azure Cache for Redis 的服務系結。

| 必要參數 | 說明 |
| --- | :--- |
| --應用程式 | 應用程式的名稱。 |
| --name | 服務系結的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --service-s | Azure 春季雲端的名稱。  您可以使用設定預設服務 `az configure --defaults spring-cloud=<name>` 。 |

| 選擇性參數 | 說明 |
| --- | :--- |
| --disable-ssl | 停用 TLS。 |

## <a name="az-spring-cloud-app-deployment-create"></a>az 春季-cloud app deployment create

建立應用程式的預備環境部署。

若要部署程式碼或將設定更新為現有的部署，請使用 `az spring-cloud app deploy --deployment <staging-deployment>` 或 ' az 春季-cloud app update--deployment <staging deployment> 。

| 必要參數 | 說明 |
| --- | :--- |
| --應用程式 | 應用程式的名稱。 |
| --name | 服務系結的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --service-s | Azure 春季雲端的名稱。  您可以使用設定預設服務 `az configure --defaults spring-cloud=<name>` 。 |

| 選擇性參數 | 說明 |
| --- | :--- |
| --cpu | 每個實例的虛擬 CPU 核心數目。  預設值：1 |
| --env | 以空格分隔的環境變數（以 ' key [= value] ' 格式表示）。 |
| --instance-count | 實例數目。 預設值：1。 |
| --jar-path | 如果有提供，請部署 jar。  否則，請將目前的資料夾部署為 tar。 |
| --jvm-選項 | 包含 JVM 選項的字串。  請使用 ' = ' 而不是 ' '，以避免發生 shell 剖析錯誤。 例如，  `--jvm-options='-Xms1024m -Xmx2048m` 。 |
| --memory | 每個實例的記憶體 GB 數。 |
| --no-wait | 請勿等待長時間執行的作業完成。 |
| --執行階段版本 | 應用程式中所使用之語言的執行階段版本。  允許的值： `Java_11` 、 `Java_8` 。 |
| --略過-複製-設定 | 藉由複製目前的生產部署設定來建立預備部署。 |
| --目的模組 | 要部署的子模組。  從原始程式碼建立多個 jar 套件時需要。 |
| --version | 部署版本。  如果未設定，則為未變更。 |

### <a name="examples"></a>範例

將原始程式碼部署到應用程式的新部署。  這會封裝目前的目錄、使用 Pivotal 組建系統建立二進位檔，然後再進行部署。

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService
```

使用 JVM 選項和環境變數，將預先建立的 jar 部署到應用程式。

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseStringDeDuplication" --env foo=bar
```

## <a name="az-spring-cloud-app-deployment-delete"></a>az 春季-雲端應用程式部署刪除

刪除應用程式的部署。

```azurecli
az spring-cloud app deployment delete --app
                                      --name -n
                                      --resource-group -g
                                      --service -s
```

| 必要參數 | 說明 |
| --- | :--- |
| --應用程式 | 應用程式的名稱。 |
| --name | 部署的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --service-s | Azure 春季雲端的名稱。  您可以使用設定預設服務 `az configure --defaults spring-cloud=<name>` 。 |

## <a name="az-spring-cloud-app-deployment-list"></a>az 春季-雲端應用程式部署清單

列出應用程式中的所有部署。

```azurecli
az spring-cloud app deployment list --app
                                    --resource-group -g
                                    --service -s
```

| 必要參數 | 說明 |
| --- | :--- |
| --應用程式 | 應用程式的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --service-s | Azure 春季雲端的名稱。  您可以使用設定預設服務 `az configure --defaults spring-cloud=<name>` 。 |

## <a name="az-spring-cloud-app-deployment-show"></a>az 春季-cloud app deployment show

顯示部署的詳細資料。

```azurecli
az spring-cloud app deployment show --app
                                    --name -n
                                    --resource-group -g
                                    --service -s
```

| 必要參數 | 說明 |
| --- | :--- |
| --應用程式 | 應用程式的名稱。 |
| --name | 部署的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --service-s | Azure 春季雲端的名稱。  您可以使用設定預設服務 `az configure --defaults spring-cloud=<name>` 。 |

## <a name="az-spring-cloud-config-server-clear"></a>az 春季-cloud config-伺服器 clear

清除 Config Server 中的所有設定。

```azurecli
az spring-cloud config-server clear --name
                                        --resource-group -g
```

| 必要參數 | 說明 |
| --- | :--- |
| --name | Azure 春季雲端的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |

## <a name="az-spring-cloud-config-server-set"></a>az 春季-cloud config-server set

使用 YAML 檔案在 Config Server 上設定設定。

```azurecli
az spring-cloud config-server set --config-file
                                  --name -n
                                  --resource-group -g
                                  --no-wait
```

| 必要參數 | 說明 |
| --- | :--- |
| --config-file | Config Server 設定的 YAML 資訊清單檔案路徑。 |
| --name | Azure 春季雲端的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |

| 選擇性參數 | 說明 |
| --- | :--- |
| --no-wait | 請勿完成長時間執行的作業。

## <a name="az-spring-cloud-config-server-show"></a>az 春季-cloud config-server show

顯示 Config Server 設定。

```azurecli
az spring-cloud config-server show --name -n
                                   --resource-group -g
```

| 必要參數 | 說明 |
| --- | :--- |
| --name | Azure 春季雲端的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |

## <a name="az-spring-cloud-config-server-git-set"></a>az 春季-cloud config-server git set

設定 Config Server 的 git 屬性。  這會覆寫所有現有的 git 屬性。

```azurecli
az spring-cloud config-server git set --name -n
                                      --resource-group g
                                      --uri
                                      --defer
                                      --host-key
                                      --host-key-algorithm
                                      --label
                                      --password
                                      --private-key
                                      --search-paths
                                      --strict-host-key-checking
                                      --username
```

| 必要參數 | 說明 |
| --- | :--- |
| --name | Azure 春季雲端的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --uri | 已新增之設定的 URI。 |

| 選擇性參數 | 說明 |
| --- | :--- |
| --defer | 暫時將物件儲存在本機快取中，而不是傳送至 Azure。  使用 `az cache` 以查看/清除。 |
| --主機金鑰 | 新增的設定的主機金鑰。 |
| --主機金鑰演算法 | 新增的設定的主機金鑰演算法。 |
| --標籤 | 新增的設定標籤。 |
| --password | 新增的設定密碼。 |
| --私用金鑰 | 新增的設定的私密金鑰。 |
| --搜尋-路徑 | 新增的設定搜尋路徑。 針對多個路徑使用逗號分隔符號。 |
| --strict-主機-索引鍵檢查 | 啟用所新增設定的嚴格主機金鑰檢查。 |
| --username | 新增的設定的使用者名稱。 |

## <a name="az-spring-cloud-config-server-git-repo-add"></a>az 春季-cloud config-伺服器 git 存放庫新增

```azurecli
az spring-cloud config-server git repo add --name -n
                                           --repo-name
                                           --resource-group -g
                                           --uri
                                           --defer
                                           --host-key
                                           --host-key-algorithm
                                           --label
                                           --password
                                           --pattern
                                           --private-key
                                           --search-paths
                                           --strict-host-key-checking
                                           --username
```

| 必要參數 | 說明 |
| --- | :--- |
| --name | Azure 春季雲端的名稱。 |
| --存放庫-名稱 | 存放庫的 URI。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --uri | 已新增之設定的 URI。 |

| 選擇性參數 | 說明 |
| --- | :--- |
| --defer | 暫時將物件儲存在本機快取中，而不是傳送至 Azure。  使用 `az cache` 以查看/清除。 |
| --主機金鑰 | 新增的設定的主機金鑰。 |
| --主機金鑰演算法 | 新增的設定的主機金鑰演算法。 |
| --標籤 | 新增的設定標籤。 |
| --password | 新增的設定密碼。 |
| --模式 | 存放庫模式。  針對多個路徑使用逗號分隔符號。|
| --私用金鑰 | 新增的設定的私密金鑰。 |
| --搜尋-路徑 | 新增的設定搜尋路徑。 針對多個路徑使用逗號分隔符號。 |
| --strict-主機-索引鍵檢查 | 啟用所新增設定的嚴格主機金鑰檢查。 |
| --username | 新增的設定的使用者名稱。 |

## <a name="az-spring-cloud-config-server-git-repo-list"></a>az 春季-cloud config-伺服器 git 存放庫清單

列出 Config Server 中定義的所有 git 存放庫

```azurecli
az spring-cloud config-server git repo list --name -n
                                       --resource-group -g
                                       --defer
```

| 必要參數 | 說明 |
| --- | :--- |
| --name | Azure 春季雲端的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |

| 選擇性參數 | 說明 |
| --- | :--- |
| --defer | 暫時將物件儲存在本機快取中，而不是傳送至 Azure。  使用 `az cache` 以查看/清除。 |

## <a name="az-spring-cloud-config-server-git-repo-remove"></a>az 春季-cloud config-伺服器 git 存放庫移除

從 Config Server 中移除現有的 git 存放庫設定。

```azurecli
az spring-cloud config-server git repo remove --name -n
                                         --repo-name
                                         --resource-group -g
                                         --defer
```

| 必要參數 | 說明 |
| --- | :--- |
| --name | Azure 春季雲端的名稱。 |
| --存放庫-名稱 | 存放庫的 URI。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |

| 選擇性參數 | 說明 |
| --- | :--- |
| --defer | 暫時將物件儲存在本機快取中，而不是傳送至 Azure。  使用 `az cache` 以查看/清除。 |

## <a name="az-spring-cloud-test-endpoint-disable"></a>az 春季-cloud test-endpoint disable

停用 Azure 春季雲端的測試端點

```azurecli
az spring-cloud test-endpoint disable --name -n
                                      --resource-group -g
```

| 必要參數 | 說明 |
| --- | :--- |
| --name | Azure 春季雲端的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |

## <a name="az-spring-cloud-test-endpoint-enable"></a>az 春季-雲端測試-啟用端點

啟用 Azure 春季雲端的測試端點。 

```azurecli
az spring-cloud test-endpoint enable --name -n
                                     --resource-group -g
```

| 必要參數 | 說明 |
| --- | :--- |
| --name | Azure 春季雲端的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |

## <a name="az-spring-cloud-test-endpoint-list"></a>az 春季-雲端測試-端點清單 

列出適用于 Azure 春季雲端的可用測試端點金鑰。

```azurecli
az spring-cloud test-endpoint list --name -n
                                   --resource-group -g
                                   --app
                                   --deployment -d
```

| 必要參數 | 說明 |
| --- | :--- |
| --name | Azure 春季雲端的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |

| 選擇性參數 | 說明 |
| --- | :--- |
| --應用程式 | 應用程式的名稱。 |
| --部署-d | 應用程式現有部署的名稱。  如果未指定，則預設為生產環境。 |

## <a name="az-spring-cloud-test-endpoint-renew-key"></a>az 春季-雲端測試-端點更新金鑰

重新產生 Azure 春季雲端的測試端點金鑰。

```azurecli
az spring-cloud test-endpoint renew-key --name -n
                                        --resource-group -g
                                        --type
```

| 必要參數 | 說明 |
| --- | :--- |
| --name | Azure 春季雲端的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --類型 | 測試端點金鑰的類型。  允許的值： Primary、次要。 |
