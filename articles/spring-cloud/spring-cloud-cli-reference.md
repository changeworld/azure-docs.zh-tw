---
title: 阿茲春雲
description: 使用 Azure CLI 管理 Azure 春雲
author: bmitchell287
ms.service: spring-cloud
ms.topic: reference
ms.date: 10/03/2019
ms.author: brendm
ms.openlocfilehash: 33d13d2d4fa9003ef041c4c96be83a69ac595a78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298786"
---
# <a name="az-spring-cloud"></a>阿茲春雲

## <a name="manage-azure-spring-cloud-using-the-azure-cli"></a>使用 Azure CLI 管理 Azure 春雲

>[!Note]
> Azure Spring Cloud 目前為預覽狀態。  這些命令可能會在將來的版本中更改或刪除。

| 阿茲春雲 |  |
|------|------:|
| [az 彈簧雲創建](#az-spring-cloud-create) | 創建 Azure 春雲實例。 |
| [az 彈簧雲刪除](#az-spring-cloud-delete) | 刪除 Azure 春雲實例。 |
| [az 彈簧雲清單](#az-spring-cloud-list) | 列出給定資源組中的所有 Azure 春雲實例，否則列出訂閱標識。 |
| [阿茲春雲秀](#az-spring-cloud-show) | 顯示 Azure 春雲的詳細資訊。 |

| az 春雲應用程式 | 管理 Azure 春雲中的應用的命令。  |
| ---- | ----: |
| [az 彈簧雲應用程式創建](#az-spring-cloud-app-create) | 在 Azure 春雲中創建具有預設部署的新應用。 |
| [az 彈簧雲應用程式刪除](#az-spring-cloud-app-delete) | 刪除 Azure 春雲中的應用。 |
| [az 彈簧雲應用部署](#az-spring-cloud-app-deploy) | 從原始程式碼或預構建的二進位檔案部署到應用並更新相關配置。 |
| [az 彈簧雲應用清單](#az-spring-cloud-app-list) | 列出 Azure 春雲中的所有應用。 |
| [az 彈簧雲應用程式重新開機](#az-spring-cloud-app-restart) | 使用生產部署預設值重新開機應用實例。 |
| [az 春雲應用規模](#az-spring-cloud-app-scale) | 手動縮放應用或其部署。 |
| [az 彈簧雲應用集部署](#az-spring-cloud-app-set-deployment) | 設置應用的生產部署。 |
| [az 春雲應用程式顯示](#az-spring-cloud-app-show) | 在 Azure 春雲中顯示應用的詳細資訊。 |
| [az 彈簧雲應用程式顯示部署日誌](#az-spring-cloud-app-show-deploy-log) | 從源顯示最新部署的生成日誌。 預設為生產部署。 |
| [az 彈簧雲應用程式啟動](#az-spring-cloud-app-start) | 使用生產部署預設值啟動應用實例。 |
| [az 彈簧雲應用程式停止](#az-spring-cloud-app-stop) | 使用生產部署預設值停止應用實例。 |
| [az 彈簧雲應用程式更新](#az-spring-cloud-app-update) | 更新指定的應用的配置。 |

| az 彈簧雲應用程式綁定 | 使用 Azure 資料服務管理綁定的命令。  在這些設置生效之前，必須重新開機應用。 |
| --- | ---: |
| [az 彈簧雲應用繫結欄位表](#az-spring-cloud-app-binding-list) | 列出應用中的所有服務綁定。 |
| [az 彈簧雲應用綁定刪除](#az-spring-cloud-app-binding-remove) | 從應用中刪除服務綁定。 |
| [az 彈簧雲應用程式綁定顯示](#az-spring-cloud-app-binding-show) | 顯示服務綁定的詳細資訊。 |
| [az 彈簧雲應用程式綁定宇宙添加](#az-spring-cloud-app-binding-cosmos-add) | 將 Azure CosmosDB 與應用綁定。 |
| [az 彈簧雲應用程式綁定宇宙更新](#az-spring-cloud-app-binding-cosmos-update) | 更新 Azure CosmosDB 服務綁定。 |
| [az 彈簧雲應用程式綁定 mysql 添加](#az-spring-cloud-app-binding-mysql-add) | 將 MySQL 的 Azure 資料庫與應用綁定。 |
| [az 彈簧雲應用程式綁定 mysql 更新](#az-spring-cloud-app-binding-mysql-update) | 更新 MySQL 服務綁定的 Azure 資料庫。 |
| [az 彈簧雲應用程式綁定 redis 添加](#az-spring-cloud-app-binding-redis-add) | 將 Redis 的 Azure 緩存與應用綁定。 |
| [az 彈簧雲應用程式綁定 redis 更新](#az-spring-cloud-app-binding-redis-update) | 更新 Redis 服務綁定的 Azure 緩存。 |

| az 彈簧雲應用部署 | 用於在 Azure Spring Cloud 中管理應用的部署生命週期的命令。 |
| --- | ---: |
| [az 彈簧雲應用部署創建](#az-spring-cloud-app-deployment-create) | 為應用創建暫存部署。 |
| [az 彈簧雲應用部署刪除](#az-spring-cloud-app-deployment-delete) | 刪除應用的部署。 |
| [az 彈簧雲應用部署清單](#az-spring-cloud-app-deployment-list) | 列出應用中的所有部署。 |
| [az 彈簧雲應用部署顯示](#az-spring-cloud-app-deployment-show) | 顯示部署的詳細資訊。 |

| az 彈簧雲佈建服務器 | 管理 Azure 春雲佈建服務器的命令。 |
| --- | ---: |
| [az 彈簧雲佈建服務器清除](#az-spring-cloud-config-server-clear) | 擦除佈建服務器中的所有設置。 |
| [az 彈簧雲佈建服務器集](#az-spring-cloud-config-server-set) | 從 YAML 檔定義佈建服務器。 |
| [az 彈簧雲佈建服務器顯示](#az-spring-cloud-config-server-show) | 顯示佈建服務器配置。 |
| [az 彈簧雲佈建服務器 git 集](#az-spring-cloud-config-server-git-set) | 為配置伺服器定義 git 屬性。  以前的值將被覆蓋。 |
| [az 彈簧雲佈建服務器 git 存儲庫 添加](#az-spring-cloud-config-server-git-repo-add) | 向佈建服務器添加新 git 存儲庫配置。 |
| [az 彈簧雲佈建服務器 git 存儲庫清單](#az-spring-cloud-config-server-git-repo-list) | 列出佈建服務器的所有 git 存儲庫配置。 |
| [az 彈簧雲佈建服務器 git 存儲庫刪除](#az-spring-cloud-config-server-git-repo-remove) | 從佈建服務器中刪除指定的 git 存儲庫。 |

| az 彈簧雲測試端點 | 在 Azure 春雲中管理終結點測試的命令 |
| --- | ---: |
| [az 彈簧雲測試終結點禁用](#az-spring-cloud-test-endpoint-disable) | 禁用測試終結點。 |
| [az 彈簧雲測試終結點啟用](#az-spring-cloud-test-endpoint-enable) | 啟用測試終結點。 |
| [az 彈簧雲測試終結點清單](#az-spring-cloud-test-endpoint-list) | 列出測試終結點鍵。 |
| [az 彈簧雲測試端點續訂鍵](#az-spring-cloud-test-endpoint-renew-key) | 重新生成測試終結點金鑰。 |

## <a name="az-spring-cloud-create"></a>az 彈簧雲創建

在 Azure 春雲中創建具有預設部署的新應用。

```azurecli
az spring-cloud create  --name -n
                        --resource-group -g
                        --location -l
                        --no-wait
```

| 必要參數 | |
| --- | ---: |
| --名稱 -n | 此 Azure 春雲實例的名稱。 |
| --resource-group -g | 指定此應用的資源組。  使用配置預設組`az configure --defaults group=<name>` |

| 選擇性參數 | |
| --- | ---: |
| --location -l | 指定此應用程式的伺服器位置。  使用`az account list-locations` |
| --no-wait | 不要為長時間運行的操作完成。

### <a name="examples"></a>範例

在西部創建新的 Azure 春雲

```azurecli
az spring-cloud create -n MyService -g MyResourceGroup -l westus
```

## <a name="az-spring-cloud-delete"></a>az 彈簧雲刪除

刪除 Azure 春雲實例。

```azurecli
az spring cloud --name -n
                --resource-group -g
                --no-wait
```

| 必要參數 | |
| --- | ---: |
| --名稱 -n | 要刪除的 Azure 春雲實例的名稱。 |
| --resource-group -g | Azure 春雲所屬的資源組的名稱。 |

| 選擇性參數 | |
| --- | ---: |
| -不等待 | 不要等待長時間運行的操作完成。 |

### <a name="example"></a>範例

從"我的資源組"中刪除名為"我的服務"的 Azure 春雲實例。

```azurecli
az spring-cloud delete -n MyService -g MyResourceGroup
```

## <a name="az-spring-cloud-list"></a>az 彈簧雲清單

列出與給定資源組關聯的所有 Azure 春雲實例。 如果未指定資源組，則列出訂閱代碼。

```azurecli
az spring-cloud list --resource-group -g
```

| 必要參數 | |
| --- | ---: |
| --resource-group -g | 資源群組的名稱。 |

## <a name="az-spring-cloud-show"></a>阿茲春雲秀

顯示指定的 Azure 春雲實例的詳細資訊。

```azurecli
az spring-cloud show --name -n
                     -- resource-group -g
```

| 必要參數 | |
| --- | ---: |
| --名稱 -n | Azure 春雲實例的名稱。 |
| --resource-group -g | Azure 春雲實例所屬的資源組的名稱。

## <a name="az-spring-cloud-app-create"></a>az 彈簧雲應用程式創建

在 Azure 春雲中創建新應用。

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

| 必要參數 | |
| --- | ---: |
| --名稱 -n | 應用程式的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --服務 -s | Azure 春雲的名稱。  您可以使用 配置預設服務`az configure --defaults spring-cloud=<name>`。 |

| 選擇性參數 | |
| --- | ---: |
| --cpu | 每個實例的虛擬內核數。  預設值：1。 |
| --啟用持久存儲 | 布林值。  如果為 true，則裝載具有預設路徑的 50GB 磁片。 |
| --instance-count | 實例數。  預設值：1。 |
| --是公共的 | 布林值。  如果為 true，則分配公有域。 |
| --memory | 每個實例的 GB 記憶體數。  預設值：1。 |

### <a name="examples"></a>範例

創建具有預設配置的應用。

```azurecli
az spring-cloud app create -n MyApp -s MyService
```

創建具有 3 個實例的可公開訪問的應用。  每個實例都有 3 GB 的記憶體和 2 個 CPU 內核。

```azurecli
az spring-cloud app create -n MyApp -s MyService --is-public true --cpu 2 --memory 3
```

## <a name="az-spring-cloud-app-delete"></a>az 彈簧雲應用程式刪除

刪除 Azure 春雲中的應用。

```azurecli
az spring cloud app delete  --name -n
                            --resource-group -g
                            --service -s
```

| 必要參數 | |
| --- | ---: |
| --名稱 -n | 應用程式的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --服務 -s | Azure 春雲的名稱。  您可以使用 配置預設服務`az configure --defaults spring-cloud=<name>`。 |

## <a name="az-spring-cloud-app-deploy"></a>az 彈簧雲應用部署

從原始程式碼或預構建的二進位檔案將應用部署到 Azure 春雲，並更新相關配置。

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

| 必要參數 | |
| --- | ---: |
| --名稱 -n | 應用程式的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --服務 -s | Azure 春雲的名稱。  您可以使用 配置預設服務`az configure --defaults spring-cloud=<name>`。 |

| 選擇性參數 | |
| --- | ---: |
| --cpu | 每個實例的虛擬 CPI 核心數。 |
| --部署-d | 現有應用部署的名稱。  如果未指定，則預設為生產部署。 |
| --恩夫 | 空間分隔環境變數採用"鍵[值]"格式。 |
| --instance-count | 實例數。 |
| --jar-path | 如果提供，則從給定路徑部署 jar。 否則，將當前資料夾部署為 tar。 |
| --jvm 選項 | 包含 JVM 選項的字串。  使用"*"而不是''來避免 shell 分析錯誤。 例如， `--jvm-options='-Xms1024m -Xmx2048m`. |
| --memory | 每個實例的 GB 記憶體數。 |
| --no-wait | 不要等待長時間運行的操作完成。 |
| --執行階段版本 | 應用中使用的語言的執行階段版本。  允許的值： `Java_11` `Java_8`. |
| --目的模組 | 要部署的子模組。  從原始程式碼生成多個 jar 包時是必需的。 |
| --version | 部署版本。  如果未設置，則保持不變。 |

### <a name="examples"></a>範例

將原始程式碼部署到應用。 這將打包目前的目錄，使用 Pivotal 生成服務構建二進位檔案，然後部署到應用。

```azurecli
az spring-cloud app deploy -n MyApp -s MyService
```

使用 JVM 選項和環境變數將預構建的 jar 部署到應用。

```azurecli
az spring-cloud app deploy -n MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseG1GC -XX:+UseStringDeduplication" --env foo=bar
```

將原始程式碼部署到應用的特定部署。

```azurecli
az spring-cloud app deploy -n MyApp -s Myspring-cloud -d green-deployment
```

## <a name="az-spring-cloud-app-list"></a>az 彈簧雲應用清單

列出 Azure 春雲實例中的所有應用。

```azurecli
az spring-cloud app list --resource-group -g
                         --service -s
```

|必要參數 | |
| --- | ---: |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --服務 -s | Azure 春雲的名稱。  您可以使用 配置預設服務`az configure --defaults spring-cloud=<name>`。 |

## <a name="az-spring-cloud-app-restart"></a>az 彈簧雲應用程式重新開機

重新開機應用的實例。  預設為生產部署。

```azurecli
az spring-cloud app restart --name -n
                            --resource-group -g
                            --service -s
                            --deployment -d
                            --no-wait
```

| 必要參數 | |
| --- | ---: |
| --名稱 -n | 應用程式的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --服務 -s | Azure 春雲的名稱。  您可以使用 配置預設服務`az configure --defaults spring-cloud=<name>`。 |

| 選擇性參數 | |
| --- | ---: |
| --部署-d | 應用的現有部署的名稱。  如果未指定，則預設為生產部署。 |
| --no-wait | 不要等待長時間運行的操作完成。 |

## <a name="az-spring-cloud-app-scale"></a>az 春雲應用規模

手動縮放應用或其部署。

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

| 必要參數 | |
| --- | ---: |
| --名稱 -n | 應用程式的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --服務 -s | Azure 春雲的名稱。  您可以使用 配置預設服務`az configure --defaults spring-cloud=<name>`。 |

| 選擇性參數 | |
| --- | ---: |
| --cpu | 每個應用實例的虛擬 CPU 內核數。 |
| --部署-d | 應用的現有部署的名稱。  如果未指定，則預設為生產部署。 |
| --instance-count | 此應用程式的實例數。 |
| --memory | 每個應用實例的 GB 記憶體數。 |
| --no-wait | 不要等待長時間運行的操作完成。 |

### <a name="examples"></a>範例

將應用擴展為 4 個 CPU 內核和每個實例的 8 GB 記憶體。

```azurecli
az spring-cloud app scale -n MyApp -s MyService --cpu 3 --memory 8
```

將應用的部署擴展到 5 個實例。

```azurecli
az spring-cloud app scale -n MyApp -s MyService -d green-deployment --instance-count 5
```

## <a name="az-spring-cloud-app-set-deployment"></a>az 彈簧雲應用集部署

設置應用的生產部署的配置選項。

```azurecli
az spring-cloud app set-deployment --deployment -d
                                   --name -n
                                   --resource-group -g
                                   --service -s
                                   --no-wait
```

| 必要參數 | |
| --- | ---: |
| --部署-d | 應用的現有部署的名稱。 |
| --名稱 -n | 應用程式的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --服務 -s | Azure 春雲的名稱。  您可以使用 配置預設服務`az configure --defaults spring-cloud=<name>`。 |

| 選擇性參數 | |
| --- | ---: |
| --no-wait | 不要等待長時間運行的操作完成。 |

### <a name="examples"></a>範例

將應用的暫存部署交換到生產。

```azurecli
az spring-cloud app set-deployment -d green-deployment -n MyApp -s MyService
```

## <a name="az-spring-cloud-app-show"></a>az 春雲應用程式顯示

在 Azure 春雲中顯示應用的詳細資訊。

```azurecli
az spring-cloud app show --name -n
                         --resource-group -g
                         --service -s
```

| 必要參數 | |
| --- | ---: |
| --名稱 -n | 應用程式的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --服務 -s | Azure 春雲的名稱。  您可以使用 配置預設服務`az configure --defaults spring-cloud=<name>`。 |

## <a name="az-spring-cloud-app-show-deploy-log"></a>az 彈簧雲應用程式顯示部署日誌

從原始程式碼顯示上次部署的生成日誌。  預設為生產環境。

```azurecli
az spring-cloud app show-deploy-log --name -n
                                    --resource-group -g
                                    --service -s
                                    --deployment -d
```

| 必要參數 | |
| --- | ---: |
| --名稱 -n | 應用程式的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --服務 -s | Azure 春雲的名稱。  您可以使用 配置預設服務`az configure --defaults spring-cloud=<name>`。 |

| 選擇性參數 | |
| --- | ---: |
| --部署-d | 應用的現有部署的名稱。  預設為生產環境。 |

## <a name="az-spring-cloud-app-start"></a>az 彈簧雲應用程式啟動

啟動應用實例。  預設為生產環境。

```azurecli
az spring-cloud app start --name -n
                          --resource-group -g
                          --service -s
                          --deployment -d
                          --no-wait
```

| 必要參數 | |
| --- | ---: |
| --名稱 -n | 應用程式的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --服務 -s | Azure 春雲的名稱。  您可以使用 配置預設服務`az configure --defaults spring-cloud=<name>`。 |

| 選擇性參數 | |
| --- | ---: |
| --部署-d | 應用的現有部署的名稱。  預設為生產環境。 |
| --no-wait | 不要等待長時間運行的操作完成。 |

## <a name="az-spring-cloud-app-stop"></a>az 彈簧雲應用程式停止

停止應用的實例。  預設為生產環境。

```azurecli
az spring-cloud app stop --name -n
                         --resource-group -g
                         --service -s
                         --deployment -d
                         --no-wait
```

| 必要參數 | |
| --- | ---: |
| --名稱 -n | 應用程式的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --服務 -s | Azure 春雲的名稱。  您可以使用 配置預設服務`az configure --defaults spring-cloud=<name>`。 |

| 選擇性參數 | |
| --- | ---: |
| --部署-d | 應用的現有部署的名稱。  預設為生產環境。 |
| --no-wait | 不要等待長時間運行的操作完成。 |

## <a name="az-spring-cloud-app-update"></a>az 彈簧雲應用程式更新

更新應用程式的存儲配置。

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

| 必要參數 | |
| --- | ---: |
| --名稱 -n | 應用程式的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --服務 -s | Azure 春雲的名稱。  您可以使用 配置預設服務`az configure --defaults spring-cloud=<name>`。 |

| 選擇性參數 | |
| --- | ---: |
| --部署-d | 應用的現有部署的名稱。  預設為生產環境。 |
| --啟用持久存儲 | 布林值。  如果為 true，則使用預設路徑裝載 50GB 磁片。 |
| --恩夫 | 空間分隔環境變數採用"鍵[值]"格式。 |
| --是公共的 | 布林值。  如果為 true，則為應用分配公有域。 |
| --jvm 選項 | 包含 JVM 選項的字串。  使用"*"而不是''來避免 shell 分析錯誤。 例如， `--jvm-options='-Xms1024m -Xmx2048m`. |
| --no-wait | 不要等待長時間運行的操作完成。 |
| --執行階段版本 | 應用中使用的語言的執行階段版本。  允許的值： `Java_11` `Java_8`. |

### <a name="example"></a>範例

為應用添加環境變數。

```azurecli
az spring-cloud app update --env foo=bar
```

## <a name="az-spring-cloud-app-binding-list"></a>az 彈簧雲應用繫結欄位表

列出應用中的所有服務綁定。

```azurecli
az spring-cloud app binding list --app
                                 --resource-group -g
                                 --service -s
```

| 必要參數 | |
| --- | ---: |
| --應用 | 應用程式的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --服務 -s | Azure 春雲的名稱。  您可以使用 配置預設服務`az configure --defaults spring-cloud=<name>`。 |

## <a name="az-spring-cloud-app-binding-remove"></a>az 彈簧雲應用綁定刪除

從應用中刪除服務綁定。

```azurecli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| 必要參數 | |
| --- | ---: |
| --應用 | 應用程式的名稱。 |
| --name | 要刪除的服務綁定的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --服務 -s | Azure 春雲的名稱。  您可以使用 配置預設服務`az configure --defaults spring-cloud=<name>`。 |

## <a name="az-spring-cloud-app-binding-show"></a>az 彈簧雲應用程式綁定顯示

顯示服務綁定的詳細資訊。

```azurecli
az spring-cloud app binding show --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| 必要參數 | |
| --- | ---: |
| --應用 | 應用程式的名稱。 |
| --name | 服務綁定的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --服務 -s | Azure 春雲的名稱。  您可以使用 配置預設服務`az configure --defaults spring-cloud=<name>`。 |

## <a name="az-spring-cloud-app-binding-cosmos-add"></a>az 彈簧雲應用程式綁定宇宙添加

將 Azure 宇宙 DB 與應用綁定。

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

| 必要參數 | |
| --- | ---: |
| --api 類型 | 使用以下值之一指定 API 類型：卡珊多拉、格林林、蒙戈、sql、表。 |
| --應用 | 應用程式的名稱。 |
| --name | 服務綁定的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --服務 -s | Azure 春雲的名稱。  您可以使用 配置預設服務`az configure --defaults spring-cloud=<name>`。 |

|選擇性參數 | |
| --- | ---: |
| --集合名稱 | 集合的名稱。  使用格雷姆林時需要。 |
| --資料庫名稱 | 資料庫的名稱。  使用蒙戈、SQL 和 Gremlin 時是必需的。 |
| --鍵空間 | 卡珊多拉鍵空間。  使用卡珊多拉時需要。 |

## <a name="az-spring-cloud-app-binding-cosmos-update"></a>az 彈簧雲應用程式綁定宇宙更新

```azurecli
az spring-cloud app binding cosmos update --app
                                          --name -n
                                          --resource-group -g
                                          --service -s
                                          --collection-name
                                          --database-name
                                          --key-space
```

| 必要參數 | |
| --- | ---: |
| --應用 | 應用程式的名稱。 |
| --name | 服務綁定的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --服務 -s | Azure 春雲的名稱。  您可以使用 配置預設服務`az configure --defaults spring-cloud=<name>`。 |

|選擇性參數 | |
| --- | ---: |
| --集合名稱 | 集合的名稱。  使用格雷姆林時需要。 |
| --資料庫名稱 | 資料庫的名稱。  使用蒙戈、SQL 和 Gremlin 時是必需的。 |
| --鍵空間 | 卡珊多拉鍵空間。  使用卡珊多拉時需要。 |

## <a name="az-spring-cloud-app-binding-mysql-add"></a>az 彈簧雲應用程式綁定 mysql 添加

```azurecli
az spring-cloud app binding mysql add --app
                                      --database-name
                                      --key
                                      --name -n
                                      --resource-group -g
                                      --service -s
                                      --username
```

| 必要參數 | |
| --- | ---: |
| --應用 | 應用程式的名稱。 |
| --資料庫名稱 | 資料庫的名稱。 |
| --key | 服務的 API 金鑰。 |
| --name | 服務綁定的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --資源識別碼 | 要綁定的服務的 Azure 資源識別碼。 |
| --服務 -s | Azure 春雲的名稱。  您可以使用 配置預設服務`az configure --defaults spring-cloud=<name>`。 |
| --username | 資料庫訪問的使用者名。 |

## <a name="az-spring-cloud-app-binding-mysql-update"></a>az 彈簧雲應用程式綁定 mysql 更新

更新應用程式與 MySQL Azure 資料庫的服務綁定連接。

```azurecli
az spring-cloud app binding mysql update --add
                                         --name -n
                                         --resource-group -g
                                         --service -s
                                         --database-name
                                         --key
                                         --username
```

| 必要參數 | |
| --- | ---: |
| --應用 | 應用程式的名稱。 |
| --name | 服務綁定的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --服務 -s | Azure 春雲的名稱。  您可以使用 配置預設服務`az configure --defaults spring-cloud=<name>`。 |

| 選擇性參數 | |
| --- | ---: |
| --資料庫名稱 | 資料庫的名稱。 |
| --key | 服務的 API 金鑰。 |
| --username | 資料庫訪問的使用者名。 |

## <a name="az-spring-cloud-app-binding-redis-add"></a>az 彈簧雲應用程式綁定 redis 添加

將 Redis 的 Azure 緩存與應用綁定。

```azurecli
az spring-cloud app binding redis add --app
                                      --name -n
                                      --resource-group -g
                                      --resource-id
                                      --service -s
                                      --disable-ssl
```

| 必要參數 | |
| --- | ---: |
| --應用 | 應用程式的名稱。 |
| --name | 服務綁定的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --資源識別碼 | 要綁定的服務的 Azure 資源識別碼。 |
| --服務 -s | Azure 春雲的名稱。  您可以使用 配置預設服務`az configure --defaults spring-cloud=<name>`。 |

| 選擇性參數 | |
| --- | ---: |
| --禁用-ssl | 禁用 TLS。 |

## <a name="az-spring-cloud-app-binding-redis-update"></a>az 彈簧雲應用程式綁定 redis 更新

更新 Redis 的 Azure 緩存的服務綁定。

| 必要參數 | |
| --- | ---: |
| --應用 | 應用程式的名稱。 |
| --name | 服務綁定的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --服務 -s | Azure 春雲的名稱。  您可以使用 配置預設服務`az configure --defaults spring-cloud=<name>`。 |

| 選擇性參數 | |
| --- | ---: |
| --禁用-ssl | 禁用 TLS。 |

## <a name="az-spring-cloud-app-deployment-create"></a>az 彈簧雲應用部署創建

為應用創建暫存部署。

要部署代碼或將設置更新到現有部署，請使用`az spring-cloud app deploy --deployment <staging-deployment>`或 "az 彈簧雲應用更新<staging deployment>- 部署"。

| 必要參數 | |
| --- | ---: |
| --應用 | 應用程式的名稱。 |
| --name | 服務綁定的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --服務 -s | Azure 春雲的名稱。  您可以使用 配置預設服務`az configure --defaults spring-cloud=<name>`。 |

| 選擇性參數 | |
| --- | ---: |
| --cpu | 每個實例的虛擬 CPU 內核數。  預設值：1 |
| --恩夫 | 空間分隔環境變數採用"鍵[值]"格式。 |
| --instance-count | 實例數。 預設值：1。 |
| --jar-path | 如果提供，則部署 jar。  否則，將當前資料夾部署為 tar。 |
| --jvm 選項 | 包含 JVM 選項的字串。  使用"*"而不是''來避免 shell 分析錯誤。 例如， `--jvm-options='-Xms1024m -Xmx2048m`. |
| --memory | 每個實例的 GB 記憶體數。 |
| --no-wait | 不要等待長時間運行的操作完成。 |
| --執行階段版本 | 應用中使用的語言的執行階段版本。  允許的值： `Java_11` `Java_8`. |
| --跳過克隆設置 | 通過克隆當前生產部署設置創建暫存部署。 |
| --目的模組 | 要部署的子模組。  從原始程式碼生成多個 jar 包時是必需的。 |
| --version | 部署版本。  如果未設置，則保持不變。 |

### <a name="examples"></a>範例

將原始程式碼部署到應用的新部署。  這將打包目前的目錄，使用 Pivotal Build 系統構建二進位檔案，然後進行部署。

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService
```

將預構建的 jar 部署到具有 JVM 選項和環境變數的應用。

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseStringDeDuplication" --env foo=bar
```

## <a name="az-spring-cloud-app-deployment-delete"></a>az 彈簧雲應用部署刪除

刪除應用的部署。

```azurecli
az spring-cloud app deployment delete --app
                                      --name -n
                                      --resource-group -g
                                      --service -s
```

| 必要參數 | |
| --- | ---: |
| --應用 | 應用程式的名稱。 |
| --name | 部署的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --服務 -s | Azure 春雲的名稱。  您可以使用 配置預設服務`az configure --defaults spring-cloud=<name>`。 |

## <a name="az-spring-cloud-app-deployment-list"></a>az 彈簧雲應用部署清單

列出應用中的所有部署。

```azurecli
az spring-cloud app deployment list --app
                                    --resource-group -g
                                    --service -s
```

| 必要參數 | |
| --- | ---: |
| --應用 | 應用程式的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --服務 -s | Azure 春雲的名稱。  您可以使用 配置預設服務`az configure --defaults spring-cloud=<name>`。 |

## <a name="az-spring-cloud-app-deployment-show"></a>az 彈簧雲應用部署顯示

顯示部署的詳細資訊。

```azurecli
az spring-cloud app deployment show --app
                                    --name -n
                                    --resource-group -g
                                    --service -s
```

| 必要參數 | |
| --- | ---: |
| --應用 | 應用程式的名稱。 |
| --name | 部署的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --服務 -s | Azure 春雲的名稱。  您可以使用 配置預設服務`az configure --defaults spring-cloud=<name>`。 |

## <a name="az-spring-cloud-config-server-clear"></a>az 彈簧雲佈建服務器清除

擦除佈建服務器中的所有配置設置。

```azurecli
az spring-cloud config-server clear --name
                                        --resource-group -g
```

| 必要參數 | |
| --- | ---: |
| --name | Azure 春雲的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |

## <a name="az-spring-cloud-config-server-set"></a>az 彈簧雲佈建服務器集

使用 YAML 檔在 Config 伺服器上設置配置設置。

```azurecli
az spring-cloud config-server set --config-file
                                  --name -n
                                  --resource-group -g
                                  --no-wait
```

| 必要參數 | |
| --- | ---: |
| --設定檔 | 用於配置配置配置的 YAML 清單的檔路徑。 |
| --name | Azure 春雲的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |

| 選擇性參數 | |
| --- | ---: |
| --no-wait | 不要為長時間運行的操作完成。

## <a name="az-spring-cloud-config-server-show"></a>az 彈簧雲佈建服務器顯示

顯示佈建服務器設置。

```azurecli
az spring-cloud config-server show --name -n
                                   --resource-group -g
```

| 必要參數 | |
| --- | ---: |
| --name | Azure 春雲的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |

## <a name="az-spring-cloud-config-server-git-set"></a>az 彈簧雲佈建服務器 git 集

設置佈建服務器的 git 屬性。  這將覆蓋所有現有的 git 屬性。

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

| 必要參數 | |
| --- | ---: |
| --name | Azure 春雲的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --尤裡 | 添加配置的 URI。 |

| 選擇性參數 | |
| --- | ---: |
| --延遲 | 將物件暫時存儲在本機快取中，而不是發送到 Azure。  用於`az cache`查看/清除。 |
| --主機鍵 | 添加配置的主機金鑰。 |
| --主機鍵演算法 | 附加配置的主機金鑰演算法。 |
| --標籤 | 添加配置的標籤。 |
| --password | 添加配置的密碼。 |
| --私密金鑰 | 添加配置的私密金鑰。 |
| --搜索路徑 | 添加配置的搜索路徑。 對多個路徑使用逗號分隔符號。 |
| --嚴格主機金鑰檢查 | 啟用對添加的配置進行嚴格的主機金鑰檢查。 |
| --username | 添加配置的使用者名。 |

## <a name="az-spring-cloud-config-server-git-repo-add"></a>az 彈簧雲佈建服務器 git 存儲庫添加

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

| 必要參數 | |
| --- | ---: |
| --name | Azure 春雲的名稱。 |
| --回購名稱 | 回購的 URI。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --尤裡 | 添加配置的 URI。 |

| 選擇性參數 | |
| --- | ---: |
| --延遲 | 將物件暫時存儲在本機快取中，而不是發送到 Azure。  用於`az cache`查看/清除。 |
| --主機鍵 | 添加配置的主機金鑰。 |
| --主機鍵演算法 | 附加配置的主機金鑰演算法。 |
| --標籤 | 添加配置的標籤。 |
| --password | 添加配置的密碼。 |
| --模式 | 回購的模式。  對多個路徑使用逗號分隔符號。|
| --私密金鑰 | 添加配置的私密金鑰。 |
| --搜索路徑 | 添加配置的搜索路徑。 對多個路徑使用逗號分隔符號。 |
| --嚴格主機金鑰檢查 | 啟用對添加的配置進行嚴格的主機金鑰檢查。 |
| --username | 添加配置的使用者名。 |

## <a name="az-spring-cloud-config-server-git-repo-list"></a>az 彈簧雲佈建服務器 git 存儲庫清單

列出佈建服務器中定義的所有 git 存儲庫

```azurecli
az spring-cloud config-server git repo list --name -n
                                       --resource-group -g
                                       --defer
```

| 必要參數 | |
| --- | ---: |
| --name | Azure 春雲的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |

| 選擇性參數 | |
| --- | ---: |
| --延遲 | 將物件暫時存儲在本機快取中，而不是發送到 Azure。  用於`az cache`查看/清除。 |

## <a name="az-spring-cloud-config-server-git-repo-remove"></a>az 彈簧雲佈建服務器 git 存儲庫刪除

從佈建服務器中刪除現有的 git 存儲庫配置。

```azurecli
az spring-cloud config-server git repo remove --name -n
                                         --repo-name
                                         --resource-group -g
                                         --defer
```

| 必要參數 | |
| --- | ---: |
| --name | Azure 春雲的名稱。 |
| --回購名稱 | 回購的 URI。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |

| 選擇性參數 | |
| --- | ---: |
| --延遲 | 將物件暫時存儲在本機快取中，而不是發送到 Azure。  用於`az cache`查看/清除。 |

## <a name="az-spring-cloud-test-endpoint-disable"></a>az 彈簧雲測試終結點禁用

禁用 Azure 春雲的測試終結點

```azurecli
az spring-cloud test-endpoint disable --name -n
                                      --resource-group -g
```

| 必要參數 | |
| --- | ---: |
| --name | Azure 春雲的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |

## <a name="az-spring-cloud-test-endpoint-enable"></a>az 彈簧雲測試終結點啟用

為 Azure 春雲啟用測試終結點。 

```azurecli
az spring-cloud test-endpoint enable --name -n
                                     --resource-group -g
```

| 必要參數 | |
| --- | ---: |
| --name | Azure 春雲的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |

## <a name="az-spring-cloud-test-endpoint-list"></a>az 彈簧雲測試終結點清單 

列出 Azure 春雲的可用測試終結點鍵。

```azurecli
az spring-cloud test-endpoint list --name -n
                                   --resource-group -g
                                   --app
                                   --deployment -d
```

| 必要參數 | |
| --- | ---: |
| --name | Azure 春雲的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |

| 選擇性參數 | |
| --- | ---: |
| --應用 | 應用程式的名稱。 |
| --部署-d | 應用的現有部署的名稱。  如果未指定，則預設為生產。 |

## <a name="az-spring-cloud-test-endpoint-renew-key"></a>az 彈簧雲測試端點續訂鍵

重新生成 Azure 春雲的測試終結點金鑰。

```azurecli
az spring-cloud test-endpoint renew-key --name -n
                                        --resource-group -g
                                        --type
```

| 必要參數 | |
| --- | ---: |
| --name | Azure 春雲的名稱。 |
| --resource-group -g | 資源群組的名稱。  您可以使用 `az configure --defaults group=<name>` 來設定預設群組。 |
| --類型 | 測試終結點金鑰的類型。  允許的值：主值、輔助值。 |
