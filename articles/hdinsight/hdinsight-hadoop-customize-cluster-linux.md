---
title: 使用文稿操作自訂 Azure HDInsight 叢集
description: 使用文稿操作將自定義元件添加到 HDInsight 叢集。 文稿操作是可用於自定義群集配置的 Bash 腳本。 或者添加其他服務和實用程式,如 Hue、Solr 或 R。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 434d4adb763fd0e0a29c065ce051bfd4fa461180
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770744"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>使用文稿操作自訂 Azure HDInsight 叢集

Azure HDInsight 提供了一種稱為**腳本操作**的配置方法,用於調用自定義腳本來自定義群集。 這些指令碼可用來安裝其他元件和變更組態設定。 叢集建立期間或叢集建立之後，可以使用指令碼動作。

指令碼動作也可以發佈到 Azure Marketplace 做為 HDInsight 應用程式。 如需有關 HDInsight 應用程式的詳細資訊，請參閱[將 HDInsight 應用程式發佈到 Azure Marketplace](hdinsight-apps-publish-applications.md)。

## <a name="permissions"></a>權限

針對已加入網域的 HDInsight 叢集，當您對叢集使用指令碼動作時，必須有兩個 Apache Ambari 權限︰

* **安巴裡執行\_自\_訂 指令**。 依預設，Ambari 系統管理員角色會具有此權限。
* **群集。執行\_自\_訂 指令**。 依預設，HDInsight 叢集系統管理員和 Ambari 系統管理員會具有此權限。

如需有關使用已加入網域之 HDInsight 的權限詳細資訊，請參閱[使用企業安全性套件管理 HDInsight 叢集](./domain-joined/apache-domain-joined-manage.md)。

## <a name="access-control"></a>存取控制

如果您不是 Azure 訂用帳戶的系統管理員或擁有者，您的帳戶必須至少具備包含 HDInsight 叢集之資源群組的「參與者」存取權。

