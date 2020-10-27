---
title: 以角色為基礎的細微存取 Azure HDInsight 叢集設定
description: 瞭解遷移至更細微的 HDInsight 叢集設定角色型存取所需的變更。
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: d2e9c1fe89866511f8eae0b900563471cd6e52e9
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533303"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>移轉至叢集組態中以角色為基礎的細微存取

我們會介紹一些重要的變更，以支援更精細的角色型存取來取得機密資訊。 作為這些變更的一部分，如果您使用其中一個 [受影響的實體/案例](#am-i-affected-by-these-changes)，則可能會在 **2019 年9月3日** 需要某些動作。

## <a name="what-is-changing"></a>變更內容為何？

先前，擁有擁有者、參與者或讀者 [Azure 角色](../role-based-access-control/rbac-and-directory-admin-roles.md)的叢集使用者可以透過 HDInsight API 取得秘密，因為這些使用者可供具有該許可權的任何人使用 `*/read` 。 密碼會定義為值，可用來取得比使用者角色應允許更高的存取權。 這些包括叢集閘道 HTTP 認證、儲存體帳戶金鑰和資料庫認證等值。

從2019年9月3日開始，存取這些秘密將需要 `Microsoft.HDInsight/clusters/configurations/action` 許可權，這表示使用者無法再存取具有「讀取者」角色的使用者。 具有此許可權的角色是「參與者」、「擁有者」和「新的 HDInsight 叢集操作員」角色 (詳細資訊) 。

我們也引進了一個新的 [HDInsight 叢集操作員](../role-based-access-control/built-in-roles.md#hdinsight-cluster-operator) 角色，此角色將能夠在不授與參與者或擁有者的系統管理許可權的情況下，取得秘密。 總括來說：

| 角色                                  | 先前是                                                                                        | 往後       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| 讀取者                                | -讀取存取權，包括秘密。                                                                   | -讀取存取權， **排除** 秘密 |           |   |   |
| HDInsight 叢集操作員<br> (新的角色)  | 不適用                                                                                              | -讀取/寫入存取權，包括秘密         |   |   |
| 參與者                           | -讀取/寫入存取權，包括秘密。<br>-建立和管理所有類型的 Azure 資源。<br>-執行腳本動作。     | 沒有變更 |
| 擁有者                                 | -讀取/寫入存取權，包括秘密。<br>-所有資源的完整存取權<br>-將存取權委派給其他人。<br>-執行腳本動作。 | 沒有變更 |

如需如何新增 HDInsight 叢集操作員角色指派給使用者，以授與他們對叢集秘密的讀取/寫入存取權的相關資訊，請參閱下一節， [將 HDInsight 叢集操作員角色指派新增至使用者](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user)。

## <a name="am-i-affected-by-these-changes"></a>這些變更是否受到影響？

下列實體和案例會受到影響：

- [API](#api)：使用 `/configurations` 或端點的使用者 `/configurations/{configurationName}` 。
- [適用于 Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) 1.1.1 版或以下版本的 Azure HDInsight 工具。
- [Azure Toolkit for IntelliJ](#azure-toolkit-for-intellij) 版本3.20.0 或以下版本。
- [Azure Data Lake 和 Stream Analytics 工具，適用于 Visual Studio](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) 以下版本的2.3.9000.1。
- [Azure Toolkit for Eclipse](#azure-toolkit-for-eclipse) 版本3.15.0 或以下版本。
- [適用於 .NET 的 SDK](#sdk-for-net)
    - 1.x[或2.x 版](#versions-1x-and-2x)：使用 `GetClusterConfigurations` disableHTTP 類別中的、 `GetConnectivitySettings` 、 `ConfigureHttpSettings` `EnableHttp` 或 `DisableHttp` 方法的使用者。
    - 3.x[和更新版本](#versions-3x-and-up)：使用 `Get` 類別的、 `Update` 、 `EnableHttp` 或 `DisableHttp` 方法 `ConfigurationsOperationsExtensions` 的使用者。
- [適用于 Python 的 SDK](#sdk-for-python)：使用 `get` 類別的或方法的使用者 `update` `ConfigurationsOperations` 。
- [適用于 JAVA 的 SDK](#sdk-for-java)：使用 `update` 類別的或方法的使用者 `get` `ConfigurationsInner` 。
- [適用于 Go 的 SDK](#sdk-for-go)：使用 `Get` 結構中的或方法的使用者 `Update` `ConfigurationsClient` 。
- [Az. HDInsight PowerShell](#azhdinsight-powershell) 版本2.0.0。
請參閱以下各節 (或使用上述連結) 來查看您案例的遷移步驟。

### <a name="api"></a>API

下列 Api 將會變更或淘汰：

- [**取得/configurations/{configurationName}**](/rest/api/hdinsight/hdinsight-cluster#get-configuration) (移除的機密資訊) 
    - 之前用來取得個別設定類型 (包括) 的秘密。
    - 從2019年9月3日開始，此 API 呼叫現在會傳回已省略秘密的個別設定類型。 若要取得所有設定，包括秘密，請使用新的 POST/configurations 呼叫。 若要只取得閘道設定，請使用新的 POST/getGatewaySettings 呼叫。
- [**取得/configurations**](/rest/api/hdinsight/hdinsight-cluster#get-configuration) (已淘汰的) 
    - 之前用來取得所有設定 (包括秘密) 
    - 從2019年9月3日開始，此 API 呼叫將會被取代，而且不再受到支援。 若要繼續進行所有設定，請使用新的 POST/configurations 通話。 若要取得已省略敏感性參數的設定，請使用 GET/configurations/{configurationName} 呼叫。
- [**/Configurations/{configurationName} 後**](/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (已淘汰) 
    - 之前用來更新閘道認證。
    - 從2019年9月3日開始，此 API 呼叫將會被取代，不再受到支援。 請改用新的文章/updateGatewaySettings。

已新增下列取代 Api：</span>

- [**張貼/configurations**](/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - 使用此 API 來取得所有設定，包括秘密。
- [**張貼/getGatewaySettings**](/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - 使用此 API 來取得閘道設定。
- [**張貼/updateGatewaySettings**](/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - 使用此 API 來更新 (使用者名稱和/或密碼) 的閘道設定。

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>適用于 Visual Studio Code 的 Azure HDInsight 工具

如果您使用1.1.1 版或更新版本，請更新為 [適用于 Visual Studio Code 的 Azure HDInsight 工具的最新版本](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) ，以避免中斷。

### <a name="azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ

如果您使用的是3.20.0 版或更新版本，請更新至 [最新版的 Azure Toolkit for IntelliJ 外掛程式](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) 以避免中斷。

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>適用于 Visual Studio 的 Azure Data Lake 和串流分析工具

更新為 Azure Data Lake 的版本2.3.9000.1 或更新版本 [，以及適用于 Visual Studio 的串流分析工具](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) ，以避免中斷。  如需更新的說明，請參閱我們 [的檔、更新 Data Lake Tools for Visual Studio](./hadoop/apache-hadoop-visual-studio-tools-get-started.md#update-data-lake-tools-for-visual-studio)。

### <a name="azure-toolkit-for-eclipse"></a>適用於 Eclipse 的 Azure 工具組

如果您使用的是3.15.0 版或更新版本，請更新至 [最新版本的 Azure Toolkit for Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) ，以避免中斷。

### <a name="sdk-for-net"></a>適用於 .NET 的 SDK

#### <a name="versions-1x-and-2x"></a>1.x 和2.x 版

更新為適用于 .NET 的 HDInsight SDK [版本 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) 。 如果您使用受這些變更影響的方法，則可能需要進行少量的程式碼修改：

- `ClusterOperationsExtensions.GetClusterConfigurations` 將 **不會再傳回機密參數** ，例如 (核心) 的儲存體金鑰，或 (閘道) 的 HTTP 認證。
    - 若要抓取所有設定，包括敏感性參數，請 `ClusterOperationsExtensions.ListConfigurations` 繼續使用。  請注意，具有「讀者」角色的使用者將無法使用此方法。 這可讓您更精確地控制哪些使用者可以存取叢集的機密資訊。
    - 若只取出 HTTP 閘道認證，請使用 `ClusterOperationsExtensions.GetGatewaySettings` 。

- `ClusterOperationsExtensions.GetConnectivitySettings` 現在已被取代，並已由取代 `ClusterOperationsExtensions.GetGatewaySettings` 。

- `ClusterOperationsExtensions.ConfigureHttpSettings` 現在已被取代，並已由取代 `ClusterOperationsExtensions.UpdateGatewaySettings` 。

- `ConfigurationsOperationsExtensions.EnableHttp` 和 `DisableHttp` 現在已被取代。 現在一律會啟用 HTTP，因此不再需要這些方法。

#### <a name="versions-3x-and-up"></a>3.x 版和更新版本

更新為適用于 .NET 的 HDInsight SDK 第 [5.0.0 版](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) 或更新版本。 如果您使用受這些變更影響的方法，則可能需要進行少量的程式碼修改：

- [`ConfigurationOperationsExtensions.Get`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet&preserve-view=true) 將 **不會再傳回機密參數** ，例如 (核心) 的儲存體金鑰，或 (閘道) 的 HTTP 認證。
    - 若要抓取所有設定，包括敏感性參數，請 [`ConfigurationOperationsExtensions.List`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet&preserve-view=true) 繼續使用。請注意，具有「讀者」角色的使用者將無法使用此方法。 這可讓您更精確地控制哪些使用者可以存取叢集的機密資訊。 
    - 若只取出 HTTP 閘道認證，請使用 [`ClusterOperationsExtensions.GetGatewaySettings`](/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet&preserve-view=true) 。 
- [`ConfigurationsOperationsExtensions.Update`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet&preserve-view=true) 現在已被取代，並已由取代 [`ClusterOperationsExtensions.UpdateGatewaySettings`](/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet&preserve-view=true) 。 
- [`ConfigurationsOperationsExtensions.EnableHttp`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet&preserve-view=true) 和 [`DisableHttp`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet&preserve-view=true) 現在已被取代。 現在一律會啟用 HTTP，因此不再需要這些方法。

### <a name="sdk-for-python"></a>適用於 Python 的 SDK

更新至適用于 Python 的 HDInsight SDK [1.0.0 版](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) 或更新版本。 如果您使用受這些變更影響的方法，則可能需要進行少量的程式碼修改：

- [`ConfigurationsOperations.get`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) 將 **不會再傳回機密參數** ，例如 (核心) 的儲存體金鑰，或 (閘道) 的 HTTP 認證。
    - 若要抓取所有設定，包括敏感性參數，請 [`ConfigurationsOperations.list`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) 繼續使用。請注意，具有「讀者」角色的使用者將無法使用此方法。 這可讓您更精確地控制哪些使用者可以存取叢集的機密資訊。 
    - 若只取出 HTTP 閘道認證，請使用 [`ClusterOperations.get_gateway_settings`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) 。
- [`ConfigurationsOperations.update`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) 現在已被取代，並已由取代 [`ClusterOperations.update_gateway_settings`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) 。

### <a name="sdk-for-java"></a>適用于 JAVA 的 SDK

更新至 [1.0.0 版](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar) 或更新版本的適用于 JAVA 的 HDInsight SDK。 如果您使用受這些變更影響的方法，則可能需要進行少量的程式碼修改：

- `ConfigurationsInner.get` 將 **不會再傳回機密參數** ，例如 (核心) 的儲存體金鑰，或 (閘道) 的 HTTP 認證。
- `ConfigurationsInner.update` 現在已被取代。

### <a name="sdk-for-go"></a>適用于 Go 的 SDK

更新至 [27.1.0 版](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight) 或更新版本的 HDInsight SDK for Go。 如果您使用受這些變更影響的方法，則可能需要進行少量的程式碼修改：

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.Get) 將 **不會再傳回機密參數** ，例如 (核心) 的儲存體金鑰，或 (閘道) 的 HTTP 認證。
    - 若要抓取所有設定，包括敏感性參數，請 [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.List) 繼續使用。請注意，具有「讀者」角色的使用者將無法使用此方法。 這可讓您更精確地控制哪些使用者可以存取叢集的機密資訊。 
    - 若只取出 HTTP 閘道認證，請使用 [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ClustersClient.GetGatewaySettings) 。
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.Update) 現在已被取代，並已由取代 [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings) 。

### <a name="azhdinsight-powershell"></a>Az. HDInsight PowerShell
更新為 [Az PowerShell version 2.0.0](https://www.powershellgallery.com/packages/Az) 或更新版本，以避免中斷。  如果您使用受這些變更影響的方法，則可能需要修改程式碼。
- `Grant-AzHDInsightHttpServicesAccess` 現在已被取代，並已由新的 `Set-AzHDInsightGatewayCredential` Cmdlet 取代。
- `Get-AzHDInsightJobOutput` 已更新為可支援以角色為基礎的細微存取儲存體金鑰。
    - 具有 HDInsight 叢集操作員、參與者或擁有者角色的使用者將不受影響。
    - 只有讀者角色的使用者必須 `DefaultStorageAccountKey` 明確指定參數。
- `Revoke-AzHDInsightHttpServicesAccess` 現在已被取代。 現在一律會啟用 HTTP，因此不再需要此 Cmdlet。
 請參閱 [az。HDInsight 遷移指南](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight) 以取得詳細資料。

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>將 HDInsight 叢集操作員角色指派新增至使用者

具有 [擁有](../role-based-access-control/built-in-roles.md#owner) 者角色的使用者可以將 [hdinsight 叢集操作員](../role-based-access-control/built-in-roles.md#hdinsight-cluster-operator) 角色指派給您想要具有敏感性 HDInsight 叢集設定值之讀取/寫入存取權的使用者 (例如叢集閘道認證和儲存體帳戶金鑰) 。

### <a name="using-the-azure-cli"></a>使用 Azure CLI

新增此角色指派最簡單的方式，就是使用 `az role assignment create` Azure CLI 中的命令。

> [!NOTE]
> 此命令必須由具有擁有者角色的使用者執行，因為他們只能授與這些許可權。 `--assignee`是您要指派 HDInsight 叢集操作員角色之使用者的服務主體名稱或電子郵件地址。 如果您收到許可權不足的錯誤，請參閱下面的常見問題。

#### <a name="grant-role-at-the-resource-cluster-level"></a>在資源 (叢集) 層級授與角色

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>在資源群組層級授與角色

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>在訂用帳戶層級授與角色

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>使用 Azure 入口網站

您也可以使用 Azure 入口網站，將 HDInsight 叢集操作員角色指派新增至使用者。 請參閱檔、 [使用 Azure 入口網站新增角色指派來新增或移除 Azure 角色指派](../role-based-access-control/role-assignments-portal.md#add-a-role-assignment)。

## <a name="faq"></a>常見問題集

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>我在更新 API 要求和/或工具之後，為何會看到 403 (禁止) 回應？

叢集設定現在是在細微的角色型存取控制之下，需要 `Microsoft.HDInsight/clusters/configurations/*` 存取它們的許可權。 若要取得此許可權，請將 HDInsight 叢集操作員、參與者或擁有者角色指派給嘗試存取設定的使用者或服務主體。

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>當執行 Azure CLI 命令將 HDInsight 叢集操作員角色指派給其他使用者或服務主體時，為什麼會看到「沒有足夠的許可權可完成作業」？

除了擁有擁有者角色之外，執行命令的使用者或服務主體也必須有足夠的 Azure AD 許可權，才能查閱受託人的物件識別碼。 此訊息表示 Azure AD 許可權不足。 請嘗試 `-–assignee` 使用取代引數， `–assignee-object-id` 並在受控識別) 的情況下，提供受託人的物件識別碼做為參數，而不是名稱 (或主體識別碼。 如需詳細資訊，請參閱 [az 角色指派建立檔](/cli/azure/role/assignment#az-role-assignment-create) 的選擇性參數一節。

如果仍然無法運作，請洽詢您的 Azure AD 系統管理員，以取得正確的許可權。

### <a name="what-will-happen-if-i-take-no-action"></a>如果我沒有採取任何動作，會發生什麼事？

從2019年9月3日開始， `GET /configurations` `POST /configurations/gateway` 呼叫將不會再傳回任何資訊，而 `GET /configurations/{configurationName}` 呼叫將不會再傳回敏感性參數，例如儲存體帳戶金鑰或叢集密碼。 對應的 SDK 方法和 PowerShell Cmdlet 也是如此。

如果您使用上述 Visual Studio、VSCode、IntelliJ 或 Eclipse 其中一種工具的舊版，則在您更新之前將無法再運作。

如需詳細資訊，請參閱本檔的對應章節以瞭解您的案例。