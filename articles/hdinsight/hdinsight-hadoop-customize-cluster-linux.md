---
title: 使用腳本操作自訂 Azure HDInsight 群集
description: 使用腳本操作將自訂群組件添加到 HDInsight 群集。 指令碼動作是 Bash 指令碼，可用來自訂叢集設定，或新增其他服務和公用程式，例如 Hue、Solr 或 R。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: 12e6892930afe8ba9c7bad9b05fd39eeaf8835fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272496"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>使用腳本操作自訂 Azure HDInsight 群集

Azure HDInsight 提供了一種稱為**腳本操作**的配置方法，用於調用自訂腳本來自訂群集。 這些指令碼可用來安裝其他元件和變更組態設定。 叢集建立期間或叢集建立之後，可以使用指令碼動作。

指令碼動作也可以發佈到 Azure Marketplace 做為 HDInsight 應用程式。 如需有關 HDInsight 應用程式的詳細資訊，請參閱[將 HDInsight 應用程式發佈到 Azure Marketplace](hdinsight-apps-publish-applications.md)。

## <a name="permissions"></a>權限

針對已加入網域的 HDInsight 叢集，當您對叢集使用指令碼動作時，必須有兩個 Apache Ambari 權限︰

* **安巴里運行\_自訂\_命令**。 依預設，Ambari 系統管理員角色會具有此權限。
* **群集。運行\_自訂\_命令**。 依預設，HDInsight 叢集系統管理員和 Ambari 系統管理員會具有此權限。

如需有關使用已加入網域之 HDInsight 的權限詳細資訊，請參閱[使用企業安全性套件管理 HDInsight 叢集](./domain-joined/apache-domain-joined-manage.md)。

## <a name="access-control"></a>存取控制

如果您不是 Azure 訂用帳戶的系統管理員或擁有者，您的帳戶必須至少具備包含 HDInsight 叢集之資源群組的「參與者」存取權。

