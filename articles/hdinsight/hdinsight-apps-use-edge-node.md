---
title: 在 Azure HDInsight 中的 Apache Hadoop 群集上使用空邊緣節點
description: 如何將空白邊緣節點新增至可作為用戶端的 HDInsight 叢集，然後測試/主控 HDInsight 應用程式。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/27/2020
ms.openlocfilehash: d7723ea63cbb9bab6adf42d7e92f84a6b8b2ab9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272600"
---
# <a name="use-empty-edge-nodes-on-apache-hadoop-clusters-in-hdinsight"></a>在 HDInsight 中的 Apache Hadoop 叢集上使用空白邊緣節點

了解如何將空白邊緣節點新增至 HDInsight 叢集。 空邊緣節點是 Linux 虛擬機器，其用戶端工具安裝和配置與頭節點相同，但沒有運行[Apache Hadoop](https://hadoop.apache.org/)服務。 您可以使用邊緣節點來存取叢集、測試用戶端應用程式，以及裝載用戶端應用程式。

您可以將空白邊緣節點新增至現有 HDInsight 叢集，以及在建立叢集時新增到新的叢集。 空白邊緣節點是使用 Azure Resource Manager 範本來新增。  以下示例演示如何使用範本完成：

    "resources": [
        {
            "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
            "type": "Microsoft.HDInsight/clusters/applications",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [ "[concat('Microsoft.HDInsight/clusters/',parameters('clusterName'))]" ],
            "properties": {
                "marketPlaceIdentifier": "EmptyNode",
                "computeProfile": {
                    "roles": [{
                        "name": "edgenode",
                        "targetInstanceCount": 1,
                        "hardwareProfile": {
                            "vmSize": "{}"
                        }
                    }]
                },
                "installScriptActions": [{
                    "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                    "uri": "[parameters('installScriptAction')]",
                    "roles": ["edgenode"]
                }],
                "uninstallScriptActions": [],
                "httpsEndpoints": [],
                "applicationType": "CustomApplication"
            }
        }
    ],

如範例所示，您可以選擇性地呼叫[指令碼動作](hdinsight-hadoop-customize-cluster-linux.md)來執行其他設定，例如在邊緣節點中安裝 [Apache Hue](hdinsight-hadoop-hue-linux.md)。 指令碼動作指令碼必須可在網站上公開存取。  例如，如果腳本存儲在 Azure 存儲中，請使用公共容器或公共 Blob。

邊緣節點虛擬機器大小必須符合 HDInsight 叢集背景工作節點 VM 大小需求。 如需建議的背景工作節點 VM 大小，請參閱[在 HDInsight 中建立 Apache Hadoop 叢集](hdinsight-hadoop-provision-linux-clusters.md#cluster-type)。

創建邊緣節點後，可以使用 SSH 連接到邊緣節點，並運行用戶端工具以訪問 HDInsight 中的 Hadoop 群集。

> [!WARNING]
> 邊緣節點上安裝的自訂元件會收到來自 Microsoft 的商業上合理支援。 可能可以解決您遇到的問題。 或者，您可能需要參考社群資源以取得進一步協助。 以下是從社群取得協助的一些最活躍網站：
>
> * [HDInsight 的 MSDN 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [https://stackoverflow.com](https://stackoverflow.com).
>
> 如果您使用的是 Apache 技術，則可以通過 上的[https://apache.org](https://apache.org)Apache 專案網站（如 Apache [Hadoop](https://hadoop.apache.org/)網站）找到説明。

> [!IMPORTANT]
> Ubuntu 映像在發行後的 3 個月內就會變成可用於建立新的 HDInsight 叢集。 截至 2019 年 1 月為止，執行中的叢集 (包括邊緣節點) 都**不會**自動修補。 客戶必須使用指令碼動作或其他機制修補執行中的叢集。  如需詳細資訊，請參閱 [HDInsight 的作業系統修補](./hdinsight-os-patching.md)。

## <a name="add-an-edge-node-to-an-existing-cluster"></a>將邊緣節點新增至現有叢集

在本節中，您會使用 Resource Manager 範本將邊緣節點新增至現有 HDInsight 叢集。  Resource Manager 範本可在 [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/)中找到。 資源管理器範本調用位於 的https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh腳本操作。腳本不執行任何操作。  它是演示從資源管理器範本調用腳本操作。

1. 選擇以下映射登錄到 Azure 並在 Azure 門戶中打開 Azure 資源管理器範本。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. 設定下列屬性：

    |屬性 |描述 |
    |---|---|
    |訂用帳戶|選取用來建立叢集的 Azure 訂用帳戶。|
    |資源群組|選取用於現有 HDInsight 叢集的資源群組。|
    |Location|選取現有 HDInsight 叢集的位置。|
    |叢集名稱|輸入現有 HDInsight 叢集的名稱。|

1. 檢查**我同意上述條款及條件**，然後選擇 **"購買"** 以創建邊緣節點。

> [!IMPORTANT]  
> 請務必選取用於現有 HDInsight 叢集的 Azure 資源群組。  否則，您會收到錯誤訊息：「無法對巢狀資源執行所要求的作業。 找不到父資源 '&lt;叢集名稱>'」。

## <a name="add-an-edge-node-when-creating-a-cluster"></a>在建立叢集時新增邊緣節點

在本節中，您會使用 Resource Manager 範本對邊緣節點建立 HDInsight 叢集。  資源管理器範本可以在[Azure 快速入門範本庫中](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/)找到。 資源管理器範本調用位於 的https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh腳本操作。腳本不執行任何操作。  它是演示從資源管理器範本調用腳本操作。

1. 如果您還沒有 HDInsight 叢集，請予以建立。  請參閱[開始在 HDInsight 中使用 Hadoop](hadoop/apache-hadoop-linux-tutorial-get-started.md)。

1. 選擇以下映射登錄到 Azure 並在 Azure 門戶中打開 Azure 資源管理器範本。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. 設定下列屬性：

    |屬性 |描述 |
    |---|---|
    |訂用帳戶|選取用來建立叢集的 Azure 訂用帳戶。|
    |資源群組|建立用於叢集的新資源群組。|
    |Location|選取資源群組的位置。|
    |叢集名稱|輸入要建立之新叢集的名稱。|
    |叢集登入使用者名稱|輸入 Hadoop HTTP 使用者名稱。  預設名稱為 **admin**。|
    |叢集登入密碼|輸入 Hadoop HTTP 使用者密碼。|
    |Ssh 使用者名|輸入 SSH 使用者名稱。 預設名稱為 **sshuser**。|
    |Ssh 密碼|輸入 SSH 使用者密碼。|
    |安裝腳本操作|保留用於流覽本文的預設值。|

    某些屬性已被硬式編碼在範本中︰叢集類型、叢集背景工作角色節點計數、邊緣節點大小與邊緣節點名稱。

1. 檢查**我同意上述條款及條件**，然後選擇 **"購買"** 以創建具有邊緣節點的群集。

## <a name="add-multiple-edge-nodes"></a>新增多個邊緣節點

您可以將多個邊緣節點新增至 HDInsight 叢集。  多個邊緣節點的設定只能使用 Azure Resource Manager 範本來執行。  請參閱本文開頭處的範本範例。  您必須更新 **targetInstanceCount**，以反映您要建立的邊緣節點數目。

## <a name="access-an-edge-node"></a>存取邊緣節點

邊緣節點 ssh 端點是 &lt;EdgeNodeName>.&lt;ClusterName>-ssh.azurehdinsight.net:22。  例如，new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22。

在 Azure 入口網站上，邊緣節點會顯示為應用程式。  入口網站可提供您相關資訊，以便使用 SSH 存取邊緣節點。

**確認邊緣節點 SSH 端點**

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 開啟具有邊緣節點的 HDInsight 叢集。
3. 選取**應用程式**。 您應該會看到邊緣節點。  預設名稱為 **new-edgenode**中找到。
4. 選擇邊緣節點。 您應該會看到 SSH 端點。

**在邊緣節點上使用 Hive**

1. 使用 SSH 連線到邊緣節點。 如需相關資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。

2. 使用 SSH 連接到邊緣節點後，請使用以下命令打開 Hive 主控台：

        hive

3. 執行下列命令，以顯示叢集中的 Hive 資料表︰

        show tables;

## <a name="delete-an-edge-node"></a>刪除邊緣節點

您可以從 Azure 入口網站刪除邊緣節點。

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 開啟具有邊緣節點的 HDInsight 叢集。
3. 選取**應用程式**。 您應該會看到邊緣節點清單。  
4. 按右鍵要刪除的邊緣節點，然後選擇 **"刪除**"。
5. 選取 [是] **** 加以確認。

## <a name="next-steps"></a>後續步驟

在本文中，您學習了如何添加邊緣節點以及如何訪問邊緣節點。 如需詳細資訊，請參閱下列文章：

* [安裝 HDInsight 應用程式](hdinsight-apps-install-applications.md)︰了解如何將 HDInsight 應用程式安裝到您的叢集。
* [安裝自訂 HDInsight 應用程式](hdinsight-apps-install-custom-applications.md)︰了解如何將未發佈的 HDInsight 應用程式部署到 HDInsight。
* [發佈 HDInsight 應用程式](hdinsight-apps-publish-applications.md)︰了解如何將自訂 HDInsight 應用程式發佈至 Azure Marketplace。
* [MSDN：安裝 HDInsight 應用程式](https://msdn.microsoft.com/library/mt706515.aspx)︰了解如何定義 HDInsight 應用程式。
* [使用指令碼動作自訂以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)：了解如何使用指令碼動作來安裝其他應用程式。
* [使用 Resource Manager 範本在 HDInsight 中建立以 Linux 為基礎的 Apache Hadoop 叢集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)︰了解如何呼叫 Resource Manager 範本來建立 HDInsight 叢集。
