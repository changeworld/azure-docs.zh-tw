---
title: Azure HDInsight 中 Hive 的安全性選項
description: 標準和 ESP 叢集中的 Hive 安全性選項。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/02/2020
ms.openlocfilehash: 14a41365640439ff99861bbb22cc04a40f35da5e
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92222072"
---
# <a name="security-options-for-hive-in-azure-hdinsight"></a>Azure HDInsight 中 Hive 的安全性選項

本檔說明 HDInsight 中 Hive 的建議安全性選項。 您可以透過 Ambari 設定這些選項。

![[Hive 的安全性選項]](./media/hdinsight-security-options-for-hive/security-options-hive.png "Hive 的安全性選項")

## <a name="hiveserver2-authentication"></a>HiveServer2 authentication

針對標準叢集，HiveServer2 authentication 的建議設定是預設值，也就是 [無]。 若要啟用驗證，建議您 (企業安全性套件) 叢集升級為 [ESP](https://docs.microsoft.com/azure/hdinsight/domain-joined/hdinsight-security-overview) 。 

針對 ESP 叢集，預設會啟用 [Kerberos](https://web.mit.edu/Kerberos/) 驗證。 不支援 (PAM) 和自訂驗證配置的插入式驗證模組。

## <a name="hiveserver2-authorization"></a>HiveServer2 授權

若為標準叢集，預設設定為 [無]。 [SqlStdAuth (SQL 標準型授權) ](https://cwiki.apache.org/confluence/display/Hive/SQL+Standard+based+hive+authorization) 可以啟用。 標準叢集不支援透過 [Apache Ranger](https://ranger.apache.org/) 的授權。 建議您升級至 ESP 叢集以進行 Ranger 授權。 

針對 ESP 叢集，預設會啟用透過 Ranger 的授權。 


## <a name="ssl-encryption-for-hiveserver2"></a>HiveServer2 的 SSL 加密

不建議在標準或 ESP 叢集上啟用 Hiveserver2 SSL。 改為在閘道上啟用 SSL。 您可以使用[網際網路通訊協定安全性 (IPSec) ](https://en.wikipedia.org/wiki/IPsec)，啟用[傳輸中加密](https://docs.microsoft.com/azure/hdinsight/domain-joined/encryption-in-transit)來加密叢集節點之間的通訊。


## <a name="next-steps"></a>後續步驟
* [HiveServer2 Authentication 總覽](https://cwiki.apache.org/confluence/display/Hive/Setting+up+HiveServer2#SettingUpHiveServer2-Authentication/SecurityConfiguration)
* [HiveServer2 授權總覽](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Authorization#:~:text=%20Overview%20of%20Authorization%20Modes%20%201%201,and%20Apache%20Sentry%20are%20apache%20projects...%20More%20)
* [啟用以 SQL 標準為基礎的 Hive 授權](https://community.cloudera.com/t5/Community-Articles/Getting-started-with-SQLStdAuth/ta-p/244263)
* [使用 Hive 的 Apache Ranger](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-run-hive#:~:text=Create%20Hive%20ODBC%20data%20source%20%20%20,Enter%20hiveuser1%40contoso158.onmicrosoft.c%20...%20%205%20more%20rows%20)