對 Azure 訂閱具有至少參與者訪問許可權的人員必須以前註冊了提供程式。 當具有訂閱參與者訪問許可權的使用者創建資源時,將發生提供程式註冊。 對於不建立資源,請參閱[使用 REST 註冊提供者](https://msdn.microsoft.com/library/azure/dn790548.aspx)。

取得有關使用存取管理的詳細資訊：

* [開始在 Azure 入口網站中使用存取管理](../role-based-access-control/overview.md)
* [使用角色指派來管理 Azure 訂用帳戶資源的存取權](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>了解指令碼動作

指令碼動作是在 HDInsight 叢集中節點上執行的 Bash 指令碼。 指令碼動作的特性和功能如下：

* 必須儲存在可從 HDInsight 叢集存取的 URI 上。 以下是可能的儲存位置：

    * 對於常規群集:

      * ADLS Gen1:用於存取資料儲存庫的服務主體 HDInsight 必須具有對文稿的讀取存取許可權。 Data Lake Storage Gen1 中所儲存指令碼的 URI 格式為 `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`。

      * 本身是 HDInsight 叢集主要或額外儲存體帳戶之「Azure 儲存體」帳戶中的 Blob。 在建立叢集期間，已將這兩種儲存體帳戶的存取權都授與 HDInsight。

        > [!IMPORTANT]  
        > 不要在此 Azure 儲存帳戶上旋轉存儲金鑰,因為這將導致存儲腳本的後續腳本操作失敗。

      * 可通過HTTP://路徑訪問的公共文件共享服務。 範例包括 Azure Blob、GitHub、OneDrive。 如需範例 URI，請參閱[範例指令碼動作指令碼](#example-script-action-scripts)。

     * 對於具有 ESP 的群集,支援 wasb://或 wasbs://或 http_s_s_:// URI。

* 可限制為只在特定節點類型上執行。 例如前端節點或背景工作節點。

* 可以持久化或`ad hoc`。

    持續性指令碼動作必須有唯一的名稱。 持續性指令碼可用來自訂透過調整規模作業新增至叢集的新背景工作節點。 持續性指令碼也可以在進行調整規模作業時，將變更套用至另一個節點類型。 例如前端節點。

    `Ad hoc`腳本不會持久化。 建立叢集期間使用的指令碼動作會自動保存下來。 它們不會套用至在指令碼執行後新增至叢集的背景工作節點。 然後,`ad hoc`您可以將腳本升級為持久腳本,或將持久腳本降級`ad hoc`到 腳本。 失敗的指令碼即使您特別指示應保存，也不會保存下來。

* 可以接受指令碼在執行期間所使用的參數。

* 在叢集節點上以根層級權限執行。

* 可透過 Azure 門戶、Azure PowerShell、Azure CLI 或 HDInsight .NET SDK 使用。

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

您可以執行停止和啟動服務等操作,包括與 Apache Hadoop 相關的服務。 如果停止服務,請確保 Ambari 和其他與 Hadoop 相關的服務在文本完成之前運行。 這些必需的服務確定群集在創建時的運行狀況和狀態。

在叢集建立期間，您可以同時使用許多指令碼動作。 這些指令碼會以指定的順序叫用。

> [!IMPORTANT]  
> 腳本操作必須在 60 分鐘內完成,否則它們超時。在群集預配期間,腳本與其他設置和配置過程同時運行。 與在您開發環境中的執行時間相較，爭用 CPU 時間和網路頻寬等資源可能會導致指令碼需要較長的時間才能完成。
>
> 若要讓執行指令碼所花費的時間縮到最短，請避免進行從來源下載和編譯應用程式之類的工作。 請預先編譯應用程式，並將二進位檔儲存在「Azure 儲存體」中。

### <a name="script-action-on-a-running-cluster"></a>執行中叢集上的指令碼動作

已在運行的群集上的腳本故障不會自動導致群集更改為失敗狀態。 在指令碼完成後，叢集應該會回到執行中狀態。 即使叢集狀態為執行中，失敗的指令碼仍可能已造成問題。 例如，指令碼可能會刪除叢集所需的檔案。

指令碼動作會以根權限執行。 在將腳本應用於群集之前,請確保瞭解腳本的作用。

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

## <a name="script-action-during-cluster-creation"></a>叢集建立期間的文稿操作

本節說明建立 HDInsight 叢集時，您可以使用指令碼動作的不同方式。

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>在建立叢集期間從 Azure 入口網站使用指令碼動作

1. 使用 Azure 門戶,開始創建群集,如在[HDInsight 中創建基於 Linux 的群集](hdinsight-hadoop-create-linux-clusters-portal.md)中所述。 從 **「設定 + 定價**」選項卡中,選擇 **「添加腳本操作**」。

    ![Azure 門戶叢集文稿操作](./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-configuration-scriptaction.png)

1. 使用 [選取指令碼]____ 項目來選取預先製作的指令碼。 若要使用自訂指令碼，請選取 [自訂]____。 然後為您的指令碼提供 [名稱]____ 和 [Bash 指令碼 URI]____。

    ![在選取指令碼表單中加入指令碼](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    下表說明表單上的元素：

    | 屬性 | 值 |
    | --- | --- |
    | 選取指令碼 | 若要使用自己的指令碼，請選取 [自訂]____。 或是選取其中一個提供的指令碼。 |
    | 名稱 |指定指令碼動作的名稱。 |
    | Bash 指令碼 URI |指定指令碼的 URI。 |
    | 頭/工人/動物園管理員 |指定執行文稿的節點:**頭**、**輔助角色**或**ZooKeeper**。 |
    | 參數 |如果指令碼要求，請指定參數。 |

    請使用 [保存此指令碼動作]____ 項目，以確保在執行規模調整作業期間會套用此指令碼。

1. 選取 [Create] \(建立\)____ 以儲存指令碼。 接著，您可以使用 [+ 送出新的]____ 來新增另一個指令碼。

    ![HDInsight 多個文稿操作](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png)

    新增完文稿後,傳回到 **「配置 + 定價**」選項卡。

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

在本節中,您可以使用[Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) cmdlet 調用腳本來自定義群集。 在您開始之前，請務必先安裝和設定 Azure PowerShell。 要使用 PowerShell 指令,您需要[AZ 模組](https://docs.microsoft.com/powershell/azure/overview)。

下列指令碼示範如何使用 PowerShell 在建立叢集時套用指令碼動作：

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

建立叢集可能需要幾分鐘的時間。

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>在建立叢集期間從 HDInsight .NET SDK 使用指令碼動作

HDInsight .NET SDK 提供用戶端程式庫，可讓您更輕鬆地從 .NET 應用程式使用 HDInsight。 有關代碼範例,請參閱[文稿操作](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet#script-actions)。

## <a name="script-action-to-a-running-cluster"></a>文稿操作到執行的叢集

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
    | 選取指令碼 | 要使用自己的文稿,請選擇__自訂__。 否則，請選取提供的指令碼。 |
    | 名稱 |指定指令碼動作的名稱。 |
    | Bash 指令碼 URI |指定指令碼的 URI。 |
    | Head/Worker/Zookeeper |指定執行文稿的節點:**頭**、**輔助角色**或**ZooKeeper**。 |
    | 參數 |如果指令碼要求，請指定參數。 |

    使用 [保存此指令碼動作]____ 項目，可確保在執行規模調整作業時套用此指令碼。

1. 最後，選取 [建立]**** 按鈕以將指令碼套用至叢集。

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>從 Azure PowerShell 將指令碼動作套用到執行中的叢集

要使用 PowerShell 指令,您需要[AZ 模組](https://docs.microsoft.com/powershell/azure/overview)。 下列範例示範如何將指令碼動作套用至執行中的叢集：

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

    有效角色為`headnode``workernode``zookeepernode`,, `edgenode`。 如果腳本應應用於多個節點類型,則按空格分隔角色。 例如： `--roles headnode workernode` 。

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

    ![門戶文稿操作提交歷史記錄](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

1. 從歷程記錄中選取指令碼，以顯示此指令碼的 [屬性]**** 區段。 從視窗的頂端，您可以重新執行指令碼或將其升階。

    ![文稿操作屬性升級](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

1. 您還可以選擇文稿操作部份項目右邊的省略號 **...**

    ![已保留的文稿操作移除](./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png)

### <a name="azure-powershell"></a>Azure PowerShell

| Cmdlet | 函式 |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |擷取持續性指令碼動作的相關資訊。 此 cmdlet 不會撤銷文本執行的操作,它只會刪除持久化標誌。|
| `Get-AzHDInsightScriptActionHistory` |擷取已套用到叢集的指令碼動作歷程記錄，或特定指令碼的詳細資料。 |
| `Set-AzHDInsightPersistedScriptAction` |將`ad hoc`腳本操作提升為持久腳本操作。 |
| `Remove-AzHDInsightPersistedScriptAction` |將持久化腳本操作降級為`ad hoc`操作。 |

下列範例指令碼示範如何使用 Cmdlet 將指令碼先升級後再降級。

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="azure-cli"></a>Azure CLI

| Command | 描述 |
| --- | --- |
| [`az hdinsight script-action delete`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-delete) |刪除群集的指定持久化腳本操作。 此命令不會撤銷文本執行的操作,它只會刪除持久化的標誌。|
|[`az hdinsight script-action execute`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute)|在指定的 HDInsight 叢集上執行指令碼動作。|
| [`az hdinsight script-action list`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list) |列出指定群集的所有持久化腳本操作。 |
|[`az hdinsight script-action list-execution-history`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list-execution-history)|列出指定群集的所有腳本執行歷史記錄。|
|[`az hdinsight script-action promote`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-promote)|將指定的臨時腳本執行提升為持久腳本。|
|[`az hdinsight script-action show-execution-details`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-show-execution-details)|獲取給定文本執行 ID 的文本執行詳細資訊。|

### <a name="hdinsight-net-sdk"></a>HDInsight .NET SDK

如需使用 .NET SDK 從叢集擷取指令碼歷程記錄、將指令碼升階或降階的範例，請參閱[針對執行中的 Linux 型 HDInsight 叢集套用指令碼動作](https://github.com/Azure-Samples/hdinsight-dotnet-script-action) \(英文\)。

> [!NOTE]  
> 這個範例也示範如何使用 .NET SDK 來安裝 HDInsight 應用程式。

## <a name="next-steps"></a>後續步驟

* [為 HDInsight 開發文稿操作文稿](hdinsight-hadoop-script-actions-linux.md)
* [在 HDInsight 叢集新增儲存體](hdinsight-hadoop-add-storage.md)
* [排除文稿操作](troubleshoot-script-action.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png "叢集建立期間的階段"
