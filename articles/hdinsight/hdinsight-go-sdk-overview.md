---
title: Azure HDInsight SDK for Go
description: 用於使用 Azure HDInsight SDK 的 Go 和 Apache Hadoop 群集的參考資料
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18
ms.date: 01/03/2020
ms.openlocfilehash: 292496c4d458621213fe62105149ac845d78891e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479581"
---
# <a name="hdinsight-sdk-for-go-preview"></a>用於 Go 的 HDInsight SDK（預覽版）

## <a name="overview"></a>總覽
適用于 Go 的 HDInsight SDK 提供類和功能，允許您管理 HDInsight 群集。 它包含用來建立、刪除、更新、列出、調整大小、執行指令碼動作、監視、取得 HDInsight 叢集屬性的作業，和其他多種作業。

> [!NOTE]  
>此 SDK 的 GoDoc 參考資料也可以在[這裡取得](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight)。

如果沒有 Azure 訂閱，請先創建[一個免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。"

## <a name="prerequisites"></a>Prerequisites

* [ `go get`工具](https://github.com/golang/go/wiki/GoGetTools)。
* [去](https://golang.org/dl/)。

## <a name="sdk-installation"></a>SDK 安裝

從您的 GOPATH 位置，執行 `go get github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight`

## <a name="authentication"></a>驗證

SDK 必須先使用您的 Azure 訂用帳戶進行驗證。  請依照下列範例建立服務主體，並使用它來驗證。 完成此操作後，您將具有 一個`ClustersClient`實例，其中包含許多可用於執行管理操作的函數（概述如下部分）。

> [!NOTE]  
> 除了下列範例以外，還有其他方式可進行驗證，可能更符合您的需求。 此處概述所有函式：[Azure SDK for Go 中的驗證函式](https://docs.microsoft.com/azure/go/azure-sdk-go-authorization)

### <a name="authentication-example-using-a-service-principal"></a>使用服務主體的驗證範例

首先，請登入 [Azure Cloud Shell](https://shell.azure.com/bash)。 驗證您當前使用的訂閱，您希望在其中創建服務主體。

```azurecli-interactive
az account show
```

您的訂用帳戶資訊會以 JSON 的形式顯示。

```json
{
  "environmentName": "AzureCloud",
  "id": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "isDefault": true,
  "name": "XXXXXXX",
  "state": "Enabled",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "user": {
    "cloudShellID": true,
    "name": "XXX@XXX.XXX",
    "type": "user"
  }
}
```

如果您未登入正確的訂用帳戶，請執行下列命令以選取正確的訂用帳戶： 
```azurecli-interactive
az account set -s <name or ID of subscription>
```

> [!IMPORTANT]  
> 如果尚未通過其他函數註冊 HDInsight 資來源提供者（例如通過 Azure 門戶創建 HDInsight 群集），則需要執行此操作一次，然後才能進行身份驗證。 此動作也可以從 [Azure Cloud Shell](https://shell.azure.com/bash) 完成，只要執行下列命令即可：
>```azurecli-interactive
>az provider register --namespace Microsoft.HDInsight
>```

接下來，請選擇服務主體的名稱，並使用下列命令加以建立：

```azurecli-interactive
az ad sp create-for-rbac --name <Service Principal Name> --sdk-auth
```

服務主體資訊會顯示為 JSON。

```json
{
  "clientId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "clientSecret": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "subscriptionId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

複製下列程式碼片段，並且在 `TENANT_ID`、`CLIENT_ID`、`CLIENT_SECRET` 和 `SUBSCRIPTION_ID` 中填入在執行建立服務主體的命令後傳回的 JSON 中所包含的字串。

```golang
package main

import (
    "context"
    "github.com/Azure/go-autorest/autorest/azure/auth"
    hdi "github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight"
    "github.com/Azure/go-autorest/autorest/to"
)

func main() {
    var err error

    // Tenant ID for your Azure Subscription
    var TENANT_ID = ""
    // Your Service Principal App Client ID
    var CLIENT_ID = ""
    // Your Service Principal Client Secret
    var CLIENT_SECRET = ""
    // Azure Subscription ID
    var SUBSCRIPTION_ID = ""

    var credentials = auth.NewClientCredentialsConfig(CLIENT_ID, CLIENT_SECRET, TENANT_ID)
    var client = hdi.NewClustersClient(SUBSCRIPTION_ID)

    client.Authorizer, err = credentials.Authorizer()
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
```

## <a name="cluster-management"></a>叢集管理

> [!NOTE]  
> 本節假設您已通過驗證並建構 `ClusterClient` 執行個體，將其儲存在稱為 `client` 的變數中。 驗證及取得 `ClusterClient` 的指示可在先前＜驗證＞一節中找到。

### <a name="create-a-cluster"></a>建立叢集

新叢集可藉由呼叫 `client.Create()` 來建立。 

#### <a name="example"></a>範例

此示例演示如何創建具有兩個頭節點和一個輔助節點的[Apache Spark](https://spark.apache.org/)群集。

> [!NOTE]  
> 您必須先建立資源群組和儲存體帳戶，說明如下。 如果您已建立這些項目，則可以略過這些步驟。

##### <a name="creating-a-resource-group"></a>建立資源群組

您可以使用 [Azure Cloud Shell](https://shell.azure.com/bash) 建立資源群組，只要執行下列命令即可

```azurecli-interactive
az group create -l <Region Name (i.e. eastus)> --n <Resource Group Name>
```

##### <a name="creating-a-storage-account"></a>建立儲存體帳戶

您可以使用 [Azure Cloud Shell](https://shell.azure.com/bash) 建立儲存體帳戶，只要執行下列命令即可：

```azurecli-interactive
az storage account create -n <Storage Account Name> -g <Existing Resource Group Name> -l <Region Name (i.e. eastus)> --sku <SKU i.e. Standard_LRS>
```

現在運行以下命令來獲取存儲帳戶的金鑰（您需要這樣做才能創建群集）：

```azurecli-interactive
az storage account keys list -n <Storage Account Name>
```

---
下面的 Go 程式碼片段創建一個 Spark 群集，該群集包含兩個頭節點和一個輔助節點。 請依照註解中的說明填入空白變數，並依據您的特定需求變更其他參數。

```golang
// The name for the cluster you are creating
var clusterName = "";
// The name of your existing Resource Group
var resourceGroupName = "";
// Choose a username
var username = "";
// Choose a password
var password = "";
// Replace <> with the name of your storage account
var storageAccount = "<>.blob.core.windows.net";
// Storage account key you obtained above
var storageAccountKey = "";
// Choose a region
var location = "";
var container = "default";

var parameters = hdi.ClusterCreateParametersExtended {
    Location: to.StringPtr(location),
    Tags: make(map[string]*string),
    Properties: &hdi.ClusterCreateProperties {
        ClusterVersion: to.StringPtr("3.6"),
        OsType: hdi.Linux,
        ClusterDefinition: &hdi.ClusterDefinition {
            Kind: to.StringPtr("spark"),
            Configurations: map[string]map[string]interface{}{
                "gateway": {
                    "restAuthCredential.isEnabled": "True",
                    "restAuthCredential.username":  username,
                    "restAuthCredential.password":  password,
                },
            },
        },
        Tier: hdi.Standard,
        ComputeProfile: &hdi.ComputeProfile {
            Roles: &[]hdi.Role {
                hdi.Role {
                    Name: to.StringPtr("headnode"),
                    TargetInstanceCount: to.Int32Ptr(2),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
                hdi.Role {
                    Name: to.StringPtr("workernode"),
                    TargetInstanceCount: to.Int32Ptr(1),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
            },
        },
        StorageProfile: &hdi.StorageProfile {
            Storageaccounts: &[]hdi.StorageAccount {
                hdi.StorageAccount {
                    Name: to.StringPtr(storageAccount),
                    Key: to.StringPtr(storageAccountKey),
                    Container: to.StringPtr(container),
                    IsDefault: to.BoolPtr(true),
                },
            },
        },
    },
}
client.Create(context.Background(), resourceGroupName, clusterName, parameters)
```

### <a name="get-cluster-details"></a>取得叢集詳細資料

若要取得特定叢集的屬性：

```golang
client.Get(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>範例

可以使用`get`來確認已成功創建群集。

```golang
cluster, err := client.Get(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
fmt.Println(*cluster.Name)
fmt.Println(*cluster.ID
```

輸出應會顯示如下：

```
<Cluster Name>
/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<Resource Group Name>/providers/Microsoft.HDInsight/clusters/<Cluster Name>
```

### <a name="list-clusters"></a>列出叢集

#### <a name="list-clusters-under-the-subscription"></a>列出訂用帳戶下的叢集

```golang
client.List()
```

#### <a name="list-clusters-by-resource-group"></a>依資源群組列出叢集

```golang
client.ListByResourceGroup("<Resource Group Name>")
```

> [!NOTE]  
> `List()` 和 `ListByResourceGroup()` 都會傳回 `ClusterListResultPage` 結構。 若要取得下一個頁面，您可以呼叫 `Next()`。 此動作可重複直到 `ClusterListResultPage.NotDone()` 傳回 `false`，如以下範例所示。

#### <a name="example"></a>範例

下列範例會列印目前訂用帳戶所有叢集的屬性：

```golang
page, err := client.List(context.Background())
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, cluster := range page.Values() {
        fmt.Println(*cluster.Name)
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

### <a name="delete-a-cluster"></a>刪除叢集

若要刪除叢集：

```golang
client.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

### <a name="update-cluster-tags"></a>更新叢集標籤

您可以更新指定叢集的標記，如下所示：

```golang
client.Update(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterPatchParameters{<map[string]*string} of Tags>)
```

#### <a name="example"></a>範例

```golang
client.Update(context.Background(), "SDKTestRG", "SDKTest", hdi.ClusterPatchParameters{map[string]*string{"tag1Name" : to.StringPtr("tag1Value"), "tag2Name" : to.StringPtr("tag2Value")}})
```

### <a name="resize-cluster"></a>調整叢集大小

您可以藉由指定新的大小來調整指定叢集的背景工作節點數目，如下所示：

```golang
client.Resize(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterResizeParameters{<Num of Worker Nodes (int)>})
```

## <a name="cluster-monitoring"></a>叢集監視

HDInsight 管理 SDK 也可用來透過 Operations Management Suite (OMS) 管理您對叢集的監視。

與您建立 `ClusterClient` 以用於管理作業的方式相似，針對監視作業您需要建立 `ExtensionClient`。 完成上述身份驗證部分後，可以創建`ExtensionClient`類似內容：

```golang
extClient := hdi.NewExtensionsClient(SUBSCRIPTION_ID)
extClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> 以下監視範例假設您已初始化稱為 `extClient` 的 `ExtensionClient`，並已如上述方式設定其 `Authorizer`。

### <a name="enable-oms-monitoring"></a>啟用 OMS 監視

> [!NOTE]  
> 若要啟用 OMS 監視，您必須擁有現有的 Log Analytics 工作區。 如果您尚未建立此工作區，您可以參考下列資料了解其建立方式：[在 Azure 入口網站中建立 Log Analytics 工作區](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace)。

若要對您的叢集啟用 OMS 監視：

```golang
extClient.EnableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name", hdi.ClusterMonitoringRequest {WorkspaceID: to.StringPtr("<Workspace Id>")})
```

### <a name="view-status-of-oms-monitoring"></a>檢視 OMS 監視的狀態

若要取得叢集的 OMS 狀態：

```golang
extClient.GetMonitoringStatus(context.Background(), "<Resource Group Name", "Cluster Name")
```

### <a name="disable-oms-monitoring"></a>停用 OMS 監視

若要對您的叢集停用 OMS：

```golang
extClient.DisableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name")
```

## <a name="script-actions"></a>指令碼動作

HDInsight 提供稱為指令碼動作的設定函式，此方法會叫用自訂指令碼來自訂叢集。

> [!NOTE]  
> 如需如何使用指令碼動作的詳細資訊，請參閱：[使用指令碼動作自訂以 Linux 為基礎的 HDInsight 叢集](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)

### <a name="execute-script-actions"></a>執行指令碼動作

您可以對指定的叢集執行指令碼動作，如下所示：

```golang
var scriptAction1 = hdi.RuntimeScriptAction{Name: to.StringPtr("<Script Name>"), URI: to.StringPtr("<URL To Script>"), Roles: <&[]string of roles>} //valid roles are "headnode", "workernode", "zookeepernode", and "edgenode"
client.ExecuteScriptActions(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ExecuteScriptActionParameters{PersistOnSuccess: to.BoolPtr(true), ScriptActions: &[]hdi.RuntimeScriptAction{scriptAction1}}) //add more RuntimeScriptActions to the list to execute multiple scripts
```

針對「刪除指令碼動作」和「列出保存的指令碼動作」作業，您需要建立 `ScriptActionsClient`，其方式與您建立 `ClusterClient` 以用於管理作業的方式相似。 完成上述身份驗證部分後，可以創建`ScriptActionsClient`類似內容：

```golang
scriptActionsClient := hdi.NewScriptActionsClient(SUBSCRIPTION_ID)
scriptActionsClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> 以下指令碼動作範例假設您已初始化稱為 `scriptActionsClient` 的 `ScriptActionsClient`，並已如上述方式設定其 `Authorizer`。

### <a name="delete-script-action"></a>刪除指令碼動作

若要刪除對給定叢集指定的持續性指令碼動作：

```golang
scriptActionsClient.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>", "<Script Name>")
```

### <a name="list-persisted-script-actions"></a>列出保存的指令碼動作

> [!NOTE]  
> 兩個 `ListByCluster()` 都會傳回 `ScriptActionsListPage` 結構。 若要取得下一個頁面，您可以呼叫 `Next()`。 此動作可重複直到 `ClusterListResultPage.NotDone()` 傳回 `false`，如以下範例所示。

若要列出指定叢集的所有持續性指令碼動作：
```golang
scriptActionsClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>範例

```golang
page, err := scriptActionsClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

### <a name="list-all-scripts-execution-history"></a>列出所有指令碼的執行記錄

針對此作業，您需要建立 `ScriptExecutionHistoryClient`，與您建立用於管理作業的 `ClusterClient` 方式相似。 完成上述身份驗證部分後，可以創建`ScriptActionsClient`類似內容：

```golang
scriptExecutionHistoryClient := hdi.NewScriptExecutionHistoryClient(SUBSCRIPTION_ID)
scriptExecutionHistoryClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> 以下假設您已初始化稱為 `scriptExecutionHistoryClient` 的 `ScriptExecutionHistoryClient`，並已如上述方式設定其 `Authorizer`。

若要針對指定的叢集列出所有指令碼的執行歷程記錄：

```golang
scriptExecutionHistoryClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>範例

此範例會列印過去所有指令碼執行的所有詳細資料。

```golang
page, err := scriptExecutionHistoryClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

## <a name="next-steps"></a>後續步驟

探索 [GoDoc reference material](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) (GoDoc 參考資料)。 GoDocs 提供 SDK 中所有函式的參考文件。
