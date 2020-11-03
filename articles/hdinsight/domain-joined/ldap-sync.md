---
title: Azure HDInsight 中的 LDAP 同步處理 Ranger 和 Apache Ambari
description: 處理 Ranger 和 Ambari 中的 LDAP 同步處理，並提供一般指導方針。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 0779ac261fbb4ee91bf63021bb0cc685a371c2b2
ms.sourcegitcommit: bbd66b477d0c8cb9adf967606a2df97176f6460b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234064"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>Azure HDInsight 中的 LDAP 同步處理 Ranger 和 Apache Ambari

HDInsight 企業安全性套件 (ESP) 叢集使用 Ranger 進行授權。 Apache Ambari 和 Ranger 會獨立同步處理使用者和群組，並以稍微不同的方式運作。 本文旨在處理 Ranger 和 Ambari 中的 LDAP 同步處理。

## <a name="general-guidelines"></a>一般指導方針

* 一律部署具有一或多個群組的叢集。
* 如果您想要在叢集中使用更多群組，請檢查 Azure Active Directory (Azure AD) 中更新群組成員資格是否合理。
* 如果您想要變更叢集群組，可以使用 Ambari 來變更同步篩選。
* 在後續同步處理中，Azure AD 中的所有群組成員資格變更都會反映在叢集中。 變更必須先同步至 Azure AD Domain Services (Azure AD DS) ，然後再同步到叢集。
* HDInsight 叢集會使用 Samba/Winbind 來投影叢集節點上的群組成員資格。
* 群組成員會以可轉移的 (所有子群組及其成員) 至 Ambari 和 Ranger 的同步處理。 

## <a name="users-are-synced-separately"></a>使用者會另外進行同步處理

 * Ambari 和 Ranger 不會共用使用者資料庫，因為它們有兩個不同的用途。 
   * 如果使用者需要使用 Ambari UI，則使用者必須同步至 Ambari。 
   * 如果使用者未同步處理至 Ambari，Ambari UI/API 將會拒絕它，但是系統的其他部分將會運作 (這些會受到 Ranger 或 Resource Manager 的防護，而不是 Ambari) 的保護。
   * 若要在 Ranger 原則中包含使用者或群組，必須在 Ranger 中明確同步處理主體。

## <a name="ambari-user-sync-and-configuration"></a>Ambari 使用者同步和設定

從前端節點的 cron 作業， `/opt/startup_scripts/start_ambari_ldap_sync.py` 會每小時執行一次，以排程使用者同步處理。Cron 作業會呼叫 Ambari rest Api 來執行同步處理。腳本會提交使用者和群組的清單，以同步處理 (因為使用者可能不屬於指定的群組，這兩個都是個別指定的) 。 Ambari 同步處理 sAMAccountName 作為使用者名稱，以及所有群組成員（以傳遞方式）。

記錄檔應該在中 `/var/log/ambari-server/ambari-server.log` 。 如需詳細資訊，請參閱 [設定 Ambari 記錄層級](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html)。

在 Data Lake 叢集中，會使用 post 使用者建立攔截來為同步處理的使用者建立主資料夾，並將其設定為主資料夾的擁有者。 如果使用者未正確地同步處理到 Ambari，則使用者可能會在執行中的作業中遇到失敗，因為主資料夾可能未正確設定。

## <a name="ranger-user-sync-and-configuration"></a>Ranger 使用者同步和設定

Ranger 具有內建的同步處理引擎，會每小時執行一次來同步處理使用者。 它不會與 Ambari 共用使用者資料庫。 HDInsight 會設定搜尋篩選，以同步處理在叢集建立期間所指定的系統管理員使用者、看門狗使用者和群組成員。 群組成員將會以可傳遞的同步處理：

1. 停用增量同步處理。
1. 啟用使用者群組同步對應。
1. 指定搜尋篩選以包含可轉移的群組成員。
1. 同步使用者的 sAMAccountName 屬性和群組的名稱屬性。

### <a name="group-or-incremental-sync"></a>群組或增量同步處理

Ranger 支援群組同步選項，但它會以使用者篩選的交集來運作，而不是群組成員資格和使用者篩選之間的聯集。 Ranger 中群組同步篩選的一般使用案例是-群組篩選： (dn = clusteradmingroup) ，使用者篩選： (city = 西雅圖) 。

增量同步處理只適用于第一次)  (同步處理的使用者。 增量並不會同步處理在初始同步處理後新增至群組的任何新使用者。

### <a name="update-ranger-sync-filter"></a>更新 Ranger 同步篩選

您可以在 Ambari UI 中的 [Ranger 使用者-同步設定] 區段下找到 LDAP 篩選器。 現有的篩選會以表單形式呈現 `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))` 。 請務必在結尾處新增述詞，並使用 `net ads` 搜尋命令或 ldp.exe 或類似的命令來測試篩選準則。

## <a name="ranger-user-sync-logs"></a>Ranger 使用者同步記錄

Ranger 使用者同步可能會在前端節點中發生。 記錄檔位於 `/var/log/ranger/usersync/usersync.log` 。 若要增加記錄的詳細資訊，請執行下列步驟：

1. 登入 Ambari。
1. 移至 [Ranger 設定] 區段。
1. 移至 Advanced **usersync-log4j** 區段。
1. 將變更 `log4j.rootLogger` 為 [ `DEBUG` 層級]。  (變更之後，看起來應該像 `log4j.rootLogger = DEBUG,logFile,FilterLog`) 。
1. 儲存設定並重新啟動 Ranger。

## <a name="known-issues-with-ranger-user-sync"></a>Ranger 使用者同步的已知問題
* 如果組名有 unicode 字元，則 Ranger 同步會無法同步處理該物件。 如果使用者屬於具有國際字元的群組，Ranger 會同步處理部分群組成員資格
* 使用者名稱 (sAMAccountName) 和組名 (名稱) 必須是長度超過20個字元。 如果組名較長，則在計算許可權時，系統會將使用者視為不屬於群組。

## <a name="next-steps"></a>下一步

* [Azure HDInsight 中的驗證問題](./domain-joined-authentication-issues.md)
* [將 Azure AD 使用者同步處理至 HDInsight 叢集](../hdinsight-sync-aad-users-to-cluster.md)
