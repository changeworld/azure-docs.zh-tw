---
title: 適用於 MariaDB 的 Azure 資料庫的憑證輪替
description: 瞭解即將變更的根憑證變更將會影響適用於 MariaDB 的 Azure 資料庫
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 3c91da6a9bfc7bfa23255dbc1c0c76d2f59818f1
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90530551"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mariadb"></a>瞭解適用於 MariaDB 的 Azure 資料庫的根 CA 變更變更

適用於 MariaDB 的 Azure 資料庫將會變更以 SSL 啟用之用戶端應用程式/驅動程式的根憑證，用來 [連接到資料庫伺服器](concepts-connectivity-architecture.md)。 目前可用的根憑證已設定為在2020年10月26日到期， (10/26/2020) 作為標準維護和安全性最佳作法的一部分。 本文提供更多有關即將進行的變更、將受影響的資源，以及確保您的應用程式維持資料庫伺服器連線所需的步驟等詳細資料。

## <a name="what-update-is-going-to-happen"></a>即將發生什麼更新？

在某些情況下，應用程式會使用由信任的憑證授權單位單位所產生的本機憑證檔案 (CA) 憑證檔案，以安全地連接。 目前的客戶只能使用預先定義的憑證來連線到適用於 MariaDB 的 Azure 資料庫伺服器（位於 [此處](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)）。 不過，[憑證授權單位單位 (ca) 瀏覽器論壇](https://cabforum.org/)   最近發行的報表，其中有多個 ca 廠商發行的憑證不符合規範。

根據產業的合規性需求，CA 廠商會開始撤銷不符合規範之 Ca 的 CA 憑證，要求伺服器使用符合規範的 Ca 所發行的憑證，並由這些符合規範的 Ca 的 CA 憑證簽署。 由於適用於 MariaDB 的 Azure 資料庫目前會使用其中一個不符合規範的憑證（用戶端應用程式會使用這些憑證來驗證其 SSL 連線），因此我們需要確保 (採取適當的動作，如下所述) 將適用于 mariadb 伺服器的潛在影響降到最低。


新憑證將于2020年10月26日 (10/26/2020) 開始使用。從 MySQL 用戶端連線時，如果您使用 CA 驗證或伺服器憑證的完整驗證 (sslmode = verify-CA 或 sslmode = verify-full) ，您必須在2020年10月26日 (10/26/2020) 之前更新您的應用程式設定。

## <a name="how-do-i-know-if-my-database-is-going-to-be-affected"></a>如何? 知道我的資料庫是否會受到影響？

使用 SSL/TLS 並確認根憑證的所有應用程式，都必須更新根憑證，才能連接到適用於 MariaDB 的 Azure 資料庫。 如果您目前未使用 SSL/TLS，將不會影響您的應用程式可用性。 您可以驗證您的用戶端應用程式是否嘗試使用 SSL 模式搭配預先定義的信任憑證授權單位單位， (CA) [此處](concepts-ssl-connection-security.md#default-settings)。

若要避免應用程式的可用性因為未預期地撤銷憑證而中斷，或要更新已撤銷的憑證，請參閱「 [**我需要怎麼做才能維持連線能力**](concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity) 」一節。

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>需要怎麼做才能維持連線能力

若要避免應用程式的可用性因為未預期地撤銷憑證而中斷，或要更新已撤銷的憑證，請遵循下列步驟：

*   從下列連結下載**baltimorecybertrustroot.crt**  &  **DigiCertGlobalRootG2** CA：
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   同時包含 **baltimorecybertrustroot.crt** 和 **DigiCertGlobalRootG2** 憑證來產生合併的 CA 憑證存儲。
    *   若為 JAVA (適用于 mariadb Connector/J) 使用者，請執行：

          ```azurecli-interactive
          keytool -importcert -alias MariaDBServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
          ```

          ```azurecli-interactive
          keytool -importcert -alias MariaDBServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
          ```

          然後，將原始金鑰儲存區檔案取代為新產生的金鑰儲存區檔案：
        *   System.string ( "javax.xml.transform.dom.domresult" trustStore "，" path_to_truststore_file ") ; 
        *   System.string ( "javax.xml.transform.dom.domresult" trustStorePassword "，" password ") ;
    *   針對 .NET (適用于 mariadb Connector/NET，MariaDBConnector) 使用者，請確定 Windows 憑證存放區、受信任的根憑證授權單位都有 **baltimorecybertrustroot.crt** 和 **DigiCertGlobalRootG2** 。 如果有任何憑證不存在，請匯入遺失的憑證。

        ![適用於 MariaDB 的 Azure 資料庫 .net 憑證](media/overview/netconnecter-cert.png)

    *   針對使用 SSL_CERT_DIR 的 Linux 上的 .NET 使用者，請確定 SSL_CERT_DIR 所指出的目錄中都有 **baltimorecybertrustroot.crt** 和 **DigiCertGlobalRootG2** 。 如果有任何憑證不存在，請建立遺失的憑證檔案。

    *   針對其他 (適用于 mariadb 用戶端/適用于 mariadb 工作臺/C/c + +/Go/Python/Ruby/PHP/NodeJS/Perl/Swift) 使用者，您可以合併兩個 CA 憑證檔案，如下所示：以下格式：</b>

        </br>-----開始憑證-----
 </br> (根 CA1： Baltimorecybertrustroot.crt .crt) 
 </br>-----終端憑證-----
 </br>-----開始憑證-----
 </br> (根 CA2： DigiCertGlobalRootG2 .crt) 
 </br>-----終端憑證-----

*   將原始的根 CA pem 檔案取代為合併的根 CA 檔案，然後重新開機您的應用程式/用戶端。
*   未來，在伺服器端部署新的憑證之後，您可以將 CA pem 檔案變更為 DigiCertGlobalRootG2。

## <a name="what-can-be-the-impact"></a>這會造成什麼影響？
如果您使用此處所述的適用於 MariaDB 的 Azure 資料庫發行的憑證，則您的應用程式可用性可能會中斷，因為資料庫將無法連線。 視您的應用程式而定，您可能會收到各種不同的錯誤訊息，包括但不限於：
*   不正確憑證/撤銷憑證
*   連線逾時
*   錯誤（如果適用）

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="1-if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. 如果我未使用 SSL/TLS，是否仍需要更新根 CA？
如果您不是使用 SSL/TLS，則不需要採取任何動作。 

### <a name="2-if-i-am-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. 如果我使用 SSL/TLS，是否需要重新開機我的資料庫伺服器來更新根 CA？
否，您不需要重新開機資料庫伺服器，就能開始使用新的憑證。 這是用戶端的變更，而連入的用戶端連線必須使用新的憑證，以確保它們可以連接到資料庫伺服器。

### <a name="3-what-will-happen-if-i-do-not-update-the-root-certificate-before-october-26-2020-10262020"></a>3. 如果我未在2020年10月26日之前更新根憑證， (10/26/2020) 會發生什麼事？
如果您未在2020年10月26日之前更新根憑證，則透過 SSL/TLS 連線並驗證根憑證的應用程式將無法與適用于 mariadb 資料庫伺服器通訊，而且應用程式將會遇到適用于 mariadb 資料庫伺服器的連線問題。

### <a name="4-do-i-need-to-plan-a-maintenance-downtime-for-this-changebr"></a>4. 我是否需要規劃此變更的維護停機時間？<BR>
否。 因為這裡的變更只在用戶端連接到資料庫伺服器，所以這項變更不需要任何維護停機時間。

### <a name="5--what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-october-26-2020-10262020"></a>5. 如果我無法在2020年10月26日之前取得此變更的排程停機時間， (10/26/2020) ？
由於用來連接到伺服器的用戶端必須更新憑證資訊，如 [此處](./concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity)的修正一節所述，在此情況下，伺服器不需要停機。

###  <a name="6-if-i-create-a-new-server-after-october-26-2020-will-i-be-impacted"></a>6. 如果我在2020年10月26日之後建立新的伺服器，將會受到影響嗎？
針對在2020年10月26日之後建立的伺服器 (10/26/2020) ，您可以使用新發行的憑證，讓您的應用程式使用 SSL 進行連接。

### <a name="7-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>7. Microsoft 更新其憑證或到期原則的頻率為何？
適用於 MariaDB 的 Azure 資料庫所使用的憑證是由受信任的憑證授權單位單位 (CA) 提供。 因此，這些憑證在適用於 MariaDB 的 Azure 資料庫上的支援系結至 CA 支援這些憑證。 不過，在這種情況下，這些預先定義的憑證可能會有未預期的錯誤，這些都必須儘早修正。

### <a name="8-if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-master-server-or-all-the-read-replicas"></a>8. 如果我使用讀取複本，我只需要在主伺服器或所有讀取複本上執行此更新嗎？
由於這項更新是用戶端的變更，如果用戶端用來從複本伺服器讀取資料，則也必須套用這些用戶端的變更。 

### <a name="9-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>9. 我們是否有伺服器端查詢來確認是否正在使用 SSL？
若要確認您是否使用 SSL 連線來連線到伺服器，請參閱 [ssl 驗證](howto-configure-ssl.md#verify-the-ssl-connection)。

### <a name="10-what-if-i-have-further-questions"></a>10. 如果我有其他問題，該怎麼辦？
如果您有任何疑問，請從 [Microsoft Q&中的](mailto:AzureDatabaseformariadb@service.microsoft.com)「社區專家」獲得解答。 如果您有支援方案，且需要技術協助，請 [與我們聯絡](mailto:AzureDatabaseformariadb@service.microsoft.com)
