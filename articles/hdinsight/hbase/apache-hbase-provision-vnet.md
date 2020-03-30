---
title: 在虛擬網路上建立 HBase 叢集 - Azure
description: 開始在 Azure HDInsight 中使用 HBase。 了解如何在 Azure 虛擬網路上建立 HDInsight HBase 叢集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/23/2019
ms.openlocfilehash: e4e15d1c6554fc567f668b2033bff5b5664db918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972786"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>在 Azure 虛擬網路中的 HDInsight 上建立 Apache HBase 叢集

了解如何在 [Azure 虛擬網路](https://azure.microsoft.com/services/virtual-network/)中建立 Azure HDInsight Apache HBase 叢集。

由於 Apache HBase 叢集已與虛擬網路整合，因此能夠部署到與您應用程式相同的虛擬網路，讓應用程式得以和 HBase 直接通訊。 其優點包括：

* Web 應用程式可以直接連接到 HBase 叢集節點，因而能夠使用 HBase Java 遠端程序呼叫 (RPC) API 來進行通訊。
* 透過使流量不須經過多個閘道器及負載平衡器，以提升其效能。
* 能夠以更安全的方式處理敏感資訊，而不會暴露公用端點。

如果沒有 Azure 訂閱，請先創建[一個免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。"

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>在虛擬網路中建立 Apache HBase 叢集

在本節中，您會使用 [Azure Resource Manager 範本](../../azure-resource-manager/templates/deploy-powershell.md)，在 Azure 虛擬網路中建立以 Linux 為基礎的 Apache HBase 叢集與相依的 Azure 儲存體帳戶。 如需其他叢集建立方法及了解各項設定，請參閱 [建立 HDInsight 叢集](../hdinsight-hadoop-provision-linux-clusters.md)。 如需有關使用範本在 HDInsight 中建立 Apache Hadoop 叢集的詳細資訊，請參閱 [使用 Azure Resource Manager 範本在 HDInsight 中建立 Apache Hadoop 叢集](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)

> [!NOTE]  
> 某些屬性已以硬式編碼方式寫入範本。 例如：
>
> * **位置**：美國東部 2
> * **叢集版本**：3.6
> * **叢集背景工作節點計數**：2
> * **預設儲存體帳戶**：唯一的字串
> * **虛擬網路名稱**： CLUSTERNAME-vnet
> * **虛擬網路位址空間**： 10.0.0.0/16
> * **子網名稱**：子網1
> * **子網位址範圍**： 10.0.0.0/24
>
> `CLUSTERNAME`替換為使用範本時提供的群集名稱。

1. 選取以下影像，在 Azure 入口網站中開啟範本。 範本位在 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/)。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. 在 **"自訂部署"** 對話方塊中，選擇 **"編輯範本**"。

1. 在行 165 上`Standard_A3`，`Standard_A4_V2`將值更改為 。 然後選擇 **"保存**"。

1. 使用以下資訊完成其餘範本：

    |屬性 |值 |
    |---|---|
    |訂用帳戶|選取用來建立 HDInsight 叢集、相依儲存體帳戶和 Azure 虛擬網路的 Azure 訂用帳戶。|
    資源群組|選取 [新建]**** 並指定新的資源群組名稱。|
    |Location|選取資源群組的位置。|
    |叢集名稱|輸入要建立的 Hadoop 叢集的名稱。|
    |群集登錄使用者名和密碼|預設使用者名是**管理員**。提供密碼。|
    |Ssh 使用者名和密碼|預設使用者名是**ssuser**。  提供密碼。|

    選擇**我同意上述條款及條件**。

1. 選取 [購買]****。 大約需要 20 分鐘的時間來建立叢集。 創建群集後，可以選擇門戶中的群集來打開群集。

完成文章後，可能需要刪除群集。 利用 HDInsight，您的資料會儲存在 Azure 儲存體中，以便您在未使用叢集時安全地進行刪除。 您也需支付 HDInsight 叢集的費用 (即使未使用)。 由於叢集費用是儲存體費用的許多倍，所以刪除未使用的叢集符合經濟效益。 如需有關刪除叢集的指示，請參閱[使用 Azure 入口網站管理 HDInsight 中的 Apache Hadoop 叢集](../hdinsight-administer-use-portal-linux.md#delete-clusters)。

若要開始使用新的 HBase 叢集，您可以使用[開始在 HDInsight 中搭配使用 Apache HBase 與 Apache Hadoop](./apache-hbase-tutorial-get-started-linux.md) 中提供的程序。

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>使用 Apache HBase Java RPC API 連線至 Apache HBase 叢集

### <a name="create-a-virtual-machine"></a>建立虛擬機器

相同的 Azure 虛擬網路和相同的子網路中建立基礎結構即服務 (IaaS) 虛擬機器。 如需建立新的 IaaS 虛擬機器的指示，請參閱[建立執行 Windows Server 的虛擬機器](../../virtual-machines/windows/quick-create-portal.md)。 當遵循本文件中的步驟時，您必須針對網路設定使用下列值：

* **虛擬網路**： CLUSTERNAME-vnet
* **子網**：子網1

> [!IMPORTANT]  
> 在`CLUSTERNAME`前幾個步驟中，使用創建 HDInsight 群集時使用的名稱替換。

使用這些值會將虛擬機器放置在與 HDInsight 叢集相同的虛擬網路和子網路。 此組態可讓它們彼此直接通訊。 有一個使用空白邊緣節點建立 HDInsight 叢集的方法。 邊緣節點可用來管理叢集。  如需詳細資訊，請參閱 [Use empty edge nodes in HDInsight (在 HDInsight 中使用空白的邊緣節點)](../hdinsight-apps-use-edge-node.md)。

### <a name="obtain-fully-qualified-domain-name"></a>獲取完全合格的功能變數名稱

使用 Java 應用程式從遠端連接到 HBase 時，您必須使用完整網域名稱 (FQDN)。 若要決定此名稱，您必須取得 HBase 叢集的連線特定 DNS 尾碼。 若要這麼做，您可以使用下列其中一種方法：

* 使用網頁瀏覽器進行 [Apache Ambari](https://ambari.apache.org/) 呼叫︰

    瀏覽至 `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts?minimal_response=true`。 它返回帶有 DNS 尾碼的 JSON 檔。

* 使用 Ambari 網站︰

    1. 瀏覽至 `https://CLUSTERNAME.azurehdinsight.net`。
    2. 從頂部功能表中選擇**主機**。

* 使用 Curl 進行 REST 呼叫︰

    ```bash
    curl -u <username>:<password> -k https://CLUSTERNAME.azurehdinsight.net/ambari/api/v1/clusters/CLUSTERNAME.azurehdinsight.net/services/hbase/components/hbrest
    ```

在傳回的 JavaScript 物件標記法 (JSON) 資料中，找出 "host_name" 項目。 其中包含叢集中節點的 FQDN。 例如：

```
"host_name" : "hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net"
```

以叢集名稱開頭的網域名稱部分就是 DNS 尾碼。 例如： `hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net` 。

<!--
3.    Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix](./media/apache-hbase-provision-vnet/hdi-primary-dns-suffix.png)
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

### <a name="verify-communication-inside-virtual-network"></a>驗證虛擬網路內部的通信

若要驗證虛擬機器能夠與 HBase 叢集通訊，請從虛擬機器使用命令 `ping headnode0.<dns suffix>` 。 例如： `ping hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net` 。

若要在 Java 應用程式中使用此資訊，您可以依照[使用 Apache Maven 建置在 HDInsight (Hadoop) 上使用 Apache HBase 的 Java 應用程式](./apache-hbase-build-java-maven-linux.md)中的步驟來建立應用程式。 若要讓應用程式連接到遠端 HBase 伺服器，請修改此範例中的 **hbase-site.xml** 檔案，以使用 Zookeeper 的 FQDN。 例如：

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [!NOTE]  
> 如需 Azure 虛擬網路中名稱解析的詳細資訊，包括如何使用您自己的 DNS 伺服器，請參閱[名稱解析 (DNS)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。

## <a name="next-steps"></a>後續步驟

在本文中，您學習了如何創建 Apache HBase 群集。 若要深入了解，請參閱：

* [開始使用 HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [在 HDInsight 中使用空白邊緣節點](../hdinsight-apps-use-edge-node.md)
* [在 HDInsight 中設定 Apache HBase 複寫](apache-hbase-replication.md)
* [在 HDInsight 中建立 Apache Hadoop 叢集](../hdinsight-hadoop-provision-linux-clusters.md)
* [開始在 HDInsight 中搭配 Apache Hadoop 使用 Apache HBase](./apache-hbase-tutorial-get-started-linux.md)
* [虛擬網路概述](../../virtual-network/virtual-networks-overview.md)
