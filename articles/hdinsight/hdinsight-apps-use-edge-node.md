---
title: 在 Azure HDInsight 中的 Apache Hadoop 叢集上使用空白邊緣節點
description: 如何將空白邊緣節點新增至 HDInsight 叢集。 用來作為用戶端，然後測試或裝載您的 HDInsight 應用程式。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/16/2020
ms.openlocfilehash: f6dea00bf3b3e8a58f42da8fd8ad59ccec2dea72
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537792"
---
# <a name="use-empty-edge-nodes-on-apache-hadoop-clusters-in-hdinsight"></a>在 HDInsight 中的 Apache Hadoop 叢集上使用空白邊緣節點

了解如何將空白邊緣節點新增至 HDInsight 叢集。 空白的邊緣節點是一部 Linux 虛擬機器，其中已安裝及設定和前端節點相同的用戶端工具。 但不執行任何[Apache Hadoop](./hadoop/apache-hadoop-introduction.md)服務。 您可以使用邊緣節點來存取叢集、測試用戶端應用程式，以及裝載用戶端應用程式。

您可以將空白邊緣節點新增至現有 HDInsight 叢集，以及在建立叢集時新增到新的叢集。 空白邊緣節點是使用 Azure Resource Manager 範本來新增。  下列範例示範如何使用範本來完成：

```json
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
```

如範例所示，您可以選擇性地呼叫[腳本動作](hdinsight-hadoop-customize-cluster-linux.md)來執行其他設定。 例如在邊緣節點中安裝[Apache 色調](hdinsight-hadoop-hue-linux.md)。 指令碼動作指令碼必須可在網站上公開存取。  例如，如果腳本儲存在 Azure 儲存體中，請使用公用容器或公用 blob。

