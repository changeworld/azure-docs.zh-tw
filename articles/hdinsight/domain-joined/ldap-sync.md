---
title: 在 Azure HDInsight 中，在遊俠和阿帕奇安巴里中的 LDAP 同步
description: 在 Ranger 和 Ambari 中解決 LDAP 同步問題，並提供一般指南。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 99bd1ac156b12a5be7b8c5c17eb5b568b7070a25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77463215"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>在 Azure HDInsight 中，在遊俠和阿帕奇安巴里中的 LDAP 同步

HDInsight 企業安全包 （ESP） 群集使用 Ranger 進行授權。 Apache Ambari 和 Ranger 都獨立同步使用者和組，並且工作方式稍有不同。 本文旨在解決 Ranger 和 Ambari 中的 LDAP 同步問題。

## <a name="general-guidelines"></a>一般方針

* 始終部署具有組的群集。
* 請嘗試在 Azure AD 中管理所有這些篩選器，並使用嵌套組引入所需的使用者，而不是更改 Ambari 和 Ranger 中的組篩選器。
* 同步使用者後，即使使用者不是組的成員，也不會將其刪除。
* 如果需要直接更改 LDAP 篩選器，請使用 UI，因為它包含一些驗證。

## <a name="users-are-synced-separately"></a>使用者單獨同步

Ambari 和 Ranger 不共用使用者資料庫，因為它們具有兩種不同的用途。 如果使用者需要使用 Ambari UI，則需要將使用者同步到 Ambari。 如果使用者未同步到 Ambari，Ambari UI / API 將拒絕它，但系統的其他部分將工作（這些部分由遊俠或資源管理器守衛，而不是 Ambari）。 如果要將使用者包含在 Ranger 策略中，請將使用者同步到 Ranger。

部署安全群集時，組成員會以傳輸方式（所有子組及其成員）同步到 Ambari 和 Ranger。 

## <a name="ambari-user-sync-and-configuration"></a>Ambari 使用者同步和配置

從頭節點、cron 作業`/opt/startup_scripts/start_ambari_ldap_sync.py`每小時運行一次以計畫使用者同步。cron 作業調用 Ambari 休息 API 以執行同步。該腳本提交要同步的使用者和組的清單（因為使用者可能不屬於指定的組，兩者都是單獨指定的）。 Ambari 將 sAMAccountName 同步為使用者名，並且所有組成員都以傳遞方式同步。

日誌應位於`/var/log/ambari-server/ambari-server.log`中。 有關詳細資訊，請參閱配置[Ambari 日誌記錄級別](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html)。

在 Data Lake 群集中，帖子使用者創建掛鉤用於為同步使用者創建主資料夾，並將這些資料夾設置為主資料夾的擁有者。 如果使用者未正確同步到 Ambari，則使用者在訪問暫存資料夾和其他暫存檔案夾時可能會面臨失敗。

### <a name="update-groups-to-be-synced-to-ambari"></a>更新要同步到 Ambari 的組

如果無法管理 Azure AD 中的組成員身份，則有兩種選擇：

* 在[同步 LDAP 使用者和組](https://docs.cloudera.com/HDPDocuments/HDP3/latest/ambari-authentication-ldap-ad/content/authe_ldapad_synchronizing_ldap_users_and_groups.html)中更全面地執行一次性同步。 每當組成員身份發生更改時，您必須再次執行此同步。

* 編寫 cron 作業，定期使用新組調用[Ambari API。](https://community.cloudera.com/t5/Support-Questions/How-do-I-automate-the-Ambari-LDAP-sync/m-p/96634)

## <a name="ranger-user-sync-and-configuration"></a>遊俠使用者同步和配置

Ranger 具有一個內置同步引擎，每小時運行一次以同步使用者。 它不與 Ambari 共用使用者資料庫。 HDInsight 配置搜索篩選器以同步管理員使用者、看門狗使用者和群集創建期間指定的組成員。 組成員將經過同步：

* 禁用增量同步。
* 啟用使用者組同步映射。
* 指定搜索篩選器以包括傳遞組成員。
* 同步使用者 sAMAccount 名稱和組的名稱屬性。

### <a name="group-or-incremental-sync"></a>組或增量同步

Ranger 支援組同步選項，但它用作與使用者篩選器的交集。 不是組成員身份和使用者篩選器之間的聯合。 Ranger 中組同步篩選器的典型用例是 - 組篩選器：（dn_群集管理組）、使用者篩選器：（城市_西雅圖）。

增量同步僅適用于已同步的使用者（首次）。 增量不會同步在初始同步後添加到組的任何新使用者。

### <a name="update-ranger-sync-filter"></a>更新遊俠同步篩選器

LDAP 篩選器可以在 Ambari UI 下 Ranger 使用者同步配置部分中找到。 現有篩選器將處於 表單`(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))`中。 請確保在末尾添加謂詞，並使用`net ads`搜索命令或 ldp.exe 或類似內容測試篩選器。

## <a name="ranger-user-sync-logs"></a>遊俠使用者同步日誌

遊俠使用者同步可能發生在任一頭節點外。 日誌在`/var/log/ranger/usersync/usersync.log`中。 要增加日誌的詳細性，執行以下步驟：

1. 登錄安巴里。
1. 轉到遊俠配置部分。
1. 轉到高級**使用者同步日誌4j**部分。
1. 將`log4j.rootLogger`更改為`DEBUG`級別（更改後，它應`log4j.rootLogger = DEBUG,logFile,FilterLog`看起來像 ）。
1. 保存配置並重新啟動護林員。

## <a name="next-steps"></a>後續步驟

* [Azure HDInsight 中的身份驗證問題](./domain-joined-authentication-issues.md)
* [將 Azure AD 使用者同步處理至 HDInsight 叢集](../hdinsight-sync-aad-users-to-cluster.md)
