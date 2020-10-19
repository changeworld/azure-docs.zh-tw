---
title: 適用於 MySQL 的 Azure 資料庫的憑證輪替
description: 瞭解即將變更的根憑證變更將會影響適用於 MySQL 的 Azure 資料庫
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: cc32a67ab681341fd8320b9445f4e00013f2aa51
ms.sourcegitcommit: 94ca9e89501e65f4dcccc3789249357c7d5e27e5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92170273"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mysql"></a>瞭解適用於 MySQL 的 Azure 資料庫的根 CA 變更變更

適用於 MySQL 的 Azure 資料庫將會變更以 SSL 啟用之用戶端應用程式/驅動程式的根憑證，用來 [連接到資料庫伺服器](concepts-connectivity-architecture.md)。 目前可用的根憑證已設定為在2021年2月15日到期， (02/15/2021) 作為標準維護和安全性最佳作法的一部分。 本文提供更多有關即將進行的變更、將受影響的資源，以及確保您的應用程式維持資料庫伺服器連線所需的步驟等詳細資料。

>[!NOTE]
> 根據客戶的意見反應，我們已從2020年10月15日到2021年10月15日之前，為現有的巴爾的摩根 CA 延伸根憑證取代。 我們希望此延伸模組可提供足夠的前置時間，讓使用者在受到影響時，執行用戶端變更。

## <a name="what-update-is-going-to-happen"></a>即將發生什麼更新？

