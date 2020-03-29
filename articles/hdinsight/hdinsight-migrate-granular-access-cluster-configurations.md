---
title: 細微性基於角色的訪問 Azure HDInsight 群集配置
description: 瞭解在遷移到基於角色的 HDInsight 群集配置的精細訪問時所需的更改。
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/22/2019
ms.openlocfilehash: f1fdb9dffbe06430ea7e3eb9339e23f5239e4e36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310827"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>移轉至叢集組態中以角色為基礎的細微存取

我們正在引入一些重要的更改，以支援更細細微性的基於角色的訪問以獲取敏感資訊。 作為這些更改的一部分，如果您使用的是[受影響的實體/方案](#am-i-affected-by-these-changes)之一，則可能需要**在 2019 年 9 月 3 日之前**執行某些操作。

## <a name="what-is-changing"></a>變更內容為何？

以前，擁有擁有者、參與者或讀取器[RBAC 角色的](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)群集使用者可以通過 HDInsight API 獲取機密，因為任何具有`*/read`該許可權的人都可以使用這些角色。 機密被定義為可用於獲取比使用者角色應允許的更多提升訪問的值。 其中包括群集閘道 HTTP 憑據、存儲帳戶金鑰和資料庫憑據等值。

從 2019 年 9 月 3 日開始，`Microsoft.HDInsight/clusters/configurations/action`訪問這些機密需要許可權，這意味著具有 Reader 角色的使用者無法再訪問這些機密。 具有此許可權的角色是參與者、擁有者和新的 HDInsight 群集操作員角色（下面將對此進行說明）。

我們還引入了一個新的[HDInsight 群集操作員](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator)角色，該角色將能夠檢索機密，而無需授予參與者或擁有者的管理許可權。 總結：

| 角色                                  | 先前是                                                                                        | 展望未來       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| 讀取者                                | - 讀取存取，包括機密                                                                   | - 讀取存取，**不包括**機密 |           |   |   |
| HDInsight 集群操作員<br>（新角色） | N/A                                                                                              | - 讀取/寫入訪問，包括機密         |   |   |
| 參與者                           | - 讀取/寫入訪問，包括機密<br>- 創建和管理所有類型的 Azure 資源。     | 沒有變更 |
| 擁有者                                 | - 讀取/寫入訪問，包括機密<br>- 完全訪問所有資源<br>- 將存取權限委派給其他人 | 沒有變更 |

有關如何向使用者添加 HDInsight 群集操作員角色指派以授予其對群集機密的讀/寫存取權限的資訊，請參閱以下部分，[將 HDInsight 群集操作員角色指派添加到使用者](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user)。

## <a name="am-i-affected-by-these-changes"></a>我受到這些變化的影響嗎？

以下實體和方案受到影響：

- [API](#api)： 使用`/configurations``/configurations/{configurationName}`或 終結點的使用者。
- [適用于視覺化工作室代碼](#azure-hdinsight-tools-for-visual-studio-code)版本 1.1.1 或以下的 Azure HDInsight 工具。
- [適用于 IntelliJ](#azure-toolkit-for-intellij)版本 3.20.0 或以下的 Azure 工具組。
- [Azure 資料湖和流分析工具，適用于](#azure-data-lake-and-stream-analytics-tools-for-visual-studio)2.3.9000.1 版本以下的視覺化工作室。
- [用於 Eclipse](#azure-toolkit-for-eclipse)版本 3.15.0 或以下的 Azure 工具組。
- [適用於 .NET 的 SDK](#sdk-for-net)
    - [版本 1.x 或 2.x](#versions-1x-and-2x) `GetClusterConfigurations`：`GetConnectivitySettings`使用者`ConfigureHttpSettings`使用`EnableHttp``DisableHttp`配置操作擴展類 中的 、 、 或方法。
    - [版本 3.x 和上](#versions-3x-and-up)：使用`Get``Update``ConfigurationsOperationsExtensions`類`EnableHttp`中的`DisableHttp`、 、 或 方法的使用者。
- [Python 的 SDK](#sdk-for-python)：`get`使用`update``ConfigurationsOperations`類 中的 或 方法的使用者。
- [JAVA 的 SDK](#sdk-for-java)：`update`使用`get``ConfigurationsInner`類 中的 或 方法的使用者。
- [Go 的 SDK](#sdk-for-go)：`Get`使用`Update`或`ConfigurationsClient`方法的使用者。
- [Az.HDInsight PowerShell](#azhdinsight-powershell)低於 2.0.0 版本。
請參閱以下部分（或使用上述連結），查看方案的遷移步驟。

### <a name="api"></a>API

以下 API 將被更改或棄用：

- [**GET /配置/[配置名稱]（**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration)已刪除敏感資訊）
    - 以前用於獲取單個配置類型（包括機密）。
    - 從 2019 年 9 月 3 日開始，此 API 呼叫現在將返回省略機密的單個配置類型。 要獲取所有配置（包括機密），請使用新的 POST /配置調用。 要獲取閘道設置，請使用新的 POST/getGatewaySettings 呼叫。
- [**GET /配置**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration)（已棄用）
    - 以前用於獲取所有配置（包括機密）
    - 從 2019 年 9 月 3 日開始，此 API 呼叫將被棄用，不再受支援。 要獲取未來的所有配置，請使用新的 POST/配置調用。 要獲取省略的敏感參數的配置，請使用 GET /配置/[配置名稱]調用。
- [**POST /配置/[配置名稱]（**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)已棄用）
    - 以前用於更新閘道憑據。
    - 從 2019 年 9 月 3 日開始，此 API 呼叫將被棄用，不再受支援。 改用新的 POST/更新閘道設置。

添加了以下替換 API：</span>

- [**POST /配置**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - 使用此 API 獲取所有配置，包括機密。
- [**POST /獲取閘道設置**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - 使用此 API 獲取閘道設置。
- [**POST /更新閘道設置**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - 使用此 API 更新閘道設置（使用者名和/或密碼）。

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>用於視覺化工作室代碼的 Azure HDInsight 工具

如果使用版本 1.1.1 或以下，請更新到[最新版本的 Azure HDInsight 工具，用於視覺化工作室代碼](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false)以避免中斷。

### <a name="azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ

如果使用版本 3.20.0 或以下，請更新到[IntelliJ 外掛程式的 Azure 工具組的最新版本](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij)，以避免中斷。

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>用於視覺化工作室的 Azure 資料湖和流分析工具

更新至 Azure 資料湖版本 2.3.9000.1 或更高版本[，為 Visual Studio 更新流分析工具](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview)以避免中斷。  有關更新的説明，請參閱我們的文檔，[更新視覺化工作室的資料湖工具](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio)。

### <a name="azure-toolkit-for-eclipse"></a>適用於 Eclipse 的 Azure 工具組

如果使用版本 3.15.0 或以下，請更新到[Eclipse 的 Azure 工具組的最新版本](https://marketplace.eclipse.org/content/azure-toolkit-eclipse)以避免中斷。

### <a name="sdk-for-net"></a>適用於 .NET 的 SDK

#### <a name="versions-1x-and-2x"></a>版本 1.x 和 2.x

更新至 .NET 的 HDInsight SDK[版本 2.1.0。](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) 如果您使用受這些更改影響的方法，則可能需要最少的代碼修改：

- `ClusterOperationsExtensions.GetClusterConfigurations`**將不再返回敏感參數，** 如存儲金鑰（核心網站）或 HTTP 憑據（閘道）。
    - 若要檢索所有配置（包括敏感參數），請使用`ClusterOperationsExtensions.ListConfigurations`"前進"。  請注意，具有"讀取器"角色的使用者將無法使用此方法。 這允許對哪些使用者可以訪問群集的敏感資訊進行精細控制。
    - 若要僅檢索 HTTP 閘道憑據`ClusterOperationsExtensions.GetGatewaySettings`，請使用 。

- `ClusterOperationsExtensions.GetConnectivitySettings`現已棄用，並已替換為`ClusterOperationsExtensions.GetGatewaySettings`。

- `ClusterOperationsExtensions.ConfigureHttpSettings`現已棄用，並已替換為`ClusterOperationsExtensions.UpdateGatewaySettings`。

- `ConfigurationsOperationsExtensions.EnableHttp`並且`DisableHttp`現在被棄用了。 現在始終啟用 HTTP，因此不再需要這些方法。

#### <a name="versions-3x-and-up"></a>版本 3.x 和上版本

更新至 .NET 的 HDInsight SDK 版本[5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0)或更高版本。 如果您使用受這些更改影響的方法，則可能需要最少的代碼修改：

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet)**將不再返回敏感參數，** 如存儲金鑰（核心網站）或 HTTP 憑據（閘道）。
    - 若要檢索所有配置（包括敏感參數），請使用[`ConfigurationOperationsExtensions.List`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet)"前進"。請注意，具有"讀取器"角色的使用者將無法使用此方法。 這允許對哪些使用者可以訪問群集的敏感資訊進行精細控制。 
    - 若要僅檢索 HTTP 閘道憑據[`ClusterOperationsExtensions.GetGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet)，請使用 。 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet)現已棄用，並已替換為[`ClusterOperationsExtensions.UpdateGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet)。 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet)並且[`DisableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet)現在被棄用了。 現在始終啟用 HTTP，因此不再需要這些方法。

### <a name="sdk-for-python"></a>適用於 Python 的 SDK

更新至適用于 Python 的 HDInsight SDK[版本 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/)或更高版本。 如果您使用受這些更改影響的方法，則可能需要最少的代碼修改：

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-)**將不再返回敏感參數，** 如存儲金鑰（核心網站）或 HTTP 憑據（閘道）。
    - 若要檢索所有配置（包括敏感參數），請使用[`ConfigurationsOperations.list`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-)"前進"。請注意，具有"讀取器"角色的使用者將無法使用此方法。 這允許對哪些使用者可以訪問群集的敏感資訊進行精細控制。 
    - 若要僅檢索 HTTP 閘道憑據[`ClusterOperations.get_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-)，請使用 。
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)現已棄用，並已替換為[`ClusterOperations.update_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)。

### <a name="sdk-for-java"></a>SDK For JAVA

更新至 JAVA 的 HDInsight SDK[版本 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar)或更高版本。 如果您使用受這些更改影響的方法，則可能需要最少的代碼修改：

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get)**將不再返回敏感參數，** 如存儲金鑰（核心網站）或 HTTP 憑據（閘道）。
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update)現在被棄用了。

### <a name="sdk-for-go"></a>用於 GO 的 SDK

更新至適用于 Go 的 HDInsight SDK[版本 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight)或更高版本。 如果您使用受這些更改影響的方法，則可能需要最少的代碼修改：

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get)**將不再返回敏感參數，** 如存儲金鑰（核心網站）或 HTTP 憑據（閘道）。
    - 若要檢索所有配置（包括敏感參數），請使用[`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List)"前進"。請注意，具有"讀取器"角色的使用者將無法使用此方法。 這允許對哪些使用者可以訪問群集的敏感資訊進行精細控制。 
    - 若要僅檢索 HTTP 閘道憑據[`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings)，請使用 。
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update)現已棄用，並已替換為[`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings)。

### <a name="azhdinsight-powershell"></a>阿茲HDInsight電源外殼
更新到[Az PowerShell 版本 2.0.0](https://www.powershellgallery.com/packages/Az)或更高版本，以避免中斷。  如果您使用受這些更改影響的方法，則可能需要最少的代碼修改。
- `Grant-AzHDInsightHttpServicesAccess`現已棄用，並已替換為新的`Set-AzHDInsightGatewayCredential`Cmdlet。
- `Get-AzHDInsightJobOutput`已更新以支援對存儲金鑰的基於角色的精細訪問。
    - 具有 HDInsight 叢集操作員、參與者或擁有者角色的使用者將不受影響。
    - 只有 Reader 角色的使用者需要顯式指定參數`DefaultStorageAccountKey`。
- `Revoke-AzHDInsightHttpServicesAccess`現在被棄用了。 現在始終啟用 HTTP，因此不再需要此 Cmdlet。
 請參閱[az。有關更多詳細資訊的 HDInsight 遷移指南](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight)。

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>向使用者添加 HDInsight 群集操作員角色指派

具有["擁有者"](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)角色的使用者可以將[HDInsight 群集操作員](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator)角色指派給您希望對敏感 HDInsight 群集配置值（如群集閘道憑據和存儲帳戶金鑰）進行讀/寫存取權限的使用者。

### <a name="using-the-azure-cli"></a>使用 Azure CLI

添加此角色指派的最簡單方法是使用 Azure CLI`az role assignment create`中的命令。

> [!NOTE]
> 此命令必須由具有"擁有者"角色的使用者運行，因為只有使用者才能授予這些許可權。 是`--assignee`要為其分配 HDInsight 群集操作員角色的使用者的服務主體或電子郵件地址的名稱。 如果您收到的許可權錯誤不足，請參閱下面的常見問題解答。

#### <a name="grant-role-at-the-resource-cluster-level"></a>在資源（群集）級別授予角色

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>在資源組級別授予角色

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>訂閱級別的授予角色

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>使用 Azure 入口網站

或者，可以使用 Azure 門戶向使用者添加 HDInsight 群集操作員角色指派。 請參閱文檔，[使用 RBAC 和 Azure 門戶管理對 Azure 資源的訪問 - 添加角色指派](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment)。

## <a name="faq"></a>常見問題集

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>我在更新 API 要求和/或工具之後，為何會看到 403 (禁止) 回應？

群集配置現在位於基於角色的精細存取控制後面，`Microsoft.HDInsight/clusters/configurations/*`需要訪問它們的許可權。 要獲得此許可權，請將 HDInsight 群集操作員、參與者或擁有者角色指派給嘗試訪問配置的使用者或服務主體。

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>為什麼在運行 Azure CLI 命令將 HDInsight 群集操作員角色指派給其他使用者或服務主體時，還要看到"完成操作的許可權不足"？

除了具有擁有者角色外，執行命令的使用者或服務主體還需要具有足夠的 AAD 許可權來查找受讓人的物件識別碼。 此消息指示 AAD 許可權不足。 嘗試將`-–assignee`參數替換為`–assignee-object-id`並提供給受讓人的物件識別碼 作為參數，而不是名稱（或託管標識中的主體 ID）。 有關詳細資訊，請參閱[az 角色指派創建文檔](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create)的可選參數部分。

如果這仍然不起作用，請與您的 AAD 管理員聯繫以獲取正確的許可權。

### <a name="what-will-happen-if-i-take-no-action"></a>如果我不採取行動，會發生什麼？

從 2019 年 9 `GET /configurations` `POST /configurations/gateway`月 3 日開始，呼叫將不再返回`GET /configurations/{configurationName}`任何資訊，呼叫將不再返回敏感參數，如存儲帳戶金鑰或群集密碼。 相應的 SDK 方法和 PowerShell Cmdlet 也是如此。

如果您使用的是上述 Visual Studio、VSCode、IntelliJ 或 Eclipse 的工具之一的舊版本，則在您更新之前，這些工具將不再起作用。

有關詳細資訊，請參閱本文檔中有關您的方案的相應部分。
