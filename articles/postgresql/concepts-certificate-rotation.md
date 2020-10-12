---
title: 適用於 PostgreSQL 的 Azure 資料庫單一伺服器的憑證輪替
description: 瞭解即將變更的根憑證變更，這些變更會影響單一伺服器適用於 PostgreSQL 的 Azure 資料庫
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 976b423822fa667df713382b34d7208cb0e3b002
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540654"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-postgresql-single-server"></a>瞭解適用於 PostgreSQL 的 Azure 資料庫單一伺服器的根 CA 變更變更

適用於 PostgreSQL 的 Azure 資料庫將會變更以 SSL 啟用之用戶端應用程式/驅動程式的根憑證，用來 [連接到資料庫伺服器](concepts-connectivity-architecture.md)。 目前可用的根憑證已設定為在2020年10月26日到期， (10/26/2020) 作為標準維護和安全性最佳作法的一部分。 本文提供更多有關即將進行的變更、將受影響的資源，以及確保您的應用程式維持資料庫伺服器連線所需的步驟等詳細資料。

## <a name="what-update-is-going-to-happen"></a>即將發生什麼更新？

在某些情況下，應用程式會使用由信任的憑證授權單位單位所產生的本機憑證檔案 (CA) 憑證檔案，以安全地連接。 目前的客戶只能使用預先定義的憑證來連線到適用於 PostgreSQL 的 Azure 資料庫伺服器（位於 [此處](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)）。 不過， [憑證授權單位單位 (ca) 瀏覽器論壇](https://cabforum.org/) 最近發行的報表，其中有多個 ca 廠商發行的憑證不符合規範。

根據產業的合規性需求，CA 廠商會開始撤銷不符合規範之 Ca 的 CA 憑證，要求伺服器使用符合規範的 Ca 所發行的憑證，並由這些符合規範的 Ca 的 CA 憑證簽署。 由於適用於 PostgreSQL 的 Azure 資料庫目前會使用其中一個不符合規範的憑證（用戶端應用程式會使用這些憑證來驗證其 SSL 連線），因此我們需要確保 (採取適當的動作，如下所述) 將于 postgresql 伺服器的潛在影響降到最低。

新憑證將于2020年10月26日 (10/26/2020) 開始使用。 從于 postgresql 用戶端連線時，如果您使用 CA 驗證或伺服器憑證的完整驗證 (sslmode = 驗證-CA 或 sslmode = 驗證-完整) ，您必須在2020年10月26日 (10/26/2020) 之前更新您的應用程式設定。

## <a name="how-do-i-know-if-my-database-is-going-to-be-affected"></a>如何? 知道我的資料庫是否會受到影響？

使用 SSL/TLS 並確認根憑證的所有應用程式都需要更新根憑證。 您可以藉由檢查您的連接字串來識別您的連接是否驗證根憑證。
-   如果您的連接字串包含 `sslmode=verify-ca` 或 `sslmode=verify-full` ，則需要更新憑證。
-   如果您的連接字串包含 `sslmode=disable` 、 `sslmode=allow` 、 `sslmode=prefer` 或 `sslmode=require` ，就不需要更新憑證。 
-   如果您的連接字串未指定 sslmode，您就不需要更新憑證。

如果您使用的用戶端會將連接字串抽象化，請參閱用戶端的檔，以瞭解它是否會驗證憑證。

若要瞭解于 postgresql sslmode，請參閱于 postgresql 檔中的 [SSL 模式說明](https://www.postgresql.org/docs/11/libpq-ssl.html#ssl-mode-descriptions) 。

若要避免應用程式的可用性因為未預期地撤銷憑證而中斷，或要更新已撤銷的憑證，請參閱「 [**我需要怎麼做才能維持連線能力**](concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity) 」一節。

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>需要怎麼做才能維持連線能力

若要避免應用程式的可用性因為未預期地撤銷憑證而中斷，或要更新已撤銷的憑證，請遵循下列步驟。 其概念是建立新的 *pem* 檔案，其中結合了目前的憑證和新憑證，而在 SSL 憑證驗證期間，將會使用允許的值。 請參閱下列步驟：

*   從下列連結下載 Baltimorecybertrustroot.crt & DigiCertGlobalRootG2 根 CA：
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   同時包含 **baltimorecybertrustroot.crt** 和 **DigiCertGlobalRootG2** 憑證來產生合併的 CA 憑證存儲。
    *   針對 JAVA (使用 DefaultJAVASSLFactory 于 postgresql JDBC) 使用者，請執行：

          ```azurecli-interactive
          keytool -importcert -alias PostgreSQLServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
          ```

          ```azurecli-interactive
          keytool -importcert -alias PostgreSQLServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
          ```

          然後，將原始金鑰儲存區檔案取代為新產生的金鑰儲存區檔案：
        *   System.string ( "javax.xml.transform.dom.domresult" trustStore "，" path_to_truststore_file ") ; 
        *   System.string ( "javax.xml.transform.dom.domresult" trustStorePassword "，" password ") ;
        
    *   針對在 Windows 上 Npgsql) 使用者的 .NET (，請確定在 Windows 憑證存放區、受信任的根憑證授權單位中，有 **巴爾的摩 CyberTrust Root** 和 **DigiCert Global root G2** 都存在。 如果有任何憑證不存在，請匯入遺失的憑證。

        ![適用於 PostgreSQL 的 Azure 資料庫 .net 憑證](media/overview/netconnecter-cert.png)

    *   若為 .NET (使用 SSL_CERT_DIR 在 Linux 上 Npgsql) 使用者，請確定 **baltimorecybertrustroot.crt** 和 **DigiCertGlobalRootG2** 都存在於 SSL_CERT_DIR 所指示的目錄中。 如果有任何憑證不存在，請建立遺失的憑證檔案。

    *   針對其他于 postgresql 用戶端使用者，您可以合併兩個 CA 憑證檔案，如下格式：

        </br>-----開始憑證-----
 </br> (根 CA1： Baltimorecybertrustroot.crt .crt) 
 </br>-----終端憑證-----
 </br>-----開始憑證-----
 </br> (根 CA2： DigiCertGlobalRootG2 .crt) 
 </br>-----終端憑證-----

*   將原始的根 CA pem 檔案取代為合併的根 CA 檔案，然後重新開機您的應用程式/用戶端。
*   未來，在伺服器端部署新的憑證之後，您可以將 CA pem 檔案變更為 DigiCertGlobalRootG2。

## <a name="what-can-be-the-impact-of-not-updating-the-certificate"></a>不更新憑證的影響為何？
如果您使用巴爾的摩 CyberTrust 根憑證來確認適用於 PostgreSQL 的 Azure 資料庫的 SSL 連線（如這裡所述），則您的應用程式可用性可能會中斷，因為無法連接資料庫。 視您的應用程式而定，您可能會收到各種不同的錯誤訊息，包括但不限於：
*   不正確憑證/撤銷憑證
*   連線逾時

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="1-if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. 如果我未使用 SSL/TLS，是否仍需要更新根 CA？
如果您不是使用 SSL/TLS，則不需要採取任何動作。 

### <a name="2-if-i-am-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. 如果我使用 SSL/TLS，是否需要重新開機我的資料庫伺服器來更新根 CA？
否，您不需要重新開機資料庫伺服器，就能開始使用新的憑證。 這是用戶端的變更，而連入的用戶端連線必須使用新的憑證，以確保它們可以連接到資料庫伺服器。

### <a name="3-what-will-happen-if-i-do-not-update-the-root-certificate-before-october-26-2020-10262020"></a>3. 如果我未在2020年10月26日之前更新根憑證， (10/26/2020) 會發生什麼事？
如果您未在2020年10月26日之前更新根憑證，則透過 SSL/TLS 連線並驗證根憑證的應用程式將無法與于 postgresql 資料庫伺服器通訊，而且應用程式將會遇到于 postgresql 資料庫伺服器的連線問題。

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-postgresql"></a>4. 使用 App Service 搭配適用於 PostgreSQL 的 Azure 資料庫會有什麼影響？
針對 Azure 應用程式服務，連接到適用於 PostgreSQL 的 Azure 資料庫，我們可以有兩個可能的案例，而這取決於您在應用程式中使用 SSL 的方式。
*   此新憑證已新增至平台層級的 App Service。 如果您在應用程式中使用包含在 App Service 平臺上的 SSL 憑證，則不需要採取任何動作。
*   如果您在程式碼中明確包含 SSL 憑證檔案的路徑，則需要下載新的憑證，並更新程式碼以使用新的憑證。

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-postgresql"></a>5. 使用 Azure Kubernetes Services (AKS) 搭配適用於 PostgreSQL 的 Azure 資料庫時的影響為何？
如果您嘗試使用 Azure Kubernetes Services (AKS) 來連線至適用於 PostgreSQL 的 Azure 資料庫，其類似于從專用客戶主機環境進行存取。 請參閱 [此處](../aks/ingress-own-tls.md)的步驟。

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-postgresql"></a>6. 使用 Azure Data Factory 連接到適用於 PostgreSQL 的 Azure 資料庫會有何影響？
針對使用 Azure Integration Runtime 的連接器，連接器會在 Azure 裝載的環境中，利用 Windows 憑證存放區中的憑證。 這些憑證已經與新套用的憑證相容，因此不需要採取任何動作。

針對使用自我裝載 Integration Runtime 的連接器，您可以在連接字串中明確包含 SSL 憑證檔案的路徑，您必須下載 [新的憑證](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) ，並更新連接字串以使用它。

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7. 我是否需要為此變更規劃資料庫伺服器維護停機時間？
否。 因為這裡的變更只在用戶端連接到資料庫伺服器，所以資料庫伺服器不需要維護停機時間來進行這項變更。

### <a name="8--what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-october-26-2020-10262020"></a>8. 如果我無法在2020年10月26日之前取得此變更的排程停機時間， (10/26/2020) ？
由於用來連接到伺服器的用戶端必須更新憑證資訊，如 [此處](./concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity)的修正一節所述，在此情況下，伺服器不需要停機。

### <a name="9-if-i-create-a-new-server-after-october-26-2020-will-i-be-impacted"></a>9. 如果我在2020年10月26日之後建立新的伺服器，將會受到影響嗎？
針對在2020年10月26日之後建立的伺服器 (10/26/2020) ，您可以使用新發行的憑證，讓您的應用程式使用 SSL 進行連接。

### <a name="10-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>10. Microsoft 更新其憑證的頻率，或到期原則是什麼？
適用於 PostgreSQL 的 Azure 資料庫所使用的憑證是由受信任的憑證授權單位單位 (CA) 提供。 因此，這些憑證在適用於 PostgreSQL 的 Azure 資料庫上的支援系結至 CA 支援這些憑證。 不過，在這種情況下，這些預先定義的憑證可能會有未預期的錯誤，這些都必須儘早修正。

### <a name="11-if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-the-primary-server-or-the-read-replicas"></a>11. 如果我使用讀取複本，我只需要在主伺服器或讀取複本上執行此更新嗎？
由於這項更新是用戶端的變更，如果用戶端用來從複本伺服器讀取資料，您也必須套用這些用戶端的變更。 

### <a name="12-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>12. 我們是否有伺服器端查詢，以確認是否正在使用 SSL？
若要確認您是否使用 SSL 連線來連線到伺服器，請參閱 [ssl 驗證](concepts-ssl-connection-security.md#applications-that-require-certificate-verification-for-tls-connectivity)。

### <a name="13-is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>13. 如果我的憑證檔案中已經有 DigiCertGlobalRootG2，是否需要採取動作？
否。 如果您的憑證檔案已經有 **DigiCertGlobalRootG2**，就不需要採取任何動作。

### <a name="14-what-if-i-have-further-questions"></a>14. 如果我有其他問題，該怎麼辦？
如果您有任何疑問，請從 [Microsoft Q&中的](mailto:AzureDatabaseforPostgreSQL@service.microsoft.com)「社區專家」獲得解答。 如果您有支援方案，且需要技術協助，請  [與我們聯絡](mailto:AzureDatabaseforPostgreSQL@service.microsoft.com)