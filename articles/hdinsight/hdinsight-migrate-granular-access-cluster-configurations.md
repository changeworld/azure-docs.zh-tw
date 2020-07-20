---
title: 以角色為基礎的細微存取 Azure HDInsight 叢集設定
description: 瞭解遷移至 HDInsight 叢集設定的細微角色型存取所需的變更。
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: 058300dca3e7eae41b7d8010e1ca5ee7d4cdcf3a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82598465"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>移轉至叢集組態中以角色為基礎的細微存取

我們即將介紹一些重要的變更，以支援更精細的角色型存取來取得機密資訊。 在這些變更中，如果您使用其中一種[受影響的實體/案例](#am-i-affected-by-these-changes)，則可能需要執行某些動作（ **2019 年9月3日**）。

## <a name="what-is-changing"></a>變更內容為何？

先前，具有「擁有者」、「參與者」或「讀者」 [RBAC 角色](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)的叢集使用者，可以透過 HDInsight API 取得秘密，因為他們可供具備該許可權的任何人使用 `*/read` 。 密碼會定義為值，可用來取得比使用者角色應允許更高的存取權。 其中包括叢集閘道 HTTP 認證、儲存體帳戶金鑰和資料庫認證之類的值。

從2019年9月3日開始，存取這些密碼將需要 `Microsoft.HDInsight/clusters/configurations/action` 許可權，這表示使用者無法再以「讀取者」角色來存取它們。 具有此許可權的角色是「參與者」、「擁有者」和「新的 HDInsight 叢集操作員」角色（如下所示）。

我們也引進了新的[HDInsight 叢集操作員](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator)角色，將能夠在不被授與參與者或擁有者的系統管理許可權的情況下，取得密碼。 總括來說：

| 角色                                  | 先前是                                                                                        | 往後       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| 讀取者                                | -讀取權限，包括秘密。                                                                   | -讀取權限，不**包括**秘密 |           |   |   |
| HDInsight 叢集操作員<br>（新角色） | 不適用                                                                                              | -讀取/寫入存取權，包括秘密         |   |   |
| 參與者                           | -讀取/寫入存取權，包括秘密。<br>-建立和管理所有類型的 Azure 資源。<br>-執行腳本動作。     | 沒有變更 |
| 擁有者                                 | -包含秘密的讀取/寫入存取權。<br>-所有資源的完整存取權<br>-將存取權委派給其他人。<br>-執行腳本動作。 | 沒有變更 |

如需如何將 HDInsight 叢集操作員角色指派新增至使用者以授與對叢集密碼之讀取/寫入存取權的詳細資訊，請參閱下一節[將 hdinsight 叢集操作員角色指派新增至使用者](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user)。

## <a name="am-i-affected-by-these-changes"></a>我是否受到這些變更的影響？

下列實體和案例會受到影響：

- [API](#api)：使用 `/configurations` 或端點的使用者 `/configurations/{configurationName}` 。
- [適用于 Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) 1.1.1 版或以下版本的 Azure HDInsight 工具。
- [Azure Toolkit for IntelliJ](#azure-toolkit-for-intellij)版本3.20.0 或以下。
- [Azure Data Lake 和串流分析適用于 Visual Studio 版本2.3.9000.1 的工具](#azure-data-lake-and-stream-analytics-tools-for-visual-studio)。
- [Azure Toolkit for Eclipse](#azure-toolkit-for-eclipse)版本3.15.0 或以下。
- [適用於 .NET 的 SDK](#sdk-for-net)
    - [版本1.x 或](#versions-1x-and-2x)2.x：使用 `GetClusterConfigurations` ConfigurationsOperationsExtensions 類別的、、 `GetConnectivitySettings` `ConfigureHttpSettings` `EnableHttp` 或 `DisableHttp` 方法的使用者。
    - 3.x[和更新版本](#versions-3x-and-up)：使用類別的、、 `Get` `Update` `EnableHttp` 或方法的使用者 `DisableHttp` `ConfigurationsOperationsExtensions` 。
- [適用于 Python 的 SDK](#sdk-for-python)：使用 `get` 類別的或方法的使用者 `update` `ConfigurationsOperations` 。
- [適用于 JAVA 的 SDK](#sdk-for-java)：使用 `update` 類別的或方法的使用者 `get` `ConfigurationsInner` 。
- [適用于 Go 的 SDK](#sdk-for-go)：使用 `Get` `Update` 來自結構之或方法的使用者 `ConfigurationsClient` 。
- [Az. HDInsight PowerShell](#azhdinsight-powershell)版本2.0.0。
請參閱下列各節（或使用上述連結）查看您案例的遷移步驟。

### <a name="api"></a>API

下列 Api 將會變更或淘汰：

- [**取得/configurations/{configurationName}**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) （已移除敏感性資訊）
    - 先前用來取得個別的設定類型（包括密碼）。
    - 從2019年9月3日開始，此 API 呼叫現在會傳回個別設定類型，並省略秘密。 若要取得所有設定（包括秘密），請使用新的 POST/configurations 呼叫。 若只要取得閘道設定，請使用新的 POST/getGatewaySettings 呼叫。
- [**GET/configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) （已被取代）
    - 先前用來取得所有設定（包括秘密）
    - 從2019年9月3日開始，此 API 呼叫將會被取代，不再受到支援。 若要繼續進行所有設定，請使用新的 POST/configurations 呼叫。 若要取得已省略敏感性參數的設定，請使用 GET/configurations/{configurationName} 呼叫。
- [**POST/configurations/{configurationName}**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) （已淘汰）
    - 先前用來更新閘道認證的。
    - 從2019年9月3日開始，此 API 呼叫將會被取代，不再支援。 請改用新的文章/updateGatewaySettings。

已新增下列取代 Api：</span>

- [**張貼/configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - 使用此 API 來取得所有設定，包括秘密。
- [**張貼/getGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - 使用此 API 來取得閘道設定。
- [**張貼/updateGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - 使用此 API 來更新閘道設定（使用者名稱和/或密碼）。

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>適用于 Visual Studio Code 的 Azure HDInsight 工具

如果您使用的版本為1.1.1 或以下，請更新為[Visual Studio Code 的最新版本 Azure HDInsight 工具](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false)，以避免中斷。

### <a name="azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ

如果您使用的是 [版本 3.20.0] 或以下，請更新至[最新版本的 Azure Toolkit for IntelliJ 外掛程式](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij)以避免中斷。

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>適用于 Visual Studio 的 Azure Data Lake 和串流分析工具

Azure Data Lake 的版本2.3.9000.1 或更新版本[，以及 Visual Studio 的串流分析工具](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview)，以避免中斷。  如需更新的協助，請參閱我們[的檔：更新 Visual Studio 的 Data Lake 工具](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio)。

### <a name="azure-toolkit-for-eclipse"></a>適用於 Eclipse 的 Azure 工具組

如果您使用的是 [版本 3.15.0] 或以下，請更新至[最新版本的 Azure Toolkit for Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse)以避免中斷。

### <a name="sdk-for-net"></a>適用於 .NET 的 SDK

#### <a name="versions-1x-and-2x"></a>1.x 和2.x 版

更新為適用于 .NET 的 HDInsight SDK[版本 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) 。 如果您使用受這些變更影響的方法，可能需要最少的程式碼修改：

- `ClusterOperationsExtensions.GetClusterConfigurations`將不會再傳回如儲存體金鑰（核心網站）或 HTTP 認證（閘道）之類的**敏感參數**。
    - 若要取出所有設定（包括機密參數），請使用 [ `ClusterOperationsExtensions.ListConfigurations` 繼續]。  請注意，具有「讀取者」角色的使用者將無法使用這個方法。 這可讓您更精確地控制哪些使用者可以存取叢集的機密資訊。
    - 若只要取得 HTTP 閘道認證，請使用 `ClusterOperationsExtensions.GetGatewaySettings` 。

- `ClusterOperationsExtensions.GetConnectivitySettings`現在已被取代，並已由取代 `ClusterOperationsExtensions.GetGatewaySettings` 。

- `ClusterOperationsExtensions.ConfigureHttpSettings`現在已被取代，並已由取代 `ClusterOperationsExtensions.UpdateGatewaySettings` 。

- `ConfigurationsOperationsExtensions.EnableHttp`和 `DisableHttp` 現在已被取代。 HTTP 現在一律會啟用，因此不再需要這些方法。

#### <a name="versions-3x-and-up"></a>3.x 版和更新版本

更新為適用于 .NET 的 HDInsight SDK 第[5.0.0 版](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0)或更新版本。 如果您使用受這些變更影響的方法，可能需要最少的程式碼修改：

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet)將不會再傳回如儲存體金鑰（核心網站）或 HTTP 認證（閘道）之類的**敏感參數**。
    - 若要取出所有設定（包括機密參數），請使用 [ [`ConfigurationOperationsExtensions.List`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) 繼續]。請注意，具有「讀取者」角色的使用者將無法使用這個方法。 這可讓您更精確地控制哪些使用者可以存取叢集的機密資訊。 
    - 若只要取得 HTTP 閘道認證，請使用 [`ClusterOperationsExtensions.GetGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet) 。 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet)現在已被取代，並已由取代 [`ClusterOperationsExtensions.UpdateGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet) 。 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet)和 [`DisableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) 現在已被取代。 HTTP 現在一律會啟用，因此不再需要這些方法。

### <a name="sdk-for-python"></a>適用於 Python 的 SDK

更新為適用于 Python 的 HDInsight SDK [1.0.0 版](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/)或更新版本。 如果您使用受這些變更影響的方法，可能需要最少的程式碼修改：

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-)將不會再傳回如儲存體金鑰（核心網站）或 HTTP 認證（閘道）之類的**敏感參數**。
    - 若要取出所有設定（包括機密參數），請使用 [ [`ConfigurationsOperations.list`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) 繼續]。請注意，具有「讀取者」角色的使用者將無法使用這個方法。 這可讓您更精確地控制哪些使用者可以存取叢集的機密資訊。 
    - 若只要取得 HTTP 閘道認證，請使用 [`ClusterOperations.get_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) 。
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)現在已被取代，並已由取代 [`ClusterOperations.update_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) 。

### <a name="sdk-for-java"></a>適用于 JAVA 的 SDK

更新為適用于 JAVA 的 HDInsight SDK [1.0.0 版](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar)或更新版本。 如果您使用受這些變更影響的方法，可能需要最少的程式碼修改：

- `ConfigurationsInner.get`將不會再傳回如儲存體金鑰（核心網站）或 HTTP 認證（閘道）之類的**敏感參數**。
- `ConfigurationsInner.update`現在已被取代。

### <a name="sdk-for-go"></a>適用于 Go 的 SDK

更新為適用于 Go 的 HDInsight SDK[版本 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight)或更新版本。 如果您使用受這些變更影響的方法，可能需要最少的程式碼修改：

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get)將不會再傳回如儲存體金鑰（核心網站）或 HTTP 認證（閘道）之類的**敏感參數**。
    - 若要取出所有設定（包括機密參數），請使用 [ [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) 繼續]。請注意，具有「讀取者」角色的使用者將無法使用這個方法。 這可讓您更精確地控制哪些使用者可以存取叢集的機密資訊。 
    - 若只要取得 HTTP 閘道認證，請使用 [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings) 。
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update)現在已被取代，並已由取代 [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings) 。

### <a name="azhdinsight-powershell"></a>Az HDInsight PowerShell
更新至[Az PowerShell version 2.0.0](https://www.powershellgallery.com/packages/Az)或更新版本以避免中斷。  如果您使用受這些變更影響的方法，可能需要最少的程式碼修改。
- `Grant-AzHDInsightHttpServicesAccess`現在已被取代，並已由新的 `Set-AzHDInsightGatewayCredential` Cmdlet 取代。
- `Get-AzHDInsightJobOutput`已更新為支援儲存體金鑰的細微角色型存取。
    - 具有 HDInsight 叢集操作員、參與者或擁有者角色的使用者將不受影響。
    - 只有讀取者角色的使用者必須 `DefaultStorageAccountKey` 明確指定參數。
- `Revoke-AzHDInsightHttpServicesAccess`現在已被取代。 HTTP 現在一律會啟用，因此不再需要此 Cmdlet。
 請參閱[az。HDInsight 遷移指南](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight)以取得詳細資料。

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>將 HDInsight 叢集操作員角色指派新增至使用者

具有「[擁有](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)者」角色的使用者可以將[hdinsight 叢集操作員](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator)角色指派給您想要擁有敏感性 HDInsight 叢集設定值之讀取/寫入存取權的使用者（例如叢集閘道認證和儲存體帳戶金鑰）。

### <a name="using-the-azure-cli"></a>使用 Azure CLI

新增此角色指派最簡單的方式，就是使用 `az role assignment create` Azure CLI 中的命令。

> [!NOTE]
> 此命令必須由具有「擁有者」角色的使用者執行，因為只有他們可以授與這些許可權。 `--assignee`是您要指派 HDInsight 叢集操作員角色之使用者的服務主體或電子郵件地址的名稱。 如果您收到許可權不足的錯誤，請參閱下列常見問題。

#### <a name="grant-role-at-the-resource-cluster-level"></a>授與資源（叢集）層級的角色

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>授與資源群組層級的角色

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>授與訂用帳戶層級的角色

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>使用 Azure 入口網站

您也可以使用 Azure 入口網站，將 HDInsight 叢集操作員角色指派新增至使用者。 請參閱[使用 RBAC 來管理對 Azure 資源的存取和 Azure 入口網站新增角色指派](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment)中的檔。

## <a name="faq"></a>常見問題集

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>我在更新 API 要求和/或工具之後，為何會看到 403 (禁止) 回應？

叢集設定現在是以更細微的角色型存取控制為基礎，而且需要 `Microsoft.HDInsight/clusters/configurations/*` 許可權才能存取。 若要取得此許可權，請將「HDInsight 叢集操作員」、「參與者」或「擁有者」角色指派給嘗試存取設定的使用者或服務主體。

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>當執行 Azure CLI 命令將 HDInsight 叢集操作員角色指派給另一個使用者或服務主體時，為什麼會看到「許可權不足，無法完成作業」？

除了擁有擁有者角色以外，執行命令的使用者或服務主體必須擁有足夠的 Azure AD 許可權，才能查詢受託人的物件識別碼。 此訊息表示 Azure AD 許可權不足。 嘗試將 `-–assignee` 引數取代 `–assignee-object-id` 為，並提供受託人的物件識別碼做為參數，而不是名稱（如果是受控識別，則為主體識別碼）。 如需詳細資訊，請參閱[az role 指派建立檔](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create)的選擇性參數一節。

如果仍然無法解決問題，請洽詢您的 Azure AD 系統管理員，以取得正確的許可權。

### <a name="what-will-happen-if-i-take-no-action"></a>如果我沒有採取任何動作，會發生什麼事？

從2019年9月3日開始，呼叫 `GET /configurations` `POST /configurations/gateway` 將不會再傳回任何資訊，而且 `GET /configurations/{configurationName}` 呼叫將不會再傳回敏感性參數，例如儲存體帳戶金鑰或叢集密碼。 對應的 SDK 方法和 PowerShell Cmdlet 也是如此。

如果您使用上述其中一個工具的舊版 Visual Studio、VSCode、IntelliJ 或 Eclipse，則在您更新之前，將無法再運作。

如需詳細資訊，請參閱本檔中適用于您案例的對應章節。