邊緣節點虛擬機器大小必須符合 HDInsight 叢集背景工作節點 VM 大小需求。 如需建議的背景工作節點 VM 大小，請參閱[在 HDInsight 中建立 Apache Hadoop 叢集](hdinsight-hadoop-provision-linux-clusters.md#cluster-type)。

建立邊緣節點之後，您可以使用 SSH 連接到邊緣節點，並執行用戶端工具來存取 HDInsight 中的 Hadoop 叢集。

> [!WARNING]
> 邊緣節點上安裝的自訂元件會收到來自 Microsoft 的商業上合理支援。 可能可以解決您遇到的問題。 或者，您可能需要參考社群資源以取得進一步協助。 以下是從社群取得協助的一些最活躍網站：
>
> * [HDInsight 的 MSDN 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [https://stackoverflow.com](https://stackoverflow.com).
>
> 如果您使用 Apache 技術，您可能可以透過上[https://apache.org](https://apache.org)的 apache 專案網站尋求協助，例如[apache Hadoop](https://hadoop.apache.org/)網站。

> [!IMPORTANT]
> Ubuntu 映像在發行後的 3 個月內就會變成可用於建立新的 HDInsight 叢集。 截至 2019 年 1 月為止，執行中的叢集 (包括邊緣節點) 都**不會**自動修補。 客戶必須使用指令碼動作或其他機制修補執行中的叢集。  如需詳細資訊，請參閱 [HDInsight 的作業系統修補](./hdinsight-os-patching.md)。

## <a name="add-an-edge-node-to-an-existing-cluster"></a>將邊緣節點新增至現有叢集

在本節中，您會使用 Resource Manager 範本將邊緣節點新增至現有 HDInsight 叢集。  Resource Manager 範本可在 [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/)中找到。 Resource Manager 範本會呼叫位於的腳本動作https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh。腳本不會執行任何動作。  這是為了示範從 Resource Manager 範本呼叫腳本動作。

1. 選取下列影像以登入 Azure，然後在 Azure 入口網站中開啟 [Azure Resource Manager] 範本。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. 設定下列屬性：

    |屬性 |描述 |
    |---|---|
    |訂用帳戶|選取用來建立叢集的 Azure 訂用帳戶。|
    |資源群組|選取用於現有 HDInsight 叢集的資源群組。|
    |位置|選取現有 HDInsight 叢集的位置。|
    |叢集名稱|輸入現有 HDInsight 叢集的名稱。|

1. 核取 **[我同意上方所述的條款及條件**]，然後選取 [**購買**] 以建立邊緣節點。

> [!IMPORTANT]  
> 請務必選取用於現有 HDInsight 叢集的 Azure 資源群組。  否則，您會收到錯誤訊息：「無法對巢狀資源執行所要求的作業。 找不到父資源 '&lt;叢集名稱>'」。

## <a name="add-an-edge-node-when-creating-a-cluster"></a>在建立叢集時新增邊緣節點

在本節中，您會使用 Resource Manager 範本對邊緣節點建立 HDInsight 叢集。  您可以在[Azure 快速入門範本資源庫](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/)中找到 Resource Manager 範本。 Resource Manager 範本會呼叫位於的腳本動作https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh。腳本不會執行任何動作。  這是為了示範從 Resource Manager 範本呼叫腳本動作。

1. 如果您還沒有 HDInsight 叢集，請予以建立。  請參閱[開始在 HDInsight 中使用 Hadoop](hadoop/apache-hadoop-linux-tutorial-get-started.md)。

1. 選取下列影像以登入 Azure，然後在 Azure 入口網站中開啟 [Azure Resource Manager] 範本。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. 設定下列屬性：

    |屬性 |描述 |
    |---|---|
    |訂用帳戶|選取用來建立叢集的 Azure 訂用帳戶。|
    |資源群組|建立用於叢集的新資源群組。|
    |位置|選取資源群組的位置。|
    |叢集名稱|輸入要建立之新叢集的名稱。|
    |叢集登入使用者名稱|輸入 Hadoop HTTP 使用者名稱。  預設名稱為 **admin**。|
    |叢集登入密碼|輸入 Hadoop HTTP 使用者密碼。|
    |SSH 使用者名稱|輸入 SSH 使用者名稱。 預設名稱為 **sshuser**。|
    |SSH 密碼|輸入 SSH 使用者密碼。|
    |安裝腳本動作|請保留預設值，以進行本文。|

    某些屬性已被硬式編碼在範本中︰叢集類型、叢集背景工作角色節點計數、邊緣節點大小與邊緣節點名稱。

1. 核取 [**我同意上方所述的條款及條件**]，然後選取 [**購買**] 以建立具有邊緣節點的叢集。

## <a name="add-multiple-edge-nodes"></a>新增多個邊緣節點

您可以將多個邊緣節點新增至 HDInsight 叢集。  多個邊緣節點的設定只能使用 Azure Resource Manager 範本來執行。  請參閱本文開頭處的範本範例。  更新**targetInstanceCount** ，以反映您想要建立的邊緣節點數目。

## <a name="access-an-edge-node"></a>存取邊緣節點

邊緣節點 ssh 端點是 &lt;EdgeNodeName>.&lt;ClusterName>-ssh.azurehdinsight.net:22。  例如，new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22。

在 Azure 入口網站上，邊緣節點會顯示為應用程式。  入口網站可提供您相關資訊，以便使用 SSH 存取邊緣節點。

**確認邊緣節點 SSH 端點**

1. 登入[Azure 入口網站](https://portal.azure.com)。
2. 開啟具有邊緣節點的 HDInsight 叢集。
3. 選取**應用程式**。 您應該會看到邊緣節點。  預設名稱為 **new-edgenode**中找到。
4. 選取邊緣節點。 您應該會看到 SSH 端點。

**在邊緣節點上使用 Hive**

1. 使用 SSH 連線到邊緣節點。 如需相關資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。

2. 使用 SSH 連線到邊緣節點之後，請使用下列命令來開啟 Hive 主控台：

        hive

3. 執行下列命令，以顯示叢集中的 Hive 資料表︰

        show tables;

## <a name="delete-an-edge-node"></a>刪除邊緣節點

您可以從 Azure 入口網站刪除邊緣節點。

1. 登入[Azure 入口網站](https://portal.azure.com)。
2. 開啟具有邊緣節點的 HDInsight 叢集。
3. 選取**應用程式**。 您應該會看到邊緣節點清單。  
4. 以滑鼠右鍵按一下您想要刪除的邊緣節點，然後選取 [**刪除**]。
5. 選取 [是]  確認。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何新增邊緣節點，以及如何存取邊緣節點。 如需詳細資訊，請參閱下列文章：

* [安裝 HDInsight 應用程式](hdinsight-apps-install-applications.md)︰了解如何將 HDInsight 應用程式安裝到您的叢集。
* [安裝自訂 HDInsight 應用程式](hdinsight-apps-install-custom-applications.md)︰了解如何將未發佈的 HDInsight 應用程式部署到 HDInsight。
* [發佈 HDInsight 應用程式](hdinsight-apps-publish-applications.md)︰了解如何將自訂 HDInsight 應用程式發佈至 Azure Marketplace。
* [MSDN：安裝 HDInsight 應用程式](https://msdn.microsoft.com/library/mt706515.aspx)︰了解如何定義 HDInsight 應用程式。
* [使用指令碼動作自訂以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)：了解如何使用指令碼動作來安裝其他應用程式。
* [使用 Resource Manager 範本在 HDInsight 中建立以 Linux 為基礎的 Apache Hadoop 叢集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)︰了解如何呼叫 Resource Manager 範本來建立 HDInsight 叢集。
