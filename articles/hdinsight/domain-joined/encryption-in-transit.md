---
title: Azure HDInsight 傳輸中加密
description: 深入瞭解安全性功能，以提供 Azure HDInsight 叢集的傳輸加密。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/24/2020
ms.openlocfilehash: ac210adbc29075f27739b08dcf83eeedbeea3ed5
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630357"
---
# <a name="ipsec-encryption-in-transit-for-azure-hdinsight"></a>傳輸中的 IPSec 加密 Azure HDInsight

本文討論 Azure HDInsight 叢集節點之間的通訊在傳輸中的執行方式。

## <a name="background"></a>背景

Azure HDInsight 提供各種安全性功能來保護您的企業資料。 這些解決方案會依周邊安全性、驗證、授權、審核、加密和合規性的要素來分組。 加密可以套用至待用和傳輸中的資料。

靜態加密是在 Azure 儲存體帳戶上的伺服器端加密，以及屬於您 HDInsight 叢集一部分的 Azure Vm 上的磁片加密所涵蓋。

您可以使用 [傳輸層安全性 (TLS) ](../transport-layer-security.md) 來存取 HDInsight 上傳輸中的資料，以存取叢集閘道和 [網際網路通訊協定安全性 (](https://wikipedia.org/wiki/IPsec) 叢集節點之間的 IPSec) 。 IPSec 可以選擇性地在所有前端節點、背景工作節點、邊緣節點和 zookeeper 節點之間啟用。 它不會在閘道或 [識別碼](./identity-broker.md) 代理人節點（以 Windows 為基礎的 vm 和叢集中其他以 linux 為基礎的節點）之間的流量啟用。

## <a name="enable-encryption-in-transit"></a>啟用傳輸中加密

### <a name="azure-portal"></a>Azure 入口網站

若要在使用 Azure 入口網站啟用傳輸中加密的情況下建立新的叢集，請執行下列步驟：

1. 開始一般叢集建立程式。 請參閱使用適用于初始叢集建立步驟的 [Azure 入口網站，在 HDInsight 中建立以 Linux 為基礎](../hdinsight-hadoop-create-linux-clusters-portal.md) 的叢集。
1. 完成 [ **基本** ] 和 [ **儲存體** ] 索引標籤。 繼續前往 [ **安全性 + 網路** ] 索引標籤。

    :::image type="content" source="media/encryption-in-transit/create-cluster-security-networking-tab.png" alt-text="[建立叢集-安全性和網路] 索引標籤。":::

1. 在 [ **安全性 + 網路** ] 索引標籤上，選取 [ **啟用傳輸中加密** ] 核取方塊。

    :::image type="content" source="media/encryption-in-transit/enable-encryption-in-transit.png" alt-text="建立叢集-在傳輸中啟用加密。":::

### <a name="create-a-cluster-with-encryption-in-transit-enabled-through-the-azure-cli"></a>使用透過 Azure CLI 啟用傳輸中加密來建立叢集

傳輸中的加密是使用屬性來啟用 `isEncryptionInTransitEnabled` 。

您可以 [下載範例範本和參數](https://github.com/Azure-Samples/hdinsight-enterprise-security)檔案。 使用範本和以下的 Azure CLI 程式碼片段之前，請使用正確的值取代下列預留位置：

| 預留位置 | 描述 |
|---|---|
| `<SUBSCRIPTION_ID>` | 您 Azure 訂用帳戶的識別碼 |
| `<RESOURCE_GROUP>` | 您要在其中建立新叢集和儲存體帳戶的資源群組。 |
| `<STORAGEACCOUNTNAME>` | 應該與叢集搭配使用的現有儲存體帳戶。 名稱的格式應為 `ACCOUNTNAME.blob.core.windows.net` |
| `<CLUSTERNAME>` | 您的 HDInsight 叢集名稱。 |
| `<PASSWORD>` | 您選擇使用 SSH 和 Ambari 儀表板登入叢集的密碼。 |
| `<VNET_NAME>` | 將在其中部署叢集的虛擬網路。 |

下列程式碼片段會執行下列初始步驟：

1. 登入您的 Azure 帳戶。
1. 設定使用中的訂用帳戶來完成建立作業。
1. 為新的部署活動建立新的資源群組。
1. 部署範本以建立新的叢集。

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus2

az group deployment create --name HDInsightEnterpriseSecDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-enterprise-security.json \
    --parameters parameters.json
```

## <a name="next-steps"></a>後續步驟

* [Azure HDInsight 中的企業安全性概觀](hdinsight-security-overview.md)
* [將 Azure Active Directory 的使用者同步處理至 HDInsight](../disk-encryption.md)叢集。
