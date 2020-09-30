---
title: 使用識別碼代理程式 (預覽) 進行認證管理-Azure HDInsight
description: 深入瞭解 HDInsight 識別碼代理人，以簡化已加入網域之 Apache Hadoop 叢集的驗證。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 09/23/2020
ms.openlocfilehash: 8f1e0a6aecc9702552a3dd66acc8dc7eb5bf1d85
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91529916"
---
# <a name="azure-hdinsight-id-broker-preview"></a>Azure HDInsight ID Broker (preview) 

本文說明如何在 Azure HDInsight 中設定和使用 HDInsight 識別碼代理程式 (HIB) 功能。 您可以使用這項功能來取得 Apache Ambari 的新式 OAuth 驗證，同時讓 Multi-Factor Authentication (MFA) 強制，而不需要 Azure Active Directory Domain Services (的) 中的舊版密碼雜湊。

## <a name="overview"></a>概觀

HIB 可簡化下列案例中的複雜驗證：

* 您的組織會依賴同盟來驗證使用者，以存取雲端資源。 在過去，若要使用 HDInsight 企業安全性套件 (ESP) 叢集，您必須從內部部署環境啟用密碼雜湊同步處理，以 Azure Active Directory (Azure AD) 。 對於某些組織而言，這項需求可能很困難或不適當。

* 您的組織想要針對以 web/HTTP 為基礎的 Apache Ambari 和其他叢集資源存取強制執行 MFA。

HIB 提供的驗證基礎結構可讓您從 OAuth (新式) 至 Kerberos (舊版的) ，而不需要將密碼雜湊同步至 AAD DS。 此基礎結構包含在 Windows Server VM 上執行的元件， (識別碼代理程式節點) ，以及叢集閘道節點。

下圖顯示啟用識別碼訊息代理程式之後，所有使用者的新式 OAuth 型驗證流程，包括同盟使用者：

:::image type="content" source="media/identity-broker/identity-broker-architecture.png" alt-text="具有識別碼 Broker 的驗證流程":::

在此圖中，用戶端 (也就是瀏覽器或應用程式) 必須先取得 OAuth 權杖，然後在 HTTP 要求中向閘道出示權杖。 如果您已經登入其他 Azure 服務（例如 Azure 入口網站），您可以使用單一登入 (SSO) 體驗登入 HDInsight 叢集。

還是有許多繼承應用程式只支援基本驗證 (也就是使用者名稱/密碼) 。 在這些情況下，您仍然可以使用 HTTP 基本驗證來連接到叢集閘道。 在此設定中，您必須確保從閘道節點到同盟端點 (ADFS 端點) 的網路連線，以確保可直接從閘道節點看見。

:::image type="content" source="media/identity-broker/basic-authentication.png" alt-text="具有識別碼 Broker 的驗證流程":::

根據您的組織需求，使用下表來決定最佳的驗證選項：

|驗證選項 |HDInsight 設定 | 要考慮的因素 |
|---|---|---|
| 完全 OAuth | ESP + HIB | 1. 支援 MFA (的最安全選項) 2。    不需要傳遞雜湊同步處理。 3.  在 AAD DS 中沒有密碼雜湊的內部內部部署帳戶沒有 ssh/kinit/keytab 存取權。 4.   僅限雲端的帳戶仍可以 ssh/kinit/keytab。 5. 透過 Oauth 6 對 Ambari 進行 Web 存取。  需要更新 (JDBC/ODBC 等 ) 的繼承應用程式，才能支援 OAuth。|
| OAuth + 基本驗證 | ESP + HIB | 1. 透過 Oauth 2 存取 Ambari 的 Web 型存取。 繼承應用程式會繼續使用基本驗證。3。 必須停用 MFA，才能進行基本驗證存取。 4. 不需要傳遞雜湊同步處理。 5. 在 AAD DS 中沒有密碼雜湊的內部內部部署帳戶沒有 ssh/kinit/keytab 存取權。 6. 僅限雲端的帳戶仍可以 ssh/kinit。 |
| 完整的基本驗證 | ESP | 1. 最類似于內部內部部署的設置。 2. 需要密碼雜湊同步處理至 AAD-DS。 3. 內部部署帳戶可以使用 ssh/kinit 或使用 keytab。 4. 如果支援儲存體 ADLS Gen2，則必須停用 MFA |


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

