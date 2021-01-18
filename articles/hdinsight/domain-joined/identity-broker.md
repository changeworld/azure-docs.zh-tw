---
title: 'Azure HDInsight 識別碼代理程式 (HIB) '
description: 瞭解 Azure HDInsight 識別碼代理程式，以簡化已加入網域之 Apache Hadoop 叢集的驗證。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: b8dfe9a23e5c6697323142212156006cb65d2f9b
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2021
ms.locfileid: "98556523"
---
# <a name="azure-hdinsight-id-broker-hib"></a>Azure HDInsight 識別碼代理程式 (HIB) 

本文說明如何設定和使用 Azure HDInsight 識別碼訊息代理程式功能。 您可以使用這項功能來取得 Apache Ambari 的新式 OAuth 驗證，同時強制執行多重要素驗證，而不需要 Azure Active Directory Domain Services (Azure AD DS) 中的舊版密碼雜湊。

## <a name="overview"></a>概觀

在下列案例中，HDInsight 識別碼代理程式可簡化複雜的驗證：

* 您的組織會依賴同盟來驗證使用者，以存取雲端資源。 在過去，若要使用 HDInsight 企業安全性套件叢集，您必須從內部部署環境啟用密碼雜湊同步處理，以 Azure Active Directory (Azure AD) 。 對於某些組織而言，這項需求可能很困難或不適當。
* 您的組織想要對 Apache Ambari 和其他叢集資源的 web 型或 HTTP 型存取強制執行多重要素驗證。

HDInsight 識別碼代理人提供驗證基礎結構，可讓您從 OAuth (新式) 至 Kerberos (舊版) ，而不需要將密碼雜湊同步處理至 Azure AD DS。 此基礎結構是由在 Windows Server 虛擬機器上執行的元件所組成， (VM) 已啟用 HDInsight 識別碼代理程式節點，以及叢集閘道節點。

根據您組織的需求，使用下表來決定最佳的驗證選項。

|驗證選項 |HDInsight 設定 | 要考慮的因素 |
|---|---|---|
| 完全 OAuth | 企業安全性套件 + HDInsight 識別碼代理程式 | 最安全的選項。 支援 (多重要素驗證。 *不* 需要 ) 傳遞雜湊同步處理。 在 Azure AD DS 中沒有密碼雜湊的內部部署帳戶沒有 ssh/kinit/keytab 存取權。 僅限雲端的帳戶仍可以 ssh/kinit/keytab。 透過 OAuth 存取 Ambari 的 Web 型存取。 需要更新繼承應用程式 (例如，JDBC/ODBC) 支援 OAuth。|
| OAuth + 基本驗證 | 企業安全性套件 + HDInsight 識別碼代理程式 | 透過 OAuth 存取 Ambari 的 Web 型存取。 繼承應用程式會繼續使用基本驗證。必須停用多重要素驗證，才能進行基本驗證存取。 *不* 需要傳遞雜湊同步處理。 在 Azure AD DS 中沒有密碼雜湊的內部部署帳戶沒有 ssh/kinit/keytab 存取權。 僅限雲端的帳戶仍可以 ssh/kinit。 |
| 完整的基本驗證 | 企業安全性套件 | 最類似內部部署的部署。 需要 Azure AD DS 的密碼雜湊同步處理。 內部部署帳戶可以使用 ssh/kinit 或使用 keytab。 如果支援儲存體 Azure Data Lake Storage Gen2，則必須停用多重要素驗證。 |

下圖顯示啟用 HDInsight 識別碼訊息代理程式之後，所有使用者的新式 OAuth 驗證流程，包括同盟使用者：

:::image type="content" source="media/identity-broker/identity-broker-architecture.png" alt-text="顯示 HDInsight 識別碼 Broker 驗證流程的圖表。":::

