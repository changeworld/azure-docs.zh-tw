---
title: Azure HDInsight 中 Hive 的安全性選項
description: 標準和 ESP 叢集中的 Hive 安全性選項。
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/02/2020
ms.openlocfilehash: 1189a320d0dc700756c9f7664d0a6303be5dab51
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98940899"
---
# <a name="security-options-for-hive-in-azure-hdinsight"></a>Azure HDInsight 中 Hive 的安全性選項

本檔說明 HDInsight 中 Hive 的建議安全性選項。 您可以透過 Ambari 設定這些選項。

![[Hive 的安全性選項]](./media/hdinsight-security-options-for-hive/security-options-hive.png "Hive 的安全性選項")

## <a name="hiveserver2-authentication"></a>HiveServer2 authentication

針對標準叢集，HiveServer2 authentication 的建議設定是預設值，也就是 [無]。 若要啟用驗證，建議您 (企業安全性套件) 叢集升級為 [ESP](../domain-joined/hdinsight-security-overview.md) 。 

針對 ESP 叢集，預設會啟用 [Kerberos](https://web.mit.edu/Kerberos/) 驗證。 不支援 (PAM) 和自訂驗證配置的插入式驗證模組。

## <a name="hiveserver2-authorization"></a>HiveServer2 授權

若為標準叢集，預設設定為 [無]。 [SqlStdAuth (SQL 標準型授權) ](https://cwiki.apache.org/confluence/display/Hive/SQL+Standard+based+hive+authorization) 可以啟用。 標準叢集不支援透過 [Apache Ranger](https://ranger.apache.org/) 的授權。 建議您升級至 ESP 叢集以進行 Ranger 授權。 

針對 ESP 叢集，預設會啟用透過 Ranger 的授權。 


## <a name="ssl-encryption-for-hiveserver2"></a>HiveServer2 的 SSL 加密

不建議在標準或 ESP 叢集上啟用 Hiveserver2 SSL。 改為在閘道上啟用 SSL。 您可以使用[網際網路通訊協定安全性 (IPSec) ](https://en.wikipedia.org/wiki/IPsec)，啟用[傳輸中加密](../domain-joined/encryption-in-transit.md)來加密叢集節點之間的通訊。


## <a name="next-steps"></a>後續步驟
* [HiveServer2 Authentication 總覽](https://cwiki.apache.org/confluence/display/Hive/Setting+up+HiveServer2#SettingUpHiveServer2-Authentication/SecurityConfiguration)
* [HiveServer2 授權總覽](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Authorization)
* [啟用以 SQL 標準為基礎的 Hive 授權](https://community.cloudera.com/t5/Community-Articles/Getting-started-with-SQLStdAuth/ta-p/244263)
* [使用 Hive 的 Apache Ranger](../domain-joined/apache-domain-joined-run-hive.md)
