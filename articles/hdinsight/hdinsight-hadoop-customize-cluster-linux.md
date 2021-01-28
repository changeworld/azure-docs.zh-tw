---
title: 使用指令碼動作來自訂 Azure HDInsight 叢集
description: 使用腳本動作，將自訂群組件新增至 HDInsight 叢集。 腳本動作是 Bash 腳本，可用於自訂叢集設定。 或新增其他服務和公用程式，例如色調、Solr 或 R。
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-azurecli, contperf-fy21q2
ms.date: 09/02/2020
ms.openlocfilehash: 46be3349490f04660d4fc8b69e4cdc295d8ecc1c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945793"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>使用指令碼動作來自訂 Azure HDInsight 叢集

Azure HDInsight 提供稱為 **腳本動作** 的設定方法，可叫用自訂腳本來自訂叢集。 這些指令碼可用來安裝其他元件和變更組態設定。 叢集建立期間或叢集建立之後，可以使用指令碼動作。

指令碼動作也可以發佈到 Azure Marketplace 做為 HDInsight 應用程式。 如需有關 HDInsight 應用程式的詳細資訊，請參閱[將 HDInsight 應用程式發佈到 Azure Marketplace](hdinsight-apps-publish-applications.md)。

## <a name="understand-script-actions"></a>了解指令碼動作

指令碼動作是在 HDInsight 叢集中節點上執行的 Bash 指令碼。 指令碼動作的特性和功能如下：