HDIsngith 工具會進行更新，以原生方式支援 OAuth。 我們強烈建議使用這些工具來進行叢集的新式 OAuth 型存取。 HDInsight [IntelliJ 外掛程式](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) 可用於以 JAVA 為基礎的應用程式，例如 Scala。 [適用于 VS Code 的 Spark & Hive 工具](https://docs.microsoft.com/azure/hdinsight/hdinsight-for-vscode) 可用於 PySpark 和 hive 工作。 它們支援批次和互動式作業。

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Azure AD DS 中沒有密碼雜湊的 SSH 存取

|SSH 選項 |要考慮的因素 |
|---|---|
| 本機 VM 帳戶 (例如 sshuser)  | 1. 您是在建立叢集時提供此帳戶。 2.  此帳戶沒有 kerberos authication |
| 僅限雲端帳戶 (例如 alice@contoso.onmicrosoft.com)  | 1. AAD DS 2 中提供密碼雜湊。 透過 SSH kerberos 可以進行 kerberos 驗證 |
| 內部部署帳戶 (例如 alice@contoso.com)  | 1. 只有在 AAD DS 中可以使用密碼雜湊時，才可能使用 SSH Kerberos 驗證，否則此使用者無法透過 SSH 連線到叢集 |

若要透過 SSH 連線到已加入網域的 VM，或要執行 `kinit` 命令，您必須提供密碼。 SSH Kerberos 驗證需要雜湊才能在 AAD DS 中使用。 如果您只想要針對系統管理案例使用 SSH，您可以建立一個僅限雲端的帳戶，並使用該帳戶透過 SSH 連線到叢集。 其他內部內部部署使用者仍可使用 Ambari 或 HDInsight 工具或 HTTP 基本驗證，而不需要在 AAD DS 中使用密碼雜湊。

如果您的組織不會將密碼雜湊同步處理至 AAD，最佳作法是在 Azure AD 中建立一個僅限雲端的使用者，並在建立叢集時將其指派為叢集系統管理員，並使用該使用者進行系統管理，包括透過 SSH 取得 Vm 的根目錄存取權。

若要對驗證問題進行疑難排解，請參閱本 [指南](https://docs.microsoft.com/azure/hdinsight/domain-joined/domain-joined-authentication-issues)。

## <a name="clients-using-oauth-to-connect-to-hdinsight-gateway-with-hib"></a>使用 OAuth 連線至 HDInsight 閘道與 HIB 的用戶端

在 HIB 設定中，您可以更新連接到閘道的自訂應用程式和用戶端，以先取得必要的 OAuth 權杖。 您可以依照這 [份檔](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app) 中的步驟取得權杖，並提供下列資訊：

*   OAuth 資源 uri： `https://hib.azurehdinsight.net` 
*   AppId：7865c1d2-f040-46cc-875f-831a1ef6a28a
*   許可權： (名稱： Cluster. ReadWrite，id： 8f89faa0-ffef-4007-974d-4989b39ad77d) 

取得 OAuth 權杖之後，您可以在 (叢集閘道的 HTTP 要求的授權標頭中使用該權杖，例如 HTTPs:// <clustername> -int.azurehdinsight.net) 。 例如，Apache Livy API 的範例捲曲命令看起來可能像這樣：
    
```bash
curl -k -v -H "Authorization: Bearer Access_TOKEN" -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://<clustername>-int.azurehdinsight.net/livy/batches" -H "X-Requested-By:<username@domain.com>"
``` 

## <a name="next-steps"></a>後續步驟

* [使用 Azure Active Directory Domain Services 設定具有企業安全性套件的 HDInsight 叢集](apache-domain-joined-configure-using-azure-adds.md)
* [將 Azure Active Directory 使用者同步至 HDInsight 叢集](../hdinsight-sync-aad-users-to-cluster.md)
* [監視叢集效能](../hdinsight-key-scenarios-to-monitor.md)
