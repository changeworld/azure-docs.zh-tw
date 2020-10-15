---
title: 教學課程：在 Azure FXT Edge Filer 叢集中設定網路
description: 如何在建立 Azure FXT Edge Filer 叢集之後自訂網路設定
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 9b0154889544e0054e309cc5f43851b73b4396b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80754686"
---
# <a name="tutorial-configure-the-clusters-network-settings"></a>教學課程：設定叢集的網路設定

在使用新建立的 Azure FXT Edge Filer 叢集之前，請先檢查和自訂工作流程的幾個網路設定。 

本教學課程會說明可能需要為新叢集調整的網路設定。 

您將了解： 

> [!div class="checklist"]
> * 在建立叢集之後可能需要更新的網路設定
> * 哪些 Azure FXT Edge Filer 使用案例需要 AD 伺服器或 DNS 伺服器 
> * 如何設定循環配置資源 DNS (RRDNS) 以便自動將用戶端要求負載平衡至 FXT 叢集

完成這些步驟所需花費的時間取決於系統需要進行多少設定變更：

* 如果您只需要完整閱讀本教學課程並檢查一些設定，則大約需要 10 至 15 分鐘的時間。 
* 如果您需要設定循環配置資源 DNS，則該工作可能需要一個小時以上的時間。

## <a name="adjust-network-settings"></a>調整網路設定

有好幾個網路相關工作屬於新 Azure FXT Edge Filer 叢集設定程序的一部分。 請查看這份清單，並決定哪些工作適用於您的系統。

若要深入了解叢集的網路設定，請閱讀《叢集設定指南》中的[設定網路服務](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html)。

* 為面向用戶端的網路設定循環配置資源 DNS (選擇性)

  請藉由設定 DNS 系統來對叢集流量進行負載平衡，如[設定 FXT Edge Filer 叢集的 DNS](#configure-dns-for-load-balancing) 所述。

* 確認 NTP 設定

* 設定 Active Directory 和使用者名稱/群組名稱下載項目 (如有需要)

  如果網路主機使用 Active Directory 或其他種類的外部目錄服務，您就必須修改叢集的目錄服務安裝，來設定叢集要如何下載使用者名稱和群組資訊。 如需詳細資訊，請閱讀《叢集設定指南》中的 [叢集]   > [目錄服務]  。

  如果您需要 SMB 支援，就必須有 AD 伺服器。 先設定 AD 再開始設定 SMB。

* 定義 VLAN (選擇性)
  
  請先設定任何所需的其他 VLAN，再定義叢集的 VServer 和全域命名空間。 若要深入了解，請閱讀《叢集設定指南》中的[使用 VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview)。

* 設定 Proxy 伺服器 (如有需要)

  如果叢集使用 Proxy 伺服器來連線至外部位址，請遵循下列步驟來設定叢集：

  1. 在 [Proxy 設定]  設定頁面中定義 Proxy 伺服器
  1. 使用 [叢集]   > [一般設定]  頁面或 [核心檔案管理工具詳細資料]  頁面來套用 Proxy 伺服器設定。
  
  如需詳細資訊，請閱讀《叢集設定指南》中的[使用 Web Proxy](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/proxy_overview.html)。

* 上傳[加密憑證](#encryption-certificates)供叢集使用 (選擇性)

### <a name="encryption-certificates"></a>加密憑證

FXT Edge Filer 叢集會使用 X.509 憑證來進行下列功能：

* 加密叢集管理流量

* 代表第三方 KMIP 伺服器的用戶端進行驗證

* 確認雲端提供者的伺服器憑證

如果您需要將憑證上傳至叢集，請使用 [叢集]   > [憑證]  設定頁面。 詳細資料位於《叢集設定指南》的 [[叢集] > [憑證]](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_certificates.html) 頁面。

若要加密叢集管理通訊，請使用 [叢集]   > [一般設定]  設定頁面來選取要用於系統管理 TLS 的憑證。

> [!Note] 
> 雲端服務的存取金鑰會使用 [雲端認證]  設定頁面來儲存。 前述的[新增核心檔案管理工具](fxt-add-storage.md#add-a-core-filer)一節會顯示範例；如需詳細資訊，請閱讀《叢集設定指南》的[雲端認證](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_credentials.html)一節。 

## <a name="configure-dns-for-load-balancing"></a>設定要用於負載平衡的 DNS

本節會說明相關基本概念，讓您了解如何設定循環配置資源 (RRDNS) 系統，以便將用戶端的負載分散到 FXT Edge Filer 叢集中的所有面向用戶端的 IP 位址。 

### <a name="decide-whether-or-not-to-use-dns"></a>決定是否使用 DNS

負載平衡永遠是建議啟用的選項，但您不一定要使用 DNS。 例如，在某些類型的用戶端工作流程中，如果用戶端掛接叢集，則使用指令碼將叢集的 IP 位址平均指派給各用戶端可能會更合理。 [掛接叢集](fxt-mount-clients.md)中會說明一些方法。 

決定是否要使用 DNS 伺服器時，請記住下列事項： 

* 如果只有 NFS 用戶端會存取系統，就不需要使用 DNS。 使用數字 IP 位址就可以指定所有網路位址。 

* 如果您的系統支援 SMB (CIFS) 存取，就需要使用 DNS，因為您必須為 Active Directory 伺服器指定 DNS 網域。

* 如果您想要使用 Kerberos 驗證，則需要有 DNS。

### <a name="round-robin-dns-configuration-details"></a>循環配置資源 DNS 設定詳細資料

當用戶端存取叢集時，RRDNS 會自動在所有可用的介面之間平衡其要求。

為達到最佳效能，請將您的 DNS 伺服器設定為處理面向用戶端的叢集位址，如下圖所示。

叢集 vserver 會顯示在左側，而 IP 位址則會出現在中間和右側。 使用 A 記錄和指標設定每個用戶端的存取點，如圖所示。

![叢集循環配置資源 DNS 圖 - 圖下有詳細的替代文字連結](media/fxt-cluster-config/fxt-rrdns-diagram.png) 
[詳細的文字說明](https://azure.github.io/Avere/legacy/Azure-FXT-EdgeFilerDNSconfiguration-alt-text.html)

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

### <a name="enable-dns-in-the-cluster"></a>在叢集中啟用 DNS 

在 [叢集]   > [系統管理網路]  設定頁面中指定叢集所使用的 DNS 伺服器。 該頁面上的設定包括：

* DNS 伺服器位址
* DNS 網域名稱
* DNS 搜尋網域

如需詳細資訊，請閱讀《叢集設定指南》中的 [DNS 設定](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>)。

## <a name="next-steps"></a>後續步驟

這是 Azure FXT Edge Filer 叢集的最後一個基本設定步驟。 

* 請在[監視硬體狀態](fxt-monitor.md)中了解系統的 LED 燈和其他指標。
* 請在[掛接叢集](fxt-mount-clients.md)中深入了解用戶端應如何掛接 FXT Edge Filer 叢集。 
* 如需如何操作和管理 FXT Edge Filer 叢集的詳細資訊，請參閱[叢集設定指南](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html)。 