在此圖中，用戶端 (也就是瀏覽器或應用程式) 必須先取得 OAuth 權杖。 然後，它會將權杖顯示在 HTTP 要求中的閘道。 如果您已經登入其他 Azure 服務（例如 Azure 入口網站），您可以使用單一登入體驗來登入您的 HDInsight 叢集。

還是有許多繼承應用程式只支援基本驗證 (也就是使用者名稱和密碼) 。 在這些情況下，您仍然可以使用 HTTP 基本驗證來連接到叢集閘道。 在此設定中，您必須確保從閘道節點到 Active Directory 同盟服務 (AD FS) 端點的網路連線，以確保可直接從閘道節點看見。

下圖顯示同盟使用者的基本驗證流程。 首先，閘道會嘗試使用 [ROPC 流程](../../active-directory/develop/v2-oauth-ropc.md)來完成驗證。 如果沒有任何密碼雜湊同步處理至 Azure AD，則會切換回以探索 AD FS 端點，並藉由存取 AD FS 端點來完成驗證。

:::image type="content" source="media/identity-broker/basic-authentication.png" alt-text="顯示基本驗證架構的圖表。":::


## <a name="enable-hdinsight-id-broker"></a>啟用 HDInsight 識別碼代理程式

若要建立已啟用 HDInsight 識別碼 Broker 的企業安全性套件叢集：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 遵循企業安全性套件叢集的基本建立步驟。 如需詳細資訊，請參閱 [使用企業安全性套件建立 HDInsight](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)叢集。
1. 選取 [ **啟用 HDINSIGHT 識別碼代理** 程式]。

HDInsight 識別碼訊息代理程式功能會將一個額外的 VM 新增至叢集中。 此 VM 是 HDInsight 識別碼代理程式節點，它包含支援驗證的伺服器元件。 HDInsight 識別碼代理人節點已加入網域至 Azure AD DS 網域。

![顯示啟用 HDInsight 識別碼訊息代理程式之選項的圖表。](./media/identity-broker/identity-broker-enable.png)

### <a name="use-azure-resource-manager-templates"></a>使用 Azure 資源管理員範本

如果您將使用下列屬性呼叫的新角色新增 `idbrokernode` 至範本的計算設定檔，將會建立叢集，並啟用 HDINSIGHT 識別碼代理人節點：

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
            "targetInstanceCount": 2,
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

若要查看 ARM 範本的完整範例，請參閱 [此處](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template)發佈的範本。


## <a name="tool-integration"></a>工具整合

