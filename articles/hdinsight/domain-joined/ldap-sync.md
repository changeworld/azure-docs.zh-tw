---
title: Azure HDInsight 中的 LDAP 同步處理 Ranger 和 Apache Ambari
description: 處理 Ranger 和 Ambari 中的 LDAP 同步處理，並提供一般指導方針。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 99bd1ac156b12a5be7b8c5c17eb5b568b7070a25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "77463215"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>Azure HDInsight 中的 LDAP 同步處理 Ranger 和 Apache Ambari

HDInsight 企業安全性套件 (ESP) 叢集使用 Ranger 進行授權。 Apache Ambari 和 Ranger 會獨立同步處理使用者和群組，並以稍微不同的方式運作。 本文旨在處理 Ranger 和 Ambari 中的 LDAP 同步處理。

## <a name="general-guidelines"></a>一般指導方針

* 一律使用群組部署叢集。
* 若不要變更 Ambari 和 Ranger 中的群組篩選，請嘗試在 Azure AD 中管理這些篩選器，並使用嵌套群組來帶入所需的使用者。
* 一旦使用者進行同步處理之後，即使使用者不是群組的一部分，也不會移除該使用者。
* 如果您需要直接變更 LDAP 篩選器，請先使用 UI，因為它包含一些驗證。

## <a name="users-are-synced-separately"></a>使用者會另外進行同步處理

Ambari 和 Ranger 不會共用使用者資料庫，因為它們有兩個不同的用途。 如果使用者需要使用 Ambari UI，則使用者必須同步至 Ambari。 如果使用者未同步處理至 Ambari，Ambari UI/API 將會拒絕該使用者，但系統的其他部分將可運作 (這些是由 Ranger 或 Resource Manager 所保護，而不是 Ambari) 。 如果您想要將使用者加入 Ranger 原則，請將使用者同步至 Ranger。

部署安全的叢集時，群組成員會以可轉移的 (所有子群組及其成員) 至 Ambari 和 Ranger 的同步處理。 

## <a name="ambari-user-sync-and-configuration"></a>Ambari 使用者同步和設定

從前端節點的 cron 作業， `/opt/startup_scripts/start_ambari_ldap_sync.py` 會每小時執行一次，以排程使用者同步處理。Cron 作業會呼叫 Ambari rest Api 來執行同步處理。腳本會提交使用者和群組的清單，以同步處理 (因為使用者可能不屬於指定的群組，這兩個都是個別指定的) 。 Ambari 同步處理 sAMAccountName 作為使用者名稱，以及所有群組成員（以傳遞方式）。

記錄檔應該在中 `/var/log/ambari-server/ambari-server.log` 。 如需詳細資訊，請參閱 [設定 Ambari 記錄層級](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html)。

在 Data Lake 叢集中，會使用 post 使用者建立攔截來為同步處理的使用者建立主資料夾，並將其設定為主資料夾的擁有者。 如果使用者未正確同步處理到 Ambari，則使用者可能會面臨存取暫存和其他暫存資料夾的失敗。

### <a name="update-groups-to-be-synced-to-ambari"></a>更新要同步處理到 Ambari 的群組

如果您無法在 Azure AD 中管理群組成員資格，您有兩個選擇：

* 執行一次同步，如更完整的 [同步處理 LDAP 使用者和群組](https://docs.cloudera.com/HDPDocuments/HDP3/latest/ambari-authentication-ldap-ad/content/authe_ldapad_synchronizing_ldap_users_and_groups.html)所述。 每當群組成員資格變更時，您就必須重新同步處理。

* 撰寫 cron 作業，使用新的群組 [定期呼叫 AMBARI API](https://community.cloudera.com/t5/Support-Questions/How-do-I-automate-the-Ambari-LDAP-sync/m-p/96634) 。

## <a name="ranger-user-sync-and-configuration"></a>Ranger 使用者同步和設定

Ranger 有一個內建的同步處理引擎，會每小時執行一次，以同步處理使用者。 它不會與 Ambari 共用使用者資料庫。 HDInsight 會設定搜尋篩選，以同步處理在叢集建立期間所指定的系統管理員使用者、看門狗使用者和群組成員。 群組成員將會以可傳遞的同步處理：

* 停用增量同步處理。
* 啟用使用者群組同步對應。
* 指定搜尋篩選以包含可轉移的群組成員。
* 針對使用者和群組的名稱屬性同步 sAMAccountName。

### <a name="group-or-incremental-sync"></a>群組或增量同步處理

Ranger 支援群組同步選項，但它可以作為使用者篩選的交集。 不是群組成員資格與使用者篩選之間的聯集。 Ranger 中群組同步篩選的一般使用案例是-群組篩選： (dn = clusteradmingroup) ，使用者篩選： (city = 西雅圖) 。

增量同步處理只適用于第一次)  (同步處理的使用者。 增量並不會同步處理在初始同步處理後新增至群組的任何新使用者。

### <a name="update-ranger-sync-filter"></a>更新 Ranger 同步篩選

您可以在 Ambari UI 中的 [Ranger 使用者-同步設定] 區段下找到 LDAP 篩選器。 現有的篩選會以表單形式呈現 `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))` 。 請務必在結尾處新增述詞，並使用 `net ads` 搜尋命令或 ldp.exe 或類似的命令來測試篩選準則。

## <a name="ranger-user-sync-logs"></a>Ranger 使用者同步記錄

Ranger 使用者同步可能會在前端節點中發生。 記錄檔位於 `/var/log/ranger/usersync/usersync.log` 。 若要增加記錄的詳細資訊，請執行下列步驟：

1. 登入 Ambari。
1. 移至 [Ranger 設定] 區段。
1. 移至 Advanced **usersync-log4j** 區段。
1. 變更變更 `log4j.rootLogger` 為 `DEBUG` 層級 (之後，它看起來應該像 `log4j.rootLogger = DEBUG,logFile,FilterLog`) 。
1. 儲存設定並重新啟動 ranger。

## <a name="next-steps"></a>後續步驟

* [Azure HDInsight 中的驗證問題](./domain-joined-authentication-issues.md)
* [將 Azure AD 使用者同步處理至 HDInsight 叢集](../hdinsight-sync-aad-users-to-cluster.md)
