---
title: Azure SQL Database & SQL 受控執行個體的憑證輪替
description: 瞭解即將變更的根憑證變更將會影響 Azure SQL Database 和 Azure SQL 受控執行個體
author: srdan-bozovic-msft
ms.author: srbozovi
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
ms.date: 09/13/2020
ms.openlocfilehash: a273b9aaae083bb4566d289e9680b50c686d4e9b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91342249"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-sql-database--sql-managed-instance"></a>瞭解 Azure SQL Database & SQL 受控執行個體的根 CA 變更變更

Azure SQL Database & SQL 受控執行個體將會變更以 SSL 啟用之用戶端應用程式/驅動程式的根憑證，用來建立安全的 TDS 連接。 [目前的根憑證](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)已設定為在2020年10月26日過期，作為標準維護和安全性最佳作法的一部分。 本文提供更多有關即將進行的變更、將受影響的資源，以及確保您的應用程式維持資料庫伺服器連線所需的步驟等詳細資料。

## <a name="what-update-is-going-to-happen"></a>即將發生什麼更新？

[憑證授權單位單位 (ca) 瀏覽器論壇](https://cabforum.org/) 最近發行的報表，其中有多個 ca 廠商發行的憑證不符合規範。

根據產業的合規性需求，CA 廠商會開始撤銷不符合規範之 Ca 的 CA 憑證，要求伺服器使用符合規範的 Ca 所發行的憑證，並由這些符合規範的 Ca 的 CA 憑證簽署。 由於 Azure SQL Database & SQL 受控執行個體目前會使用其中一個不符合規範的憑證，用戶端應用程式會使用這些憑證來驗證其 SSL 連線，因此我們必須確保採取適當的動作 () 所述，以將潛在的影響降至最低。

新憑證將于2020年10月26日開始使用。 如果您在從 SQL 用戶端連接時使用伺服器憑證的完整驗證 (TrustServerCertificate = true) ，您必須確定您的 SQL 用戶端能夠在2020年10月26日之前驗證新的根憑證。

## <a name="how-do-i-know-if-my-application-might-be-affected"></a>如何? 知道我的應用程式可能會受到影響嗎？

使用 SSL/TLS 並確認根憑證的所有應用程式都必須更新根憑證，才能連接到 Azure SQL Database 的 & SQL 受控執行個體。 

如果您目前未使用 SSL/TLS，將不會影響您的應用程式可用性。 您可以藉由查看連接字串，確認您的用戶端應用程式是否嘗試驗證根憑證。 如果 TrustServerCertificate 明確設定為 true，則不會受到影響。

如果您的用戶端驅動程式利用 OS 憑證存放區，因為大部分的驅動程式都是如此，而您的作業系統會定期維護，這項變更可能不會對您造成影響，因為我們要切換的根憑證應該已經在您的受信任的根憑證存放區中提供使用。 檢查巴爾的摩 CyberDigiCert GlobalRoot G2 並驗證它是否存在。

如果您的用戶端驅動程式使用本機檔案憑證存放區，為了避免應用程式的可用性因為未預期地撤銷憑證而中斷，或要更新已撤銷的憑證，請參閱 [**我需要怎麼做才能維持連線能力**](./ssl-root-certificate-expiring.md#what-do-i-need-to-do-to-maintain-connectivity) 一節。

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>需要怎麼做才能維持連線能力

若要避免應用程式的可用性因為未預期地撤銷憑證而中斷，或要更新已撤銷的憑證，請遵循下列步驟：

*   從下列連結下載巴爾的摩 CyberTrust Root & DigiCert GlobalRoot G2 根 CA：
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   同時包含 **baltimorecybertrustroot.crt** 和 **DigiCertGlobalRootG2** 憑證來產生合併的 CA 憑證存儲。

## <a name="what-can-be-the-impact"></a>這會造成什麼影響？
如果您要驗證如下所述的伺服器憑證，則應用程式的可用性可能會中斷，因為無法連線到資料庫。 視您的應用程式而定，您可能會收到各種不同的錯誤訊息，包括但不限於：
*   不正確憑證/撤銷憑證
*   連線逾時
*   錯誤（如果適用）

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>如果我不是使用 SSL/TLS，還需要更新根 CA 嗎？
如果您不是使用 SSL/TLS，則不需要任何關於此變更的動作。 您仍然應該設定計劃，以便在不久的未來計畫進行 TLS 強制，以開始使用最新的 TLS 版本。

### <a name="what-will-happen-if-i-do-not-update-the-root-certificate-before-october-26-2020"></a>如果我未在2020年10月26日之前更新根憑證，會發生什麼事？
如果您未在2020年11月30日之前更新根憑證，則透過 SSL/TLS 連線的應用程式以及根憑證的驗證，將無法與 Azure SQL Database & SQL 受控執行個體，而應用程式將會遇到 Azure SQL Database & SQL 受控執行個體的連接問題。

### <a name="do-i-need-to-plan-a-maintenance-downtime-for-this-changebr"></a>我是否需要規劃此變更的維護停機時間？<BR>
否。 因為這裡的變更只在用戶端連接到伺服器，所以這項變更不需要任何維護停機時間。

### <a name="what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-october-26-2020"></a>如果我無法在2020年10月26日之前取得此變更的排程停機時間，該怎麼辦？
由於用來連接到伺服器的用戶端必須更新憑證資訊，如 [此處](./ssl-root-certificate-expiring.md#what-do-i-need-to-do-to-maintain-connectivity)的修正一節所述，在此情況下，伺服器不需要停機。

### <a name="if-i-create-a-new-server-after-november-30-2020-will-i-be-impacted"></a>如果我在2020年11月30日之後建立新的伺服器，將會受到影響嗎？
針對在2020年10月26日之後建立的伺服器，您可以使用新發行的憑證，讓您的應用程式使用 SSL 進行連接。

### <a name="how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>Microsoft 更新其憑證或到期原則的頻率為何？
Azure SQL Database & SQL 受控執行個體所使用的憑證是由受信任的憑證授權單位單位所提供， (CA) 。 因此，Azure SQL Database & SQL 受控執行個體的這些憑證支援系結至 CA 支援這些憑證。 不過，在這種情況下，這些預先定義的憑證可能會有未預期的錯誤，這些都必須儘早修正。

### <a name="if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-master-server-or-all-the-read-replicas"></a>如果我使用讀取複本，是否只需要在主伺服器或所有讀取複本上執行此更新？
由於這項更新是用戶端的變更，如果用戶端用來從複本伺服器讀取資料，則也必須套用這些用戶端的變更。 

### <a name="do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>我們是否有伺服器端查詢，以確認是否正在使用 SSL？
由於這項設定是用戶端，因此伺服器端不提供資訊。

### <a name="what-if-i-have-further-questions"></a>如果我有其他問題，該怎麼辦？
如果您有支援方案，且需要技術協助，請建立 Azure 支援要求，請參閱 [如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。
