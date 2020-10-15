---
title: 教學課程：使用 Azure 自動化 Runbook 來建立叢集 - Azure HDInsight
description: 了解如何使用 Azure 自動化 Runbook，透過在雲端中執行的指令碼來建立和刪除 Azure HDInsight 叢集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 12/27/2019
ms.openlocfilehash: 05c0aaf6cc33442fa4f36eb38eb0d6d593fc6c1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "75553118"
---
# <a name="tutorial-create-azure-hdinsight-clusters-with-azure-automation"></a>教學課程：使用 Azure 自動化建立 Azure HDInsight 叢集

Azure 自動化可讓您建立在雲端執行的指令碼，並視需要或根據排程來管理 Azure 資源。 本文將說明如何建立 PowerShell Runbook 來建立和刪除 Azure HDInsight 叢集。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 安裝與 HDInsight 互動所需的模組。
> * 建立和儲存叢集建立期間所需的認證。
> * 建立新的 Azure 自動化 Runbook 來建立 HDInsight 叢集。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>Prerequisites

* 現有的 [Azure 自動化帳戶](../automation/automation-quickstart-create-account.md)。
* 現有的 [Azure 儲存體帳戶](../storage/common/storage-account-create.md)，將用來作為叢集存放區。

## <a name="install-hdinsight-modules"></a>安裝 HDInsight 模組

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取您的 Azure 自動化帳戶。
1. 選取 [共用資源]  底下的 [模組庫]  。
1. 在方塊中輸入 **AzureRM.Profile**，然後按 Enter 鍵進行搜尋。 選取可用的搜尋結果。
1. 在 **AzureRM.profile** 畫面上，選取 [匯入]  。 勾選更新 Azure 模組的方塊，然後選取 [確定]  。

    ![匯入 AzureRM.profile 模組](./media/manage-clusters-runbooks/import-azurermprofile-module.png)

1. 選取 [共用資源]  底下的 [模組庫]  ，返回模組庫。
1. 輸入 **HDInsight**。 選取 [AzureRM.HDInsight]  。

    ![瀏覽 HDInsight 模組](./media/manage-clusters-runbooks/browse-modules-hdinsight.png)

1. 在 [AzureRM.HDInsight]  面板上，選取 [匯入]  ，然後選取 [確定]  。

    ![匯入 AzureRM.HDInsight 模組](./media/manage-clusters-runbooks/import-azurermhdinsight-module.png)

## <a name="create-credentials"></a>建立認證

1. 在 [共用資源]  底下選取 [認證]  。
1. 選取 [新增認證]  。
1. 在 [新增認證]  面板上輸入必要的資訊。 此認證會用來儲存叢集密碼，以便您登入 Ambari。

    | 屬性 | 值 |
    | --- | --- |
    | 名稱 | `cluster-password` |
    | [使用者名稱] | `admin` |
    | 密碼 | `SECURE_PASSWORD` |
    | 確認密碼 | `SECURE_PASSWORD` |

1. 選取 [建立]  。
1. 以使用者名稱 `sshuser` 對新的認證 `ssh-password` 重複相同程序，並使用您選擇的密碼。 選取 [建立]  。 此認證會用來儲存叢集的 SSH 密碼。

    ![建立認證](./media/manage-clusters-runbooks/create-credentials.png)

## <a name="create-a-runbook-to-create-a-cluster"></a>建立 Runbook 來建立叢集

1. 在 [程序自動化]  下方，選取 [Runbook]  。
1. 選取 [建立 Runbook]  。
1. 在 [建立 Runbook]  面板上，輸入 Runbook 的名稱，例如 `hdinsight-cluster-create`。 從 [Runbook 類型]  下拉式清單中選取 [Powershell]  。
1. 選取 [建立]  。

    ![建立 Runbook](./media/manage-clusters-runbooks/create-runbook.png)

