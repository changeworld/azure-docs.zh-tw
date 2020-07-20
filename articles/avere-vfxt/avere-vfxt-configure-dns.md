---
title: Avere vFXT DNS - Azure
description: 使用 Avere vFXT for Azure 設定循環配置資源負載平衡的 DNS 伺服器
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 81b53904f85e2ac936195b1e39d7586fd1d47524
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76153780"
---
# <a name="avere-cluster-dns-configuration"></a>Avere 叢集 DNS 組態

本節說明設定 DNS 系統以便為 Avere vFXT 叢集進行負載平衡的基本概念。

本文件*不包含*在 Azure 環境中設定和管理 DNS 伺服器的指示。

請考慮使用手動方法，在裝載 IP 位址時，於用戶端之間平均指派 IP 位址，而不是使用循環配置資源 DNS，為 Azure 中的 vFXT 叢集進行負載平衡。 在[裝載 Avere 叢集](avere-vfxt-mount-clients.md)中描述數種方法。

決定是否要使用 DNS 伺服器時，請記住下列事項：

* 如果只有 NFS 用戶端才能存取您的系統，則不需要使用 DNS，使用數字 IP 位址就可以指定所有網路位址。

* 如果您的系統支援 SMB (CIFS) 存取，就需要使用 DNS，因為您必須為 Active Directory 伺服器指定 DNS 網域。

* 如果您想要使用 Kerberos 驗證，則需要有 DNS。

## <a name="load-balancing"></a>負載平衡

若要分配整體負載，請將您的 DNS 網域設定為將循環配置資源負載分配用於面向用戶端的 IP 位址。

## <a name="configuration-details"></a>組態詳細資料

當用戶端存取叢集時，RRDNS 會自動在所有可用的介面之間平衡其要求。

為達到最佳效能，請將您的 DNS 伺服器設定為處理面向用戶端的叢集位址，如下圖所示。

叢集 vserver 會顯示在左側，而 IP 位址則會出現在中間和右側。 使用 A 記錄和指標設定每個用戶端的存取點，如圖所示。

![Avere 叢集循環配置資源 DNS 圖表](media/avere-vfxt-rrdns-diagram.png)
<!--- separate text description file provided  [diagram text description](avere-vfxt-rrdns-alt-text.md) -->

每個面向用戶端的 IP 位址都必須有唯一的名稱，供叢集內部使用 (在此圖表中，為了清楚起見，用戶端 IP 名稱為 vs1-client-IP-*，但在生產環境中，您可能要使用更精簡的名稱，例如 client*)。

用戶端會使用 vserver 名稱作為伺服器引數，裝載叢集。

修改您 DNS 伺服器的 ``named.conf`` 檔案，將查詢的循環順序設定為您的 vserver。 此選項可確保循環顯示所有可用的值。 加入陳述式，如下所示：

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

下列 ``nsupdate`` 命令提供正確設定 DNS 的範例：

```
update add vserver1.example.com. 86400 A 10.0.0.10
update add vserver1.example.com. 86400 A 10.0.0.11
update add vserver1.example.com. 86400 A 10.0.0.12
update add vs1-client-IP-10.example.com. 86400 A 10.0.0.10
update add vs1-client-IP-11.example.com. 86400 A 10.0.0.11
update add vs1-client-IP-12.example.com. 86400 A 10.0.0.12
update add 10.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-10.example.com
update add 11.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-11.example.com
update add 12.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-12.example.com
```

## <a name="cluster-dns-settings"></a>叢集 DNS 設定

在 **[叢集**  >  **管理網路**設定] 頁面中，指定 vFXT 叢集所使用的 DNS 伺服器。 該頁面上的設定包括：

* DNS 伺服器位址
* DNS 網域名稱
* DNS 搜尋網域

如需有關使用此頁面的詳細資訊，請閱讀《Avere 叢集設定指南》中的 [DNS 設定](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>)。