- 必須儲存在可從 HDInsight 叢集存取的 URI 上。 以下是可能的儲存位置：

    - 針對一般 (非 ESP) 叢集：
      - Data Lake Storage Gen1/Gen2： HDInsight 用來存取 Data Lake Storage 的服務主體必須具有腳本的讀取權限。 Data Lake Storage Gen1 中所儲存指令碼的 URI 格式為 `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`。 Data Lake Storage Gen2 腳本的 URI 格式為 `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`
      - 本身是 HDInsight 叢集主要或額外儲存體帳戶之「Azure 儲存體」帳戶中的 Blob。 在建立叢集期間，已將這兩種儲存體帳戶的存取權都授與 HDInsight。

        > [!IMPORTANT]  
        > 請勿輪替此 Azure 儲存體帳戶上的儲存體金鑰，因為這會導致後續腳本動作儲存在該處的腳本失敗。

      - 可透過路徑存取的公用檔案共用服務 `http://` 。 範例包括 Azure Blob、GitHub 或 OneDrive。 如需範例 URI，請參閱[範例指令碼動作指令碼](#example-script-action-scripts)。
    - 若為具有 ESP 的叢集，則 `wasb://` `wasbs://` 支援或或 `http[s]://` uri。

- 可限制為只在特定節點類型上執行。 例如前端節點或背景工作節點。
- 可以是持續性 *或特定的。*

    - 持續性指令碼動作必須有唯一的名稱。 持續性指令碼可用來自訂透過調整規模作業新增至叢集的新背景工作節點。 持續性指令碼也可以在進行調整規模作業時，將變更套用至另一個節點類型。 例如前端節點。
    - *特定* 腳本不會保存。 建立叢集期間使用的指令碼動作會自動保存下來。 它們不會套用至在指令碼執行後新增至叢集的背景工作節點。 然後，您 *可以將臨機操作腳本升* 階為持續性腳本，或將持續性腳本降級為 *特定腳本。* 失敗的指令碼即使您特別指示應保存，也不會保存下來。

- 可以接受指令碼在執行期間所使用的參數。
- 在叢集節點上以根層級權限執行。
- 可以透過 Azure 入口網站、Azure PowerShell、Azure CLI 或 HDInsight .NET SDK 來使用。
- 在 VM 上移除或修改服務檔案的腳本動作可能會影響服務健康狀態和可用性。

叢集會保留所有已執行指令碼的歷程記錄。 當您需要尋找指令碼的識別碼以進行升階或降階作業時，歷程記錄會很有幫助。

> [!IMPORTANT]  
> 沒有任何自動方式可復原指令碼動作所做的變更。 請手動回復變更，或提供可回復變更的指令碼。

## <a name="permissions"></a>權限

針對已加入網域的 HDInsight 叢集，當您對叢集使用指令碼動作時，必須有兩個 Apache Ambari 權限︰

* **AMBARI。執行 \_ 自訂 \_ 命令**。 依預設，Ambari 系統管理員角色會具有此權限。
* **群集。執行 \_ 自訂 \_ 命令**。 依預設，HDInsight 叢集系統管理員和 Ambari 系統管理員會具有此權限。

如需有關使用已加入網域之 HDInsight 的權限詳細資訊，請參閱[使用企業安全性套件管理 HDInsight 叢集](./domain-joined/apache-domain-joined-manage.md)。

## <a name="access-control"></a>存取控制

如果您不是 Azure 訂用帳戶的系統管理員或擁有者，則您的帳戶至少必須具備 `Contributor` 包含 HDInsight 叢集之資源群組的存取權。

至少具有 Azure 訂用帳戶參與者存取權的人員，必須先前已註冊該提供者。 當具有訂用帳戶參與者存取權的使用者建立資源時，就會發生提供者註冊。 若未建立資源，請參閱 [使用 REST 註冊提供者](/rest/api/resources/providers#Providers_Register)。

取得有關使用存取管理的詳細資訊：

- [開始在 Azure 入口網站中使用存取管理](../role-based-access-control/overview.md)
- [使用角色指派來管理 Azure 訂用帳戶資源的存取權](../role-based-access-control/role-assignments-portal.md)

## <a name="methods-for-using-script-actions"></a>使用腳本動作的方法

您可以選擇設定腳本動作，以在第一次建立叢集時執行，或在現有的叢集上執行腳本動作。

### <a name="script-action-in-the-cluster-creation-process"></a>叢集建立程序中的指令碼動作

在叢集建立期間使用的指令碼動作與在現有叢集上執行的指令碼動作稍微不同︰

- 此指令碼會自動保存。
- 指令碼若發生失敗，可能會導致叢集建立程序失敗。

下圖說明在建立程序期間何時會執行指令碼動作：

![HDInsight 叢集自訂和叢集建立期間的階段][img-hdi-cluster-states]

設定 HDInsight 時會執行此指令碼。 指令碼會以平行方式在叢集中所有指定的節點上執行。 它會在節點上以根權限執行。

您可以進行停止和啟動服務（包括 Apache Hadoop 相關服務）等作業。 如果您停止服務，請確定 Ambari 和其他 Hadoop 相關服務在腳本完成之前正在執行。 這些必要的服務會決定叢集在建立時的健康情況和狀態。

在叢集建立期間，您可以同時使用許多指令碼動作。 這些指令碼會以指定的順序叫用。

> [!IMPORTANT]  
> 腳本動作必須在60分鐘內完成，或已超時。在叢集布建期間，腳本會與其他安裝程式和設定進程同時執行。 與在您開發環境中的執行時間相較，爭用 CPU 時間和網路頻寬等資源可能會導致指令碼需要較長的時間才能完成。
>
> 若要讓執行指令碼所花費的時間縮到最短，請避免進行從來源下載和編譯應用程式之類的工作。 請預先編譯應用程式，並將二進位檔儲存在「Azure 儲存體」中。

### <a name="script-action-on-a-running-cluster"></a>執行中叢集上的指令碼動作

已在執行中叢集上的腳本失敗不會自動導致叢集變成失敗狀態。 在指令碼完成後，叢集應該會回到執行中狀態。 即使叢集狀態為執行中，失敗的指令碼仍可能已造成問題。 例如，指令碼可能會刪除叢集所需的檔案。

指令碼動作會以根權限執行。 請先確定您瞭解腳本的作用，再將它套用至您的叢集。

當您將指令碼套用至叢集時，叢集狀態會從 [正在執行] 變更為 [已接受]。 然後，它會變更為 [HDInsight 設定]，最後，如果指令碼成功，就會再變更回 [正在執行]。 指令碼狀態會記錄在指令碼動作歷程記錄中。 此資訊會告訴您指令碼成功還是失敗。 例如，`Get-AzHDInsightScriptActionHistory` PowerShell Cmdlet 會顯示指令碼的狀態。 它會傳回類似以下文字的資訊：

```output
ScriptExecutionId : 635918532516474303
StartTime         : 8/14/2017 7:40:55 PM
EndTime           : 8/14/2017 7:41:05 PM
Status            : Succeeded
```

> [!IMPORTANT]  
> 如果您在叢集建立後變更叢集使用者、系統管理員、密碼，則針對此叢集執行的指令碼動作可能會失敗。 如果您有任何以背景工作節點為目標的持續性指令碼動作，當您調整叢集的規模時，這些指令碼動作可能會失敗。

## <a name="example-script-action-scripts"></a>範例指令碼動作指令碼

指令碼動作的指令碼可以透過下列公用程式使用：

* Azure 入口網站
* Azure PowerShell
* Azure CLI
* HDInsight .NET SDK

HDInsight 提供一些指令碼以在 HDInsight 叢集上安裝下列元件：

| 名稱 | 指令碼 |
| --- | --- |
| 新增 Azure 儲存體帳戶 |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. 請參閱[將其他儲存體帳戶新增至 HDInsight](hdinsight-hadoop-add-storage.md)。 |
| 安裝 Hue |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. 請參閱[在 HDInsight Hadoop 叢集上安裝和使用 Hue](hdinsight-hadoop-hue-linux.md)。 |
| 預先載入 Hive 程式庫 |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. 請參閱[建立 HDInsight 叢集時新增自訂 Apache Hive 程式庫](hdinsight-hadoop-add-hive-libraries.md)。 |

## <a name="script-action-during-cluster-creation"></a>叢集建立期間的腳本動作

本節說明建立 HDInsight 叢集時，您可以使用指令碼動作的不同方式。

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>在建立叢集期間從 Azure 入口網站使用指令碼動作

1. 依照 [使用 Azure 入口網站在 HDInsight 中建立以 Linux 為基礎](hdinsight-hadoop-create-linux-clusters-portal.md)的叢集所述，開始建立叢集。 從 [設定 **+ 定價** ] 索引標籤中，選取 [ **+ 新增腳本動作**]。

    ![Azure 入口網站 cluster 腳本動作](./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-configuration-scriptaction.png)

1. 使用 [選取指令碼] 項目來選取預先製作的指令碼。 若要使用自訂指令碼，請選取 [自訂]。 然後為您的指令碼提供 [名稱] 和 [Bash 指令碼 URI]。

    ![在選取指令碼表單中加入指令碼](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    下表說明表單上的元素：

    | 屬性 | 值 |
    | --- | --- |
    | 選取指令碼 | 若要使用自己的指令碼，請選取 [自訂]。 或是選取其中一個提供的指令碼。 |
    | 名稱 |指定指令碼動作的名稱。 |
    | Bash 指令碼 URI |指定指令碼的 URI。 |
    | Head/Worker/ZooKeeper |指定執行腳本的節點： **Head**、 **Worker** 或 **ZooKeeper**。 |
    | 參數 |如果指令碼要求，請指定參數。 |

    請使用 [保存此指令碼動作] 項目，以確保在執行規模調整作業期間會套用此指令碼。

1. 選取 [Create] \(建立\) 以儲存指令碼。 接著，您可以使用 [+ 送出新的] 來新增另一個指令碼。

    ![HDInsight 多個腳本動作](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png)

    當您完成新增腳本時，您會回到 [設定 **+ 定價** ] 索引標籤。

1. 照常完成剩餘的叢集建立步驟。

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>從 Azure Resource Manager 範本使用指令碼動作

指令碼動作可搭配 Azure Resource Manager 範本使用。 例如，請參閱[建立 HDInsight Linux 叢集並執行指令碼動作](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/) \(英文\)。

在此範例中，會使用下列程式碼來新增指令碼動作：

```json
"scriptActions": [
    {
        "name": "setenvironmentvariable",
        "uri": "[parameters('scriptActionUri')]",
        "parameters": "headnode"
    }
]
```

取得有關如何部署範本的詳細資訊：

- [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](../azure-resource-manager/templates/deploy-powershell.md)
- [使用 Resource Manager 範本與 Azure CLI 部署資源](../azure-resource-manager/templates/deploy-cli.md)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>在建立叢集期間從 Azure PowerShell 使用指令碼動作

在本節中，您會使用 [AzHDInsightScriptAction](/powershell/module/az.hdinsight/add-azhdinsightscriptaction) 指令 Cmdlet 來叫用腳本，以自訂叢集。 在您開始之前，請務必先安裝和設定 Azure PowerShell。 若要使用這些 PowerShell 命令，您需要 [AZ 模組](/powershell/azure/)。

下列指令碼示範如何使用 PowerShell 在建立叢集時套用指令碼動作：

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

建立叢集可能需要幾分鐘的時間。

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>在建立叢集期間從 HDInsight .NET SDK 使用指令碼動作

HDInsight .NET SDK 提供用戶端程式庫，可讓您更輕鬆地從 .NET 應用程式使用 HDInsight。 如需程式碼範例，請參閱 [腳本動作](/dotnet/api/overview/azure/hdinsight?view=azure-dotnet&preserve-view=true#script-actions)。

## <a name="script-action-to-a-running-cluster"></a>對執行中叢集的腳本動作

本節說明如何將腳本動作套用至執行中的叢集。

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>從 Azure 入口網站將指令碼動作套用到執行中的叢集

1. 登入 [Azure 入口網站](https://portal.azure.com) 並找出您的叢集。

1. 從預設檢視的 [設定] 底下，選取 [指令碼動作]。

1. 從 [指令碼動作] 頁面上方，選取 [+ 送出新的]。

    ![將指令碼加入執行中的叢集](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

1. 使用 [選取指令碼] 項目來選取預先製作的指令碼。 若要使用自訂指令碼，請選取 [自訂]。 然後為您的指令碼提供 [名稱] 和 [Bash 指令碼 URI]。

    ![在選取指令碼表單中加入指令碼](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    下表說明表單上的元素：

    | 屬性 | 值 |
    | --- | --- |
    | 選取指令碼 | 若要使用您自己的腳本，請選取 [ __自訂__]。 否則，請選取提供的指令碼。 |
    | 名稱 |指定指令碼動作的名稱。 |
    | Bash 指令碼 URI |指定指令碼的 URI。 |
    | Head/Worker/Zookeeper |指定執行腳本的節點： **Head**、 **Worker** 或 **ZooKeeper**。 |
    | 參數 |如果指令碼要求，請指定參數。 |

    使用 [保存此指令碼動作] 項目，可確保在執行規模調整作業時套用此指令碼。

1. 最後，選取 [建立] 按鈕以將指令碼套用至叢集。

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>從 Azure PowerShell 將指令碼動作套用到執行中的叢集

若要使用這些 PowerShell 命令，您需要 [AZ 模組](/powershell/azure/)。 下列範例示範如何將指令碼動作套用至執行中的叢集：

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

在作業完成之後，您會收到類似以下文字的訊息：

```output
OperationState  : Succeeded
ErrorMessage    :
Name            : Giraph
Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
Parameters      :
NodeTypes       : {HeadNode, WorkerNode}
```

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>從 Azure CLI 將指令碼動作套用到執行中的叢集

在您開始之前，請務必先安裝和設定 Azure CLI。 確認您擁有最新版本。 如需詳細資訊，請參閱 [安裝 Azure CLI](/cli/azure/install-azure-cli)。

1. 向您的 Azure 訂用帳戶進行驗證：

    ```azurecli
    az login
    ```

1. 將指令碼動作套用至執行中的叢集：

    ```azurecli
    az hdinsight script-action execute --cluster-name CLUSTERNAME --name SCRIPTNAME --resource-group RESOURCEGROUP --roles ROLES
    ```

    有效的角色為 `headnode` 、 `workernode` 、 `zookeepernode` 、 `edgenode` 。 如果腳本應該套用至數個節點類型，請以空格分隔角色。 例如： `--roles headnode workernode` 。

    若要保存指令碼，請新增 `--persist-on-success`。 您之後也可以使用 `az hdinsight script-action promote` 來保存指令碼。

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>使用 REST API 將指令碼動作套用至執行中的叢集

請參閱 [Azure HDInsight 中的叢集 REST API](/rest/api/hdinsight/hdinsight-cluster) \(英文\)。

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>從 HDInsight .NET SDK 將指令碼動作套用到執行中的叢集

如需使用 .NET SDK 將指令碼套用至叢集的範例，請參閱[針對執行中的 Linux 型 HDInsight 叢集套用指令碼動作](https://github.com/Azure-Samples/hdinsight-dotnet-script-action) \(英文\)。

## <a name="view-history-and-promote-and-demote-script-actions"></a>檢視歷程記錄及將指令碼動作升階和降階

### <a name="the-azure-portal"></a>Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com) 並找出您的叢集。

1. 從預設檢視的 [設定] 底下，選取 [指令碼動作]。

1. 此叢集的指令碼歷程記錄會顯示在 [指令碼動作] 區段上。 此資訊包含持續性指令碼清單。 以下螢幕擷取畫面顯示 Solr 指令碼已在此叢集上執行。 此螢幕擷取畫面未顯示任何持續性指令碼。

    ![入口網站腳本動作提交歷程記錄](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

1. 從歷程記錄中選取指令碼，以顯示此指令碼的 [屬性] 區段。 從視窗的頂端，您可以重新執行指令碼或將其升階。

    ![腳本動作屬性升級](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

1. 您也可以選取 [腳本動作] 區段上專案右邊的省略號（ **...**）來執行動作。

    ![刪除保存的腳本動作](./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png)

### <a name="azure-powershell"></a>Azure PowerShell

| Cmdlet | 函式 |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |擷取持續性指令碼動作的相關資訊。 此 Cmdlet 不會復原腳本所執行的動作，只會移除保存的旗標。|
| `Get-AzHDInsightScriptActionHistory` |擷取已套用到叢集的指令碼動作歷程記錄，或特定指令碼的詳細資料。 |
| `Set-AzHDInsightPersistedScriptAction` |將 `ad hoc` 腳本動作升階為持續性腳本動作。 |
| `Remove-AzHDInsightPersistedScriptAction` |將持續性腳本動作降級為 `ad hoc` 動作。 |

下列範例指令碼示範如何使用 Cmdlet 將指令碼先升級後再降級。

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="azure-cli"></a>Azure CLI

| 命令 | 描述 |
| --- | --- |
| [`az hdinsight script-action delete`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-delete) |刪除叢集的指定持續性腳本動作。 此命令不會復原腳本所執行的動作，只會移除已保存的旗標。|
|[`az hdinsight script-action execute`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-execute)|在指定的 HDInsight 叢集上執行指令碼動作。|
| [`az hdinsight script-action list`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-list) |列出指定叢集的所有持續性腳本動作。 |
|[`az hdinsight script-action list-execution-history`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-list-execution-history)|列出指定叢集的所有腳本執行歷程記錄。|
|[`az hdinsight script-action promote`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-promote)|將指定的臨機操作腳本執行升級為持續性腳本。|
|[`az hdinsight script-action show-execution-details`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-show-execution-details)|取得指定腳本執行識別碼的腳本執行詳細資料。|

### <a name="hdinsight-net-sdk"></a>HDInsight .NET SDK

如需使用 .NET SDK 從叢集擷取指令碼歷程記錄、將指令碼升階或降階的範例，請參閱[針對執行中的 Linux 型 HDInsight 叢集套用指令碼動作](https://github.com/Azure-Samples/hdinsight-dotnet-script-action) \(英文\)。

> [!NOTE]  
> 這個範例也示範如何使用 .NET SDK 來安裝 HDInsight 應用程式。

## <a name="next-steps"></a>後續步驟

* [開發 HDInsight 的指令碼動作指令碼](hdinsight-hadoop-script-actions-linux.md)
* [在 HDInsight 叢集新增儲存體](hdinsight-hadoop-add-storage.md)
* [針對指令碼動作進行疑難排解](troubleshoot-script-action.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png "叢集建立期間的階段"