HDInsight 工具會進行更新，以原生方式支援 OAuth。 使用這些工具進行叢集的新式 OAuth 型存取。 HDInsight [IntelliJ 外掛程式](../spark/apache-spark-intellij-tool-plugin.md#integrate-with-hdinsight-identity-broker-hib) 可用於以 JAVA 為基礎的應用程式，例如 Scala。 [適用于 Visual Studio Code 的 Spark 和 Hive 工具](../hdinsight-for-vscode.md) 可用於 PySpark 和 hive 工作。 這些工具支援批次和互動式作業。

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Azure AD DS 中沒有密碼雜湊的 SSH 存取

|SSH 選項 |要考慮的因素 |
|---|---|
| 本機 VM 帳戶 (例如，sshuser)  | 您已在建立叢集時提供此帳戶。 此帳戶沒有 Kerberos 驗證。 |
| 僅限雲端的帳戶 (例如 alice@contoso.onmicrosoft.com)  | 密碼雜湊可在 Azure AD DS 中使用。 可以透過 SSH Kerberos 進行 kerberos 驗證。 |
| 內部部署帳戶 (例如， alice@contoso.com)  | 只有當密碼雜湊可在 Azure AD DS 中使用時，才可以使用 SSH Kerberos 驗證。 否則，此使用者無法透過 SSH 連線到叢集。 |

若要透過 SSH 連線到已加入網域的 VM 或執行 `kinit` 命令，您必須提供密碼。 SSH Kerberos 驗證需要 Azure AD DS 中可用的雜湊。 如果您只想要針對系統管理案例使用 SSH，您可以建立一個僅限雲端的帳戶，並使用它來透過 SSH 連線到叢集。 其他內部部署使用者仍然可以使用 Ambari 或 HDInsight 工具或 HTTP 基本驗證，而不需要在 Azure AD DS 中使用密碼雜湊。

如果您的組織未將密碼雜湊同步處理至 Azure AD DS，最佳作法是在 Azure AD 中建立一個僅限雲端的使用者。 然後，在建立叢集時將它指派為叢集系統管理員，並將其用於系統管理用途。 您可以使用它來透過 SSH 取得 Vm 的根目錄存取權。

若要對驗證問題進行疑難排解，請參閱 [本指南](./domain-joined-authentication-issues.md)。

## <a name="clients-using-oauth-to-connect-to-an-hdinsight-gateway-with-hdinsight-id-broker"></a>使用 OAuth 連線至 HDInsight 閘道的用戶端與 HDInsight 識別碼代理程式

在 HDInsight 識別碼代理程式安裝中，可更新連線至閘道的自訂應用程式和用戶端，以先取得必要的 OAuth 權杖。 依照 [本檔](../../storage/common/storage-auth-aad-app.md) 中的步驟取得權杖，並提供下列資訊：

*    OAuth 資源 uri： `https://hib.azurehdinsight.net` 
*   AppId：7865c1d2-f040-46cc-875f-831a1ef6a28a
*    許可權： (名稱： Cluster. ReadWrite，id： 8f89faa0-ffef-4007-974d-4989b39ad77d) 

取得 OAuth 權杖之後，請將它用於叢集閘道之 HTTP 要求的授權標頭 (例如 HTTPs:// <clustername> -int.azurehdinsight.net) 。 Apache Livy API 的範例捲曲命令可能看起來像這個範例：
    
```bash
curl -k -v -H "Authorization: Bearer Access_TOKEN" -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://<clustername>-int.azurehdinsight.net/livy/batches" -H "X-Requested-By:<username@domain.com>"
``` 

若要使用 Beeline 和 Livy，您也可以遵循 [此處](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/HIB/HIBSamples) 提供的範例程式碼，將用戶端設定為使用 OAuth 並連接到叢集。

## <a name="faq"></a>常見問題集
### <a name="what-app-is-created-by-hdinsight-in-aad"></a>HDInsight 在 AAD 中所建立的應用程式是什麼？
針對每個叢集，會在 AAD 中註冊協力廠商應用程式，並以叢集 uri 作為 identifierUri (例如 `https://clustername.azurehdinsight.net`) 。

### <a name="why-are-users-prompted-for-consent-before-using-hib-enabled-clusters"></a>為什麼使用者在使用啟用 HIB 的叢集之前會提示您同意？
在 AAD 中，所有協力廠商應用程式都需要同意，才能驗證使用者或存取資料。

### <a name="can-the-consent-be-approved-programatically"></a>同意會以程式設計方式核准嗎？
Microsoft Graph api 可讓您將同意自動化，請參閱 [api 檔](/graph/api/resources/oauth2permissiongrant) ，以將同意自動化的順序如下：

* 註冊應用程式，並將應用程式的擁有權限授與應用程式，以存取 Microsoft Graph
* 建立叢集之後，請根據識別碼 uri 查詢叢集應用程式
* 註冊應用程式的同意

刪除叢集時，HDInsight 會刪除應用程式，且不需要清除任何同意。

 


## <a name="next-steps"></a>後續步驟

* [使用 Azure Active Directory Domain Services 設定具有企業安全性套件的 HDInsight 叢集](apache-domain-joined-configure-using-azure-adds.md)
* [將 Azure Active Directory 使用者同步至 HDInsight 叢集](../hdinsight-sync-aad-users-to-cluster.md)
* [監視叢集效能](../hdinsight-key-scenarios-to-monitor.md)