如果您建立 HDInsight 叢集，則至少具備 Azure 訂用帳戶之「參與者」存取權的人必須先前已註冊 HDInsight 的提供者。 具有訂用帳戶之參與者存取權的使用者第一次在訂用帳戶上建立資源時，便會註冊提供者。 如果您[透過使用 REST 來註冊提供者](https://msdn.microsoft.com/library/azure/dn790548.aspx)，則無須建立資源也可以完成此作業。

取得有關使用存取管理的詳細資訊：

* [開始在 Azure 入口網站中使用存取管理](../role-based-access-control/overview.md)
* [使用角色指派來管理 Azure 訂用帳戶資源的存取權](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>了解指令碼動作

指令碼動作是在 HDInsight 叢集中節點上執行的 Bash 指令碼。 指令碼動作的特性和功能如下：

* 必須儲存在可從 HDInsight 叢集存取的 URI 上。 以下是可能的儲存位置：

    * 對於常規群集：

      * ADLS Gen1：用於訪問資料存儲庫的服務主體 HDInsight 必須具有對腳本的讀取存取許可權。 Data Lake Storage Gen1 中所儲存指令碼的 URI 格式為 `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`。

      * 本身是 HDInsight 叢集主要或額外儲存體帳戶之「Azure 儲存體」帳戶中的 Blob。 在建立叢集期間，已將這兩種儲存體帳戶的存取權都授與 HDInsight。

        > [!IMPORTANT]  
        > 不要在此 Azure 存儲帳戶上旋轉存儲金鑰，因為這將導致存儲腳本的後續腳本操作失敗。

      * 可通過HTTP://路徑訪問的公共檔共用服務。 示例包括 Azure Blob、GitHub、OneDrive。 如需範例 URI，請參閱[範例指令碼動作指令碼](#example-script-action-scripts)。

     * 對於具有 ESP 的群集，支援wasb://或wasbs://或 HTTP_s_s_：// URI。

* 可限制為只在特定節點類型上執行。 例如前端節點或背景工作節點。

* 可以是持續性或臨時性。

    持續性指令碼動作必須有唯一的名稱。 持續性指令碼可用來自訂透過調整規模作業新增至叢集的新背景工作節點。 持續性指令碼也可以在進行調整規模作業時，將變更套用至另一個節點類型。 例如前端節點。

    臨時性指令碼不會持續留存。 建立叢集期間使用的指令碼動作會自動保存下來。 它們不會套用至在指令碼執行後新增至叢集的背景工作節點。 您可以在之後將臨時性指令碼升階為持續性指令碼，或將持續性指令碼降階為臨時性指令碼。 失敗的指令碼即使您特別指示應保存，也不會保存下來。

* 可以接受指令碼在執行期間所使用的參數。

* 在叢集節點上以根層級權限執行。

* 可通過 Azure 門戶、Azure PowerShell、Azure CLI 或 HDInsight .NET SDK 使用。

叢集會保留所有已執行指令碼的歷程記錄。 當您需要尋找指令碼的識別碼以進行升階或降階作業時，歷程記錄會很有幫助。

> [!IMPORTANT]  
> 沒有任何自動方式可復原指令碼動作所做的變更。 請手動回復變更，或提供可回復變更的指令碼。

### <a name="script-action-in-the-cluster-creation-process"></a>叢集建立程序中的指令碼動作

在叢集建立期間使用的指令碼動作與在現有叢集上執行的指令碼動作稍微不同︰

* 此指令碼會自動保存。

* 指令碼若發生失敗，可能會導致叢集建立程序失敗。

下圖說明在建立程序期間何時會執行指令碼動作：

![HDInsight 叢集自訂和叢集建立期間的階段][img-hdi-cluster-states]

設定 HDInsight 時會執行此指令碼。 指令碼會以平行方式在叢集中所有指定的節點上執行。 它會在節點上以根權限執行。

您可以執行停止和啟動服務 (包括 Apache Hadoop 相關服務) 等作業。 如果您停止服務，請確定 Ambari 服務及其他 Hadoop 相關服務在指令碼完成前處於執行狀態。 在建立叢集時，必須要有這些服務，才能成功地判斷叢集的健康情況和狀態。

在叢集建立期間，您可以同時使用許多指令碼動作。 這些指令碼會以指定的順序叫用。

> [!IMPORTANT]  
> 腳本操作必須在 60 分鐘內完成，否則它們超時。在群集預配期間，腳本與其他設置和配置過程同時運行。 與在您開發環境中的執行時間相較，爭用 CPU 時間和網路頻寬等資源可能會導致指令碼需要較長的時間才能完成。
>
> 若要讓執行指令碼所花費的時間縮到最短，請避免進行從來源下載和編譯應用程式之類的工作。 請預先編譯應用程式，並將二進位檔儲存在「Azure 儲存體」中。

### <a name="script-action-on-a-running-cluster"></a>執行中叢集上的指令碼動作

在已處於執行中狀態的叢集上執行的指令碼如果發生失敗，並不會自動導致叢集變成失敗狀態。 在指令碼完成後，叢集應該會回到執行中狀態。 即使叢集狀態為執行中，失敗的指令碼仍可能已造成問題。 例如，指令碼可能會刪除叢集所需的檔案。

指令碼動作會以根權限執行。 在將腳本應用於群集之前，請確保瞭解腳本的作用。

當您將指令碼套用至叢集時，叢集狀態會從 [正在執行]**** 變更為 [已接受]****。 然後，它會變更為 [HDInsight 設定]****，最後，如果指令碼成功，就會再變更回 [正在執行]****。 指令碼狀態會記錄在指令碼動作歷程記錄中。 此資訊會告訴您指令碼成功還是失敗。 例如，`Get-AzHDInsightScriptActionHistory` PowerShell Cmdlet 會顯示指令碼的狀態。 它會傳回類似以下文字的資訊：

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

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

## <a name="script-action-during-cluster-creation"></a>群集創建期間的腳本操作

本節說明建立 HDInsight 叢集時，您可以使用指令碼動作的不同方式。

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>在建立叢集期間從 Azure 入口網站使用指令碼動作

1. 使用 Azure 門戶，開始創建群集，如在[HDInsight 中創建基於 Linux 的群集](hdinsight-hadoop-create-linux-clusters-portal.md)中所述。 從 **"配置 + 定價**"選項卡中，選擇 **"添加腳本操作**"。

    ![Azure 門戶群集腳本操作](./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-configuration-scriptaction.png)

1. 使用 [選取指令碼]____ 項目來選取預先製作的指令碼。 若要使用自訂指令碼，請選取 [自訂]____。 然後為您的指令碼提供 [名稱]____ 和 [Bash 指令碼 URI]____。

    ![在選取指令碼表單中加入指令碼](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    下表說明表單上的元素：

    | 屬性 | 值 |
    | --- | --- |
    | 選取指令碼 | 若要使用自己的指令碼，請選取 [自訂]____。 或是選取其中一個提供的指令碼。 |
    | 名稱 |指定指令碼動作的名稱。 |
    | Bash 指令碼 URI |指定指令碼的 URI。 |
    | 頭/工人/動物園管理員 |指定運行腳本的節點：**頭**、**輔助角色**或**ZooKeeper**。 |
    | 參數 |如果指令碼要求，請指定參數。 |

    請使用 [保存此指令碼動作]____ 項目，以確保在執行規模調整作業期間會套用此指令碼。

1. 選取 [Create] \(建立\)____ 以儲存指令碼。 接著，您可以使用 [+ 送出新的]____ 來新增另一個指令碼。

    ![HDInsight 多個腳本操作](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png)

    添加完腳本後，返回到 **"配置 + 定價**"選項卡。

1. 像往常一樣完成剩餘的群集創建步驟。

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

* [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

* [使用 Resource Manager 範本與 Azure CLI 部署資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>在建立叢集期間從 Azure PowerShell 使用指令碼動作

在本節中，您可以使用[Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) Cmdlet 調用腳本來自訂群集。 在您開始之前，請務必先安裝和設定 Azure PowerShell。 要使用這些 PowerShell 命令，您需要[AZ 模組](https://docs.microsoft.com/powershell/azure/overview)。

下列指令碼示範如何使用 PowerShell 在建立叢集時套用指令碼動作：

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

建立叢集可能需要幾分鐘的時間。

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>在建立叢集期間從 HDInsight .NET SDK 使用指令碼動作

HDInsight .NET SDK 提供用戶端程式庫，可讓您更輕鬆地從 .NET 應用程式使用 HDInsight。 有關代碼示例，請參閱[腳本操作](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet#script-actions)。

## <a name="script-action-to-a-running-cluster"></a>腳本操作到正在運行的群集

本節說明如何將指令碼動作套用至執行中的叢集。

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>從 Azure 入口網站將指令碼動作套用到執行中的叢集

1. 登錄到 Azure[門戶](https://portal.azure.com)並找到群集。

1. 從預設檢視的 [設定]**** 底下，選取 [指令碼動作]****。

1. 從 [指令碼動作]**** 頁面上方，選取 [+ 送出新的]****。

    ![將指令碼加入執行中的叢集](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

1. 使用 [選取指令碼]____ 項目來選取預先製作的指令碼。 若要使用自訂指令碼，請選取 [自訂]____。 然後為您的指令碼提供 [名稱]____ 和 [Bash 指令碼 URI]____。

    ![在選取指令碼表單中加入指令碼](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    下表說明表單上的元素：

    | 屬性 | 值 |
    | --- | --- |
    | 選取指令碼 | 要使用自己的腳本，請選擇__自訂__。 否則，請選取提供的指令碼。 |
    | 名稱 |指定指令碼動作的名稱。 |
    | Bash 指令碼 URI |指定指令碼的 URI。 |
    | Head/Worker/Zookeeper |指定運行腳本的節點：**頭**、**輔助角色**或**ZooKeeper**。 |
    | 參數 |如果指令碼要求，請指定參數。 |

    使用 [保存此指令碼動作]____ 項目，可確保在執行規模調整作業時套用此指令碼。

1. 最後，選取 [建立]**** 按鈕以將指令碼套用至叢集。

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>從 Azure PowerShell 將指令碼動作套用到執行中的叢集

要使用這些 PowerShell 命令，您需要[AZ 模組](https://docs.microsoft.com/powershell/azure/overview)。 下列範例示範如何將指令碼動作套用至執行中的叢集：

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

在作業完成之後，您會收到類似以下文字的訊息：

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>從 Azure CLI 將指令碼動作套用到執行中的叢集

在您開始之前，請務必先安裝和設定 Azure CLI。 確保您擁有最新版本。 如需詳細資訊，請參閱 [安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

1. 向您的 Azure 訂用帳戶進行驗證：

    ```azurecli
    az login
    ```

1. 將指令碼動作套用至執行中的叢集：

    ```azurecli
    az hdinsight script-action execute --cluster-name CLUSTERNAME --name SCRIPTNAME --resource-group RESOURCEGROUP --roles ROLES
    ```

    有效角色為`headnode` `workernode` `zookeepernode`，， `edgenode`。 如果腳本應應用於多個節點類型，則按空格分隔角色。 例如： `--roles headnode workernode` 。

    若要保存指令碼，請新增 `--persist-on-success`。 您之後也可以使用 `az hdinsight script-action promote` 來保存指令碼。

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>使用 REST API 將指令碼動作套用至執行中的叢集

請參閱 [Azure HDInsight 中的叢集 REST API](https://msdn.microsoft.com/library/azure/mt668441.aspx) \(英文\)。

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>從 HDInsight .NET SDK 將指令碼動作套用到執行中的叢集

如需使用 .NET SDK 將指令碼套用至叢集的範例，請參閱[針對執行中的 Linux 型 HDInsight 叢集套用指令碼動作](https://github.com/Azure-Samples/hdinsight-dotnet-script-action) \(英文\)。

## <a name="view-history-and-promote-and-demote-script-actions"></a>檢視歷程記錄及將指令碼動作升階和降階

### <a name="the-azure-portal"></a>Azure 入口網站

1. 登錄到 Azure[門戶](https://portal.azure.com)並找到群集。

1. 從預設檢視的 [設定]**** 底下，選取 [指令碼動作]****。

1. 此叢集的指令碼歷程記錄會顯示在 [指令碼動作] 區段上。 此資訊包含持續性指令碼清單。 以下螢幕擷取畫面顯示 Solr 指令碼已在此叢集上執行。 此螢幕擷取畫面未顯示任何持續性指令碼。

    ![門戶腳本操作提交歷史記錄](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

1. 從歷程記錄中選取指令碼，以顯示此指令碼的 [屬性]**** 區段。 從視窗的頂端，您可以重新執行指令碼或將其升階。

    ![腳本操作屬性升級](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

1. 您也可以選取 [指令碼動作] 區段上項目右邊的省略符號 (**...**) 來執行動作。

    ![已保留的腳本操作刪除](./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png)

### <a name="azure-powershell"></a>Azure PowerShell

| Cmdlet | 函式 |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |擷取持續性指令碼動作的相關資訊。 此 Cmdlet 不會撤銷腳本執行的操作，它只會刪除持久化標誌。|
| `Get-AzHDInsightScriptActionHistory` |擷取已套用到叢集的指令碼動作歷程記錄，或特定指令碼的詳細資料。 |
| `Set-AzHDInsightPersistedScriptAction` |將臨時性指令碼動作升階為持續性指令碼動作。 |
| `Remove-AzHDInsightPersistedScriptAction` |將持續性指令碼動作降階為臨時性動作。 |

下列範例指令碼示範如何使用 Cmdlet 將指令碼先升級後再降級。

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="azure-cli"></a>Azure CLI

| Command | 描述 |
| --- | --- |
| [az hdinsight 腳本操作刪除](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-delete) |刪除群集的指定持久化腳本操作。 此命令不會撤銷腳本執行的操作，它只會刪除持久化的標誌。|
|[az hdinsight script-action execute](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute)|在指定的 HDInsight 叢集上執行指令碼動作。|
| [az hdinsight 腳本動作清單](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list) |列出指定群集的所有持久化腳本操作。 |
|[az hdinsight 腳本動作清單-執行歷史記錄](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list-execution-history)|列出指定群集的所有腳本執行歷史記錄。|
|[az hdinsight 腳本操作升級](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-promote)|將指定的臨時腳本執行提升為持久腳本。|
|[az hdinsight 腳本操作顯示執行詳細資訊](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-show-execution-details)|獲取給定腳本執行 ID 的腳本執行詳細資訊。|

### <a name="hdinsight-net-sdk"></a>HDInsight .NET SDK

如需使用 .NET SDK 從叢集擷取指令碼歷程記錄、將指令碼升階或降階的範例，請參閱[針對執行中的 Linux 型 HDInsight 叢集套用指令碼動作](https://github.com/Azure-Samples/hdinsight-dotnet-script-action) \(英文\)。

> [!NOTE]  
> 這個範例也示範如何使用 .NET SDK 來安裝 HDInsight 應用程式。

## <a name="support-for-open-source-software"></a>支援開源軟體

Microsoft Azure HDInsight 服務會使用以 Apache Hadoop 為中心的開放原始碼技術生態系統。 Microsoft Azure 可為開放原始碼技術提供一般層級的支援。 如需詳細資訊，請參閱 [Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)的**支援範圍**一節。 HDInsight 服務針對內建元件提供額外等級的支援。

HDInsight 服務中有兩種類型的開放原始碼元件可供使用：

* **內建元件**。 這些元件會預先安裝在 HDInsight 叢集上，並且提供叢集的核心功能。 以下是屬於此類別的元件：

  * [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) ResourceManager。
  * Hive 查詢語言 [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)。
  * [阿帕奇·馬胡](https://mahout.apache.org/)

    如需完整的叢集元件清單，請參閱[可以搭配 HDInsight 使用的 Apache Hadoop 元件和版本有哪些？](hdinsight-component-versioning.md)

* **自訂元件**。 身為叢集的使用者，您可以安裝或在工作負載中使用社群中可用或您建立的任何元件。

> [!WARNING]  
> 對隨 HDInsight 叢集提供的元件會有完整支援。 Microsoft 支援服務可協助隔離和解決這些元件的相關問題。
>
> 自訂元件則會獲得商務上合理的支援，以協助您進一步針對問題進行疑難排解。 「Microsoft 支援服務」可能能夠解決問題。 或是可能要求您參與可提供該技術深度專業知識的可用開放原始碼技術管道。 您可以使用許多社群網站。 例如 [HDInsight 的 MSDN 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)和 [Stack Overflow](https://stackoverflow.com)。
>
> 此外，Apache 專案在 [Apache 網站](https://apache.org)上也有專案網站。 例如 [Hadoop](https://hadoop.apache.org/)。

HDInsight 服務提供數種方式以使用自訂元件。 不論元件在叢集上的使用或安裝方式為何，都適用相同層級的支援。 以下清單說明自訂元件在 HDInsight 叢集上的最常見使用方式：

1. **工作提交**。 可將執行或使用自訂元件的 Hadoop 或其他類型的作業提交給叢集。

2. **叢集自訂**。 在叢集建立期間，您可以指定額外設定及安裝在叢集節點上的自訂元件。

3. **樣品**. 針對熱門的自訂元件，Microsoft 和其他提供者可能會提供如何在 HDInsight 叢集上使用這些元件的範例。 提供這些範例，但是沒有支援。

## <a name="troubleshooting"></a>疑難排解

您可以使用 Ambari Web UI 來檢視指令碼動作所記錄的資訊。 如果指令碼在叢集建立期間失敗，則與該叢集相關聯的預設儲存體帳戶中也會有記錄。 本節提供有關如何使用這兩個選項來擷取記錄的資訊。

### <a name="the-apache-ambari-web-ui"></a>Apache Ambari Web UI

1. 從 Web 瀏覽器導航到`https://CLUSTERNAME.azurehdinsight.net`，`CLUSTERNAME`群集的名稱在哪裡。

1. 在頁面頂端的列中，選取 **ops** 項目。 系統會顯示一個清單，當中包含透過 Ambari 在叢集上執行的目前和先前作業。

    ![Ambari Web UI 列與選取的 ops](./media/hdinsight-hadoop-customize-cluster-linux/hdi-apache-ambari-nav.png)

1. 尋找在 [作業]**** 欄位中有 **run\_customscriptaction** 的項目。 這些項目是在執行指令碼動作時建立的。

    ![阿帕奇·安巴里腳本操作操作](./media/hdinsight-hadoop-customize-cluster-linux/ambari-script-action.png)

    若要檢視 **STDOUT** 和 **STDERR** 輸出，請選取 **run\customscriptaction** 項目，然後向下鑽研連結。 這是指令碼執行時所產生的輸出，其中可能包含實用的資訊。

### <a name="access-logs-from-the-default-storage-account"></a>從預設的儲存體帳戶存取記錄

如果叢集建立因指令碼錯誤而失敗，記錄會保存在叢集儲存體帳戶中。

* 儲存體記錄位於 `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`。

    ![腳本動作記錄](./media/hdinsight-hadoop-customize-cluster-linux/script-action-logs-in-storage.png)

    在此目錄底下，記錄會個別針對**前端節點**、**背景工作節點**及 **Zookeeper 節點**進行組織。 請參閱下列範例：

    * **頭節點**：`<ACTIVE-HEADNODE-NAME>.cloudapp.net`

    * **輔助節點**：`<ACTIVE-WORKERNODE-NAME>.cloudapp.net`

    * **動物園管理員節點**：`<ACTIVE-ZOOKEEPERNODE-NAME>.cloudapp.net`

* 相對應主機的所有 **stdout** 和 **stderr** 都會上傳到儲存體帳戶。 每個指令碼動作都有一個 **output-\*.txt** 和 **errors-\*.txt**。 **output-*.txt** 檔案包含在主機上執行之指令碼的 URI 相關資訊。 以下文字是此資訊的範例：

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* 您有可能重複建立具有相同名稱的指令碼動作叢集。 在該情況下，您可以根據 **DATE** 資料夾名稱來區分相關的記錄。 例如，在不同日期建立之叢集 (**mycluster**) 的資料夾結構會類似下列記錄項目：

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* 如果您在同一天建立具有相同名稱的指令碼動作叢集，您可以使用唯一的前置詞來識別相關的記錄檔。

* 如果您在接近上午 12:00 (午夜) 時建立叢集，則記錄檔可能會橫跨兩天。 在該情況下，您會看到同一個叢集有兩個不同日期的資料夾。

* 將記錄檔上傳到預設容器最多可能需要 5 分鐘的時間，特別是針對大型叢集。 因此，如果您想要存取記錄，就不應在指令碼動作失敗時立即刪除叢集。

### <a name="ambari-watchdog"></a>Ambari 看門狗

> [!WARNING]  
> 請勿變更 Linux 型 HDInsight 叢集上的 Ambari 看門狗 (hdinsightwatchdog) 密碼。 變更此帳戶的密碼會破壞在 HDInsight 叢集上執行新指令碼動作的能力。

### <a name="cant-import-name-blobservice"></a>無法匯入名稱 BlobService

__症狀__。 指令碼動作失敗。 在 Ambari 中檢視作業時，會顯示類似下列錯誤的文字：

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__原因__。 如果您升級 HDInsight 叢集隨附的「Python Azure 儲存體」用戶端，就會發生此錯誤。 HDInsight 需要 Azure 儲存體用戶端 0.20.0。

__解決方案__。 若要解決此錯誤，請使用 `ssh`來手動連線至每個叢集節點。 請執行下列命令來重新安裝正確的儲存體用戶端版本：

```bash
sudo pip install azure-storage==0.20.0
```

如需有關使用 SSH 來連線到叢集的資訊，請參閱[使用 SSH 連線到 HDInsight (Apache Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md)。

### <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>歷程記錄不會顯示在叢集建立期間使用的指令碼

如果您的叢集是在 2016 年 3 月 15 日之前建立的，您在指令碼動作歷程記錄中就可能不會看到項目。 調整叢集大小會導致指令碼顯示在指令碼動作歷程記錄中。

有兩種例外狀況：

* 您的叢集是在 2015 年 9 月 1 日之前建立的。 此日期是引進指令碼動作的日期。 任何叢集只要是在此日期之前建立的，都不可能是使用指令碼動作來建立叢集。

* 您在叢集建立期間使用了多個指令碼動作。 或是您將相同名稱用於多個指令碼，或將相同名稱、相同 URI 但不同參數用於多個指令碼。 在這些情況下，您會得到下列錯誤：

    由於現有指令碼的指令碼名稱發生衝突，因此無法在此叢集上執行任何新的指令碼動作。 在叢集建立時所提供的指令碼名稱全都必須是唯一的名稱。 現有指令碼會在調整大小時執行。

## <a name="next-steps"></a>後續步驟

* [為 HDInsight 開發腳本操作腳本](hdinsight-hadoop-script-actions-linux.md)
* [在 HDInsight 叢集新增儲存體](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png "叢集建立期間的階段"
