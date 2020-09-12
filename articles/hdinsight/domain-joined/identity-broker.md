---
title: 使用識別碼代理程式 (預覽) 進行認證管理-Azure HDInsight
description: 深入瞭解 HDInsight 識別碼代理人，以簡化已加入網域之 Apache Hadoop 叢集的驗證。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 12/12/2019
ms.openlocfilehash: 3b2807ccd6d83511dd0c9a32a177ea9fe2c4b642
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89662089"
---
# <a name="use-id-broker-preview-for-credential-management"></a>使用識別碼代理 (預覽版) 進行認證管理

本文說明如何在 Azure HDInsight 中設定和使用識別碼訊息代理程式功能。 您可以使用這項功能透過 Azure Multi-Factor Authentication 登入 Apache Ambari，並取得所需的 Kerberos 票證，而不需要 Azure Active Directory Domain Services (Azure AD DS) 中的密碼雜湊。

## <a name="overview"></a>概觀

在下列案例中，識別碼 Broker 可簡化複雜的驗證：

* 您的組織會依賴同盟來驗證使用者，以存取雲端資源。 在過去，若要使用 HDInsight 企業安全性套件 (ESP) 叢集，您必須啟用從內部部署環境到 Azure Active Directory 的密碼雜湊同步處理。 對於某些組織而言，這項需求可能很困難或不適當。

* 您正在建立使用依賴不同驗證機制之技術的解決方案。 例如，Apache Hadoop 和 Apache Ranger 依賴 Kerberos，而 Azure Data Lake Storage 依賴 OAuth。

識別碼代理程式提供統一的驗證基礎結構，並移除將密碼雜湊同步處理至 Azure AD DS 的需求。 識別碼代理程式是由在 Windows Server VM 上執行的元件所組成， (識別碼 Broker 節點) ，以及叢集閘道節點。 

下圖顯示啟用識別碼訊息代理程式之後，所有使用者的驗證流程（包括同盟使用者）：

![具有識別碼 Broker 的驗證流程](./media/identity-broker/identity-broker-architecture.png)

識別碼訊息代理程式可讓您使用 Multi-Factor Authentication 登入 ESP 叢集，而不需提供任何密碼。 如果您已經登入其他 Azure 服務（例如 Azure 入口網站），您可以使用單一登入 (SSO) 體驗登入 HDInsight 叢集。

## <a name="enable-hdinsight-id-broker"></a>啟用 HDInsight 識別碼代理程式

若要建立已啟用 ID Broker 的 ESP 叢集，請執行下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 遵循 ESP 叢集的基本建立步驟。 如需詳細資訊，請參閱 [建立具有 ESP 的 HDInsight](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)叢集。
1. 選取 [ **啟用 HDINSIGHT 識別碼代理**程式]。

識別碼訊息代理程式功能會將一個額外的 VM 新增至叢集中。 此 VM 是識別碼訊息代理程式節點，包含支援驗證的伺服器元件。 識別碼訊息代理程式節點已加入網域，以加入 Azure AD DS 網域。

![啟用 ID Broker 的選項](./media/identity-broker/identity-broker-enable.png)

### <a name="using-azure-resource-manager-templates"></a>使用 Azure Resource Manager 範本
如果您將 `idbrokernode` 使用下列屬性（property）呼叫的角色新增至範本的計算設定檔，則會建立叢集，並啟用 ID broker 節點：

```json
.
.
.
"computeProfile": {
    "roles": [
        {
            "autoscale": null,
            "name": "headnode",
           ....
        },
        {
            "autoscale": null,
            "name": "workernode",
            ....
        },
        {
            "autoscale": null,
            "name": "idbrokernode",
            "targetInstanceCount": 1,
            "hardwareProfile": {
                "vmSize": "Standard_A2_V2"
            },
            "virtualNetworkProfile": {
                "id": "string",
                "subnet": "string"
            },
            "scriptActions": [],
            "dataDisksGroups": null
        }
    ]
}
.
.
.
```

## <a name="tool-integration"></a>工具整合

已更新 HDInsight [IntelliJ 外掛程式](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) 以支援 OAuth。 您可以使用此外掛程式連接到叢集並提交作業。

您也可以使用 [Spark & Hive Tools for VS Code](https://docs.microsoft.com/azure/hdinsight/hdinsight-for-vscode) 來利用筆記本和提交作業。

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Azure AD DS 中沒有密碼雜湊的 SSH 存取

啟用 ID Broker 之後，您仍然需要使用網域帳戶在 SSH 案例的 Azure AD DS 中儲存密碼雜湊。 若要透過 SSH 連線到已加入網域的 VM，或要執行 `kinit` 命令，您必須提供密碼。 

SSH 驗證需要 Azure AD DS 提供雜湊。 如果您只想要針對系統管理案例使用 SSH，您可以建立一個僅限雲端的帳戶，並使用該帳戶透過 SSH 連線到叢集。 其他使用者仍然可以使用 Ambari 或 HDInsight 工具 (例如 IntelliJ 外掛程式) ，而不需要在 Azure AD DS 中使用密碼雜湊。

若要對驗證問題進行疑難排解，請參閱本 [指南](https://docs.microsoft.com/azure/hdinsight/domain-joined/domain-joined-authentication-issues)。

## <a name="clients-using-oauth-to-connect-to-hdinsight-gateway-with-id-broker-setup"></a>使用 OAuth 連線到具有 ID Broker 設定之 HDInsight 閘道的用戶端

在識別碼代理程式安裝程式中，您可以更新連接到閘道的自訂應用程式和用戶端，以先取得必要的 OAuth 權杖。 您可以依照這 [份檔](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app) 中的步驟取得權杖，並提供下列資訊：

*   OAuth 資源 uri： `https://hib.azurehdinsight.net` 
* AppId：7865c1d2-f040-46cc-875f-831a1ef6a28a
*   許可權： (名稱： Cluster. ReadWrite，id： 8f89faa0-ffef-4007-974d-4989b39ad77d) 

獲取 OAuth 權杖之後，您可以在對叢集閘道的 HTTP 要求的授權標頭中使用該權杖， (例如 <clustername> -int.azurehdinsight.net) 。 例如，livy API 的範例捲曲命令可能看起來像這樣：
    
```bash
curl -k -v -H "Authorization: Bearer Access_TOKEN" -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://<clustername>-int.azurehdinsight.net/livy/batches" -H "X-Requested-By:<username@domain.com>"
``` 

## <a name="next-steps"></a>接下來的步驟

* [使用 Azure Active Directory Domain Services 設定具有企業安全性套件的 HDInsight 叢集](apache-domain-joined-configure-using-azure-adds.md)
* [將 Azure Active Directory 使用者同步至 HDInsight 叢集](../hdinsight-sync-aad-users-to-cluster.md)
* [監視叢集效能](../hdinsight-key-scenarios-to-monitor.md)