在某些情況下，應用程式會使用由信任的憑證授權單位單位所產生的本機憑證檔案 (CA) 憑證檔案，以安全地連接。 目前的客戶只能使用預先定義的憑證來連線到適用於 MySQL 的 Azure 資料庫伺服器（位於 [此處](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)）。 不過，[憑證授權單位單位 (ca) 瀏覽器論壇](https://cabforum.org/)   最近發行的報表，其中有多個 ca 廠商發行的憑證不符合規範。

根據產業的合規性需求，CA 廠商會開始撤銷不符合規範之 Ca 的 CA 憑證，要求伺服器使用符合規範的 Ca 所發行的憑證，並由這些符合規範的 Ca 的 CA 憑證簽署。 由於適用於 MySQL 的 Azure 資料庫目前會使用其中一個不符合規範的憑證（用戶端應用程式會使用這些憑證來驗證其 SSL 連線），因此我們需要確保採取適當的動作，如下所述 () 將 MySQL 伺服器的潛在影響降到最低。

新憑證將在2021年2月15日 (02/15/2021) 開始使用。從 MySQL 用戶端連線時，如果您使用 CA 驗證或伺服器憑證的完整驗證 (sslmode = verify-CA 或 sslmode = verify-full) ，您必須在2021年2月15日 (03/15/2021) 之前更新您的應用程式設定。

## <a name="how-do-i-know-if-my-database-is-going-to-be-affected"></a>如何? 知道我的資料庫是否會受到影響？

使用 SSL/TLS 並確認根憑證的所有應用程式都需要更新根憑證。 您可以藉由檢查您的連接字串來識別您的連接是否驗證根憑證。
-   如果您的連接字串包含 `sslmode=verify-ca` 或 `sslmode=verify-identity` ，則需要更新憑證。
-   如果您的連接字串包含 `sslmode=disable` 、 `sslmode=allow` 、 `sslmode=prefer` 或 `sslmode=require` ，就不需要更新憑證。 
-  如果使用 JAVA 連接器，而您的連接字串包含 useSSL = false 或 requireSSL = false，您就不需要更新憑證。
-   如果您的連接字串未指定 sslmode，您就不需要更新憑證。

如果您使用的用戶端會將連接字串抽象化，請參閱用戶端的檔，以瞭解它是否會驗證憑證。
若要瞭解適用於 MySQL 的 Azure 資料庫 sslmode 請參閱 [SSL 模式描述](concepts-ssl-connection-security.md#ssl-default-settings)。

若要避免應用程式的可用性因為未預期地撤銷憑證而中斷，或要更新已撤銷的憑證，請參閱「 [**我需要怎麼做才能維持連線能力**](concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity) 」一節。

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>需要怎麼做才能維持連線能力

若要避免應用程式的可用性因為未預期地撤銷憑證而中斷，或要更新已撤銷的憑證，請遵循下列步驟。 其概念是建立新的 *pem* 檔案，其中結合了目前的憑證和新憑證，而在 SSL 憑證驗證期間，將會使用允許的值。 請參閱下列步驟：

*   從下列連結下載 Baltimorecybertrustroot.crt & DigiCertGlobalRootG2 根 CA：
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   同時包含 **baltimorecybertrustroot.crt** 和 **DigiCertGlobalRootG2** 憑證來產生合併的 CA 憑證存儲。
    *   針對 JAVA (MySQL Connector/J) 使用者，請執行：

          ```azurecli-interactive
          keytool -importcert -alias MySQLServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
          ```

          ```azurecli-interactive
          keytool -importcert -alias MySQLServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
          ```

          然後，將原始金鑰儲存區檔案取代為新產生的金鑰儲存區檔案：
        *   System.string ( "javax.xml.transform.dom.domresult" trustStore "，" path_to_truststore_file ") ; 
        *   System.string ( "javax.xml.transform.dom.domresult" trustStorePassword "，" password ") ;

    *   針對 .NET (MySQL Connector/NET，MySQLConnector) 使用者，請確定 Windows 憑證存放區、受信任的根憑證授權單位都有 **baltimorecybertrustroot.crt** 和 **DigiCertGlobalRootG2** 。 如果有任何憑證不存在，請匯入遺失的憑證。

        ![適用於 MySQL 的 Azure 資料庫 .net 憑證](media/overview/netconnecter-cert.png)

    *   針對使用 SSL_CERT_DIR 的 Linux 上的 .NET 使用者，請確定 SSL_CERT_DIR 所指出的目錄中都有 **baltimorecybertrustroot.crt** 和 **DigiCertGlobalRootG2** 。 如果有任何憑證不存在，請建立遺失的憑證檔案。

    *   針對其他 (MySQL 用戶端/MySQL 工作臺/C/c + +/Go/Python/Ruby/PHP/NodeJS/Perl/Swift) 使用者，您可以合併兩個 CA 憑證檔案，如下所示：以下格式：</b>

        </br>-----開始憑證-----
 </br> (根 CA1： Baltimorecybertrustroot.crt .crt) 
 </br>-----終端憑證-----
 </br>-----開始憑證-----
 </br> (根 CA2： DigiCertGlobalRootG2 .crt) 
 </br>-----終端憑證-----

*   將原始的根 CA pem 檔案取代為合併的根 CA 檔案，然後重新開機您的應用程式/用戶端。
*   未來，在伺服器端部署新的憑證之後，您可以將 CA pem 檔案變更為 DigiCertGlobalRootG2。

## <a name="what-can-be-the-impact-of-not-updating-the-certificate"></a>不更新憑證的影響為何？
如果您使用此處所述的適用於 MySQL 的 Azure 資料庫發行的憑證，則您的應用程式可用性可能會中斷，因為資料庫將無法連線。 視您的應用程式而定，您可能會收到各種不同的錯誤訊息，包括但不限於：
*   不正確憑證/撤銷憑證
*   連線逾時

> [!NOTE]
> 在進行 cert 變更之前，請勿捨棄或修改 **巴爾的摩 certificate** 。 變更完成之後，我們會傳送通訊，在這之後，就可以安全地捨棄巴爾的摩憑證。 

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="1-if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. 如果我未使用 SSL/TLS，是否仍需要更新根 CA？
如果您不是使用 SSL/TLS，則不需要採取任何動作。 

### <a name="2-if-i-am-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. 如果我使用 SSL/TLS，是否需要重新開機我的資料庫伺服器來更新根 CA？
否，您不需要重新開機資料庫伺服器，就能開始使用新的憑證。 這個根憑證是用戶端的變更，而連入的用戶端連線必須使用新的憑證，以確保它們可以連接到資料庫伺服器。

### <a name="3-what-will-happen-if-i-do-not-update-the-root-certificate-before-february-15-2021-02152021"></a>3. 如果我未在2021年2月15日之前更新根憑證， (02/15/2021) 會發生什麼事？
如果您在) 02/15/2021 (2021 年2月15日之前未更新根憑證，則透過 SSL/TLS 連接的應用程式以及根憑證的驗證，將無法與 MySQL 資料庫伺服器通訊，而且應用程式將會遇到 MySQL 資料庫伺服器的連線問題。

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-mysql"></a>4. 使用 App Service 搭配適用於 MySQL 的 Azure 資料庫會有什麼影響？
針對 Azure 應用程式服務，連接到適用於 MySQL 的 Azure 資料庫，我們可以有兩個可能的案例，而這取決於您在應用程式中使用 SSL 的方式。
*   此新憑證已新增至平台層級的 App Service。 如果您在應用程式中使用包含在 App Service 平臺上的 SSL 憑證，則不需要採取任何動作。
*   如果您在程式碼中明確包含 SSL 憑證檔案的路徑，則需要下載新的憑證，並更新程式碼以使用新的憑證。此案例的一個良好範例是當您在 App Service 中使用自訂容器，如[App Service 檔](/app-service/tutorial-multi-container-app#configure-database-variables-in-wordpress.md)中所共用

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-mysql"></a>5. 使用 Azure Kubernetes Services (AKS) 搭配適用於 MySQL 的 Azure 資料庫時的影響為何？
如果您嘗試使用 Azure Kubernetes Services (AKS) 來連線至適用於 MySQL 的 Azure 資料庫，其類似于從專用客戶主機環境進行存取。 請參閱 [此處](../aks/ingress-own-tls.md)的步驟。

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-mysql"></a>6. 使用 Azure Data Factory 連接到適用於 MySQL 的 Azure 資料庫會有何影響？
針對使用 Azure Integration Runtime 的連接器，連接器會在 Azure 裝載的環境中，利用 Windows 憑證存放區中的憑證。 這些憑證已經與新套用的憑證相容，因此不需要採取任何動作。

針對使用自我裝載 Integration Runtime 的連接器，您可以在連接字串中明確包含 SSL 憑證檔案的路徑，您必須下載 [新的憑證](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) ，並更新連接字串以使用它。

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7. 我是否需要為此變更規劃資料庫伺服器維護停機時間？
否。 因為這裡的變更只在用戶端連接到資料庫伺服器，所以資料庫伺服器不需要維護停機時間來進行這項變更。

### <a name="8--what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-february-15-2021-02152021"></a>8. 如果我無法在2021年2月15日之前，于年2月15日之前變更排程的停機時間， (02/15/2021) ？
由於用來連接到伺服器的用戶端必須更新憑證資訊，如 [此處](./concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity)的修正一節所述，在此情況下，伺服器不需要停機。

### <a name="9-if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>9. 如果我在2021年2月15日之後建立新的伺服器 (02/15/2021) 會受到影響嗎？
針對在2021年2月15日之後建立的伺服器 (02/15/2021) ，您可以使用新發行的憑證，讓您的應用程式使用 SSL 進行連接。

### <a name="10-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>10. Microsoft 更新其憑證的頻率，或到期原則是什麼？
適用於 MySQL 的 Azure 資料庫所使用的憑證是由受信任的憑證授權單位單位 (CA) 提供。 因此，這些憑證在適用於 MySQL 的 Azure 資料庫上的支援系結至 CA 支援這些憑證。 不過，在這種情況下，這些預先定義的憑證可能會有未預期的錯誤，這些都必須儘早修正。

### <a name="11-if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-source-server-or-the-read-replicas"></a>11. 如果我使用讀取複本，我只需要在來源伺服器或讀取複本上執行此更新嗎？
由於這項更新是用戶端的變更，如果用戶端用來從複本伺服器讀取資料，您也必須套用這些用戶端的變更。

### <a name="12-if-i-am-using-data-in-replication-do-i-need-to-perform-any-action"></a>12. 如果我使用資料輸入複寫，是否需要執行任何動作？
如果您使用 [資料傳入](concepts-data-in-replication.md) 複寫來連接適用於 MySQL 的 Azure 資料庫，則需要考慮兩件事：
*   如果資料複寫是來自 (內部內部部署或 Azure 虛擬機器) 到適用於 MySQL 的 Azure 資料庫的虛擬機器，則您必須檢查是否使用 SSL 來建立複本。 執行 **顯示從屬狀態** 並檢查下列設定。  

    ```azurecli-interactive
    Master_SSL_Allowed            : Yes
    Master_SSL_CA_File            : ~\azure_mysqlservice.pem
    Master_SSL_CA_Path            :
    Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
    Master_SSL_Cipher             :
    Master_SSL_Key                : ~\azure_mysqlclient_key.pem
    ```

    如果您看到憑證是針對 CA_file 所提供，SSL_Cert 和 SSL_Key，則必須新增 [新憑證](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)來更新檔案。

*   如果資料複寫在兩個適用於 MySQL 的 Azure 資料庫之間，則您必須執行 **呼叫 mysql.az_replication_change_master** 來重設複本，並將新的雙重根憑證提供為最後一個參數 [master_ssl_ca](howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication)

### <a name="13-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>13. 我們是否有伺服器端查詢，以確認是否正在使用 SSL？
若要確認您是否使用 SSL 連線來連線到伺服器，請參閱 [ssl 驗證](howto-configure-ssl.md#step-4-verify-the-ssl-connection)。

### <a name="14-is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>14. 如果我的憑證檔案中已經有 DigiCertGlobalRootG2，是否需要採取動作？
否。 如果您的憑證檔案已經有 **DigiCertGlobalRootG2**，就不需要採取任何動作。

### <a name="15-what-if-i-have-further-questions"></a>15. 如果我有其他問題，該怎麼辦？
如果您有任何疑問，請從 [Microsoft Q&中的](mailto:AzureDatabaseforMySQL@service.microsoft.com)「社區專家」獲得解答。 如果您有支援方案，且需要技術協助，請 [與我們聯絡](mailto:AzureDatabaseforMySQL@service.microsoft.com)。