1. 在 [編輯 PowerShell Runbook]  畫面上輸入下列程式碼，然後選取 [發佈]  ：

    ![發佈 Runbook](./media/manage-clusters-runbooks/publish-runbook.png)

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $subscriptionID,
    
      [Parameter (Mandatory= $true)]
      [String] $resourceGroup,
    
      [Parameter (Mandatory= $true)]
      [String] $storageAccount,
    
      [Parameter (Mandatory= $true)]
      [String] $containerName,
    
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    # Set cluster variables
    $storageAccountKey = (Get-AzureRmStorageAccountKey –Name $storageAccount –ResourceGroupName $resourceGroup)[0].value 
    
    # Setting cluster credentials
    
    #Automation credential for Cluster Admin
    $clusterCreds = Get-AutomationPSCredential –Name 'cluster-password'
    
    #Automation credential for user to SSH into cluster
    $sshCreds = Get-AutomationPSCredential –Name 'ssh-password' 
    
    $clusterType = "Hadoop" #Use any supported cluster type (Hadoop, HBase, Storm, etc.)
    $clusterOS = "Linux"
    $clusterWorkerNodes = 3
    $clusterNodeSize = "Standard_D3_v2"
    $location = Get-AzureRmStorageAccount –StorageAccountName $storageAccount –ResourceGroupName $resourceGroup | %{$_.Location}
    
    ### Provision HDInsight cluster
    New-AzureRmHDInsightCluster –ClusterName $clusterName –ResourceGroupName $resourceGroup –Location $location –DefaultStorageAccountName "$storageAccount.blob.core.windows.net" –DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName –ClusterType $clusterType –OSType $clusterOS –Version “3.6” –HttpCredential $clusterCreds –SshCredential $sshCreds –ClusterSizeInNodes $clusterWorkerNodes –HeadNodeSize $clusterNodeSize –WorkerNodeSize $clusterNodeSize
    ```

## <a name="create-a-runbook-to-delete-a-cluster"></a>建立 Runbook 來刪除叢集

1. 在 [程序自動化]  下方，選取 [Runbook]  。
1. 選取 [建立 Runbook]  。
1. 在 [建立 Runbook]  面板上，輸入 Runbook 的名稱，例如 `hdinsight-cluster-delete`。 從 [Runbook 類型]  下拉式清單中選取 [Powershell]  。
1. 選取 [建立]  。
1. 在 [編輯 PowerShell Runbook]  畫面上輸入下列程式碼，然後選取 [發佈]  ：

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    Remove-AzureRmHDInsightCluster -ClusterName $clusterName
    ```

## <a name="execute-runbooks"></a>執行 Runbook

### <a name="create-a-cluster"></a>建立叢集

1. 若要檢視您自動化帳戶的 Runbook 清單，請選取 [程序自動化]  底下的 [Runbook]  。
1. 選取 `hdinsight-cluster-create`，或建立「叢集建立 Runbook」時使用的名稱。
1. 選取 [啟動]  以立即執行 Runbook。 您也可以為 Runbook 設定排程以定期執行。 請參閱[在 Azure 自動化中排程 Runbook](../automation/shared-resources/schedules.md)
1. 輸入用於指令碼的必要參數，然後選取 [確定]  。 這會使用您在 **CLUSTERNAME** 參數中指定的名稱來建立新的 HDInsight 叢集。

    ![執行建立叢集 Runbook](./media/manage-clusters-runbooks/execute-create-runbook.png)

### <a name="delete-a-cluster"></a>刪除叢集

選取您建立的 `hdinsight-cluster-delete` Runbook 來刪除叢集。 選取 [啟動]  ，輸入 **CLUSTERNAME** 參數，然後選取 [確定]  。

## <a name="clean-up-resources"></a>清除資源

不再需要時，請刪除已建立的 Azure 自動化帳戶，以避免產生非預期的費用。 若要這麼做，請瀏覽至 Azure 入口網站，選取您在其中建立 Azure 自動化帳戶的資源群組並選取 [自動化帳戶]，然後選取 [刪除]  。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Azure PowerShell 管理 HDInsight 上的 Apache Hadoop 叢集](hdinsight-administer-use-powershell.md)