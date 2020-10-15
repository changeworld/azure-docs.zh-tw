---
title: 教學課程：建立 Azure FXT Edge Filer 快取叢集
description: 了解如何使用 Azure FXT Edge Filer 建立混合式儲存體快取叢集，以及如何登入叢集控制台來設定叢集設定。
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 7a471868bac8f5e0623942c0cc1dc4af4e3881e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88185344"
---
# <a name="tutorial-create-the-azure-fxt-edge-filer-cluster"></a>教學課程：建立 Azure FXT Edge Filer 叢集

為您的快取安裝並初始化 Azure FXT Edge Filer 硬體節點之後，使用 FXT 叢集軟體建立快取叢集。 

此教學課程會逐步引導您將硬體節點設定為叢集。 

在本教學課程中，您將了解： 

> [!div class="checklist"]
> * 開始建立叢集之前需要哪些資訊
> * 叢集的管理網路、叢集網路及面向用戶端的網路之間的差異
> * 如何連線到叢集 
> * 如何使用 Azure FXT Edge Filer 節點建立初始叢集
> * 如何登入叢集控制台來設定叢集設定

此程序需要 15 至 45 分鐘的時間，根據您識別 IP 位址和網路資源所需的研究時間長短而定。

## <a name="prerequisites"></a>Prerequisites

開始進行此教學課程之前，請完成下列先決條件：

* 在您的資料中心安裝 Azure FXT Edge Filer 硬體系統 

  建立叢集只需要一個節點，但您必須先[新增至少兩個其他節點](fxt-add-nodes.md)才能設定叢集，並將它備妥以供使用。 

* 將適當的電源和網路線連接到系統  
* 開啟至少一個 Azure FXT Edge Filer 節點的電源，並[設定其根密碼](fxt-node-password.md)

## <a name="gather-information-for-the-cluster"></a>收集叢集資訊 

您需要下列資訊來建立 Azure FXT Edge Filer 叢集：

* 叢集名稱

* 要為叢集設定的系統管理密碼

* IP 位址：

  * 用於叢集管理的單一 IP 位址，以及用於管理網路的網路遮罩與路由器
  * 連續 IP 範圍的第一個與最後一個 IP 位址，以供叢集 (節點對節點) 通訊使用。 如需詳細資訊，請參閱下面的 [IP 位址散發](#ip-address-distribution)。 
  * (叢集建立之後，才設定面向用戶端的 IP 位址。)

* 網路基礎結構資訊：

  * 叢集的 DNS 伺服器 IP 位址
  * 叢集的 DNS 網域名稱
  * 叢集 NTP 伺服器的 IP 位址或名稱 (一部伺服器，或是三部或 (含) 以上) 
  * 您是否要在叢集的介面上啟用 IEEE 802.1AX-2008 連結彙總
  * 如果您啟用連結彙總，是否要使用 IEEE 802.3ad (LACP) 動態彙總

您可以在建立叢集之後再設定這些網路基礎結構項目，但最好在建立時進行。 

### <a name="ip-address-distribution"></a>IP 位址散發

Azure FXT Edge Filer 混合式儲存體快取叢集使用三種類別的 IP 位址：

* 管理 IP：用於叢集管理的單一 IP 位址

  此位址可作為存取叢集設定公用程式 (網頁式控制台或 XML-RPC API) 的進入點。 此位址會自動指派給叢集中的主要節點，並在主要節點變更時自動隨之移動。

  其他 IP 位址可用來存取控制台，但管理 IP 位址的設計是即使個別節點容錯移轉，也能提供存取。

* 叢集網路：可供叢集通訊使用的 IP 位址範圍

  叢集網路可用來讓叢集節點之間通訊，以及從後端儲存體 (核心檔案管理工具) 擷取檔案。

  **最佳做法：** 為每個實體連接埠都配置一個 IP 位址，以供每個 Azure FXT Edge Filer 節點進行叢集通訊。 叢集會自動將指定範圍內的位址指派給個別節點。

* 面向用戶端的網路：用戶端用來要求及寫入檔案的 IP 位址範圍

  用戶端網路位址可由用戶端用來透過叢集存取核心檔案管理工具資料。 例如，NFS 用戶端可能會裝載其中一個位址。

  **最佳做法：** 為每個實體連接埠都配置一個 IP 位址，以供每個 FXT 系列節點進行用戶端通訊。

  叢集會儘可能在其構成節點之間平均散發面向用戶端的 IP 位址。

  為了簡單起見，許多系統管理員會使用循環配置資源 DNS (RRDNS) 設定，設定單一的 DNS 名稱，以更輕鬆地將用戶端要求散發到位址範圍。 此設定可讓所有用戶端使用相同的裝載命令來存取叢集。 如需詳細資訊，請參閱[設定 DNS](fxt-configure-network.md#configure-dns-for-load-balancing)。

必須指定管理 IP 位址和叢集網路位址範圍，才能建立新叢集。 叢集建立之後，才指定面向用戶端的 IP 位址。

## <a name="connect-to-the-first-node"></a>連線到第一個節點

您可以連線到任何已安裝的 FXT 節點，並使用其 OS 軟體來設定叢集。

如果您尚未這麼做，請為叢集開啟至少一個 FXT 節點的電源，並確定它有網路連線與 IP 位址。 您必須設定新的根密碼以啟動節點，如果您尚未這麼做，請依照[設定硬體密碼](fxt-node-password.md)中的步驟執行。

若要檢查網路連線，請確定節點的網路連結 LED 燈 (如有必要的話，還有所連接網路交換器上的指示燈) 亮起。 如需 LED 指示燈的說明，請參閱[監視 Azure FXT Edge Filer 硬體狀態](fxt-monitor.md)。

當節點開機時會要求 IP 位址。 如果它已連線到 DHCP 伺服器，則會接受 DHCP 提供的 IP 位址。 (這是臨時 IP 位址。 它會在您建立叢集時變更。)

如果它未連線到 DHCP 伺服器或未收到回應，節點會使用 Bonjour 軟體以 169.254.\*.\* 的格式設定自我指派的 IP 位址。 不過，在用來建立叢集之前，您應該在其中一個節點網路卡上設定臨時靜態 IP 位址。 此舊版文件包含相關指示；如需更新的資訊，請連絡 Microsoft 服務及支援：[附錄 A：設定 FXT 節點上的靜態 IP 位址](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html) \(英文\)。

### <a name="find-the-ip-address"></a>尋找 IP 位址

連線到 Azure FXT Edge Filer 節點，以尋找其 IP 位址。 您可以使用序列纜線直接連接到 USB 和 VGA 連接埠，或透過 KVM 交換器連接。 (如需連接埠連線詳細資料，請參閱[設定初始密碼](fxt-node-password.md)。)

連線之後，以使用者名稱 `root` 和您第一次將節點開機時設定的密碼來登入。  

登入之後，您需要判斷該節點的 IP 位址。

使用命令 `ifconfig`，即可查看指派給此系統的位址。

例如，命令 `ifconfig | grep -B5 inet` 可搜尋具有網際網路位址的連接埠，並提供五行的內容以顯示連接埠識別碼。

請記下 ifconfig 報告中顯示的任何 IP 位址。 所列出的位址若有 e0a 或 e0b 等諸如此類的連接埠名稱，都是不錯的選項。 請勿使用具有 e7 * 名稱的任何 IP 位址，因為這些名稱只適用於 iDRAC/IPMI 服務連接埠。  

## <a name="load-the-cluster-configuration-wizard"></a>載入叢集設定精靈

使用瀏覽器式叢集設定工具來建立叢集。 

在網頁瀏覽器中，輸入節點的 IP 位址。 如果瀏覽器發出一則有關網站未受信任的訊息，請繼續前往該網站。 (個別的 Azure FXT Edge Filer 節點不會有 CA 提供的安全性憑證。)

![瀏覽器視窗中的控制台登入頁面](media/fxt-cluster-create/unconfigured-node-gui.png)

請將 [使用者名稱]  與 [密碼]  欄位保留空白。 按一下 [登入]  以載入叢集建立頁面。

![瀏覽器型 GUI 控制台中未設定節點的初始設定畫面。 它會顯示更新軟體、手動設定叢集，或從安裝檔案設定叢集的選項。](media/fxt-cluster-create/setup-first-screen.png)

## <a name="create-the-cluster"></a>建立叢集

叢集設定工具會引導您完成建立 Azure FXT Edge Filer 叢集的一組畫面。 開始之前，請先備妥[必要資訊](#gather-information-for-the-cluster)。 

### <a name="creation-options"></a>建立選項

第一個畫面提供三個選項。 除非您有支援人員的特殊指示，否則請使用手動設定選項。

按一下 [我要手動設定叢集]  ，以載入新的叢集設定選項畫面。 

其他選項都很少使用：

* [更新系統映像] 會提示您先安裝新的作業系統軟體，再建立叢集。 (畫面頂端會列出目前安裝的軟體版本。)您必須提供軟體套件檔案 - URL 與使用者名稱/密碼，或從電腦上傳檔案。 

* Microsoft 客戶服務及支援有時會使用叢集安裝檔案選項。 

## <a name="cluster-options"></a>叢集選項

下一個畫面會提示您設定新叢集的選項。

頁面分割成兩個主要區段，[基本設定]  與 [網路設定]  。 網路設定區段也有子區段：一個用於**管理**網路，另一個用於**叢集**網路。

### <a name="basic-configuration"></a>基本設定

在頂端區段中，填入新叢集的基本資訊。

![瀏覽器 GUI 頁面中 [基本設定] 區段的詳細資料。 它會顯示三個欄位 (叢集名稱、系統管理員密碼、確認密碼)](media/fxt-cluster-create/basic-configuration.png) 

* **叢集名稱** - 請輸入叢集的唯一名稱。

  叢集名稱必須符合下列準則：
  
  * 長度為 1 到 16 個字元
  * 可以包含字母、數字與虛線 (-) 和底線 (_) 字元 
  * 不能包含其他標點符號或特殊字元
  
  您可以稍後在 [叢集]   > [一般設定]  設定頁面上變更此名稱。 (如需叢集設定的詳細資訊，請閱讀[叢集設定指南](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html)，這不是此文件集的一部分。)

  > [!NOTE] 
  > 叢集名稱是用來識別上傳的系統資訊以供監視或疑難排解使用，所以最好可以包含公司名稱。

* **系統管理員密碼** - 設定預設系統管理使用者 `admin` 的密碼。
  
  您應該為管理叢集的每個人員設定個別的使用者帳戶，但您無法移除使用者 `admin`。 如果您需要建立其他使用者，請以 `admin` 的身分登入。
 
  您可以在叢集控制台中的 [管理]   > [使用者]  設定頁面變更 `admin` 的密碼。 如需詳細資訊，請參閱[叢集設定指南](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_users.html)中的＜使用者＞  文件。

<!-- to do: update "legacy" URLs when docs are ported to Microsoft site -->

### <a name="network-configuration"></a>網路組態

[網路]  區段會提示您指定叢集將使用的網路基礎結構。 

要設定兩個不同的網路：

* 「管理網路」  可讓系統管理員存取叢集以進行設定及監視。 連線到控制台或進行 SSH 存取時，就會使用這裡指定的 IP 位址。 

  大部分叢集只使用一個管理 IP 位址，但如果您想要新增介面，可在建立叢集之後進行。

* 「叢集網路」  可用於讓叢集節點之間的通訊，以及叢集節點與後端儲存體 (核心檔案管理工具) 之間的通訊。

稍後會在建立叢集之後，才設定面向用戶端的網路。

此節也包含兩個網路所使用 DNS 與 NTP 伺服器適用的設定。

### <a name="configure-the-management-network"></a>設定管理網路

[管理]  區段中的設定是用於讓系統管理員存取叢集的網路。

![[管理] 區段的詳細資料，其中包含 5 個選項的欄位和 1Gb 管理網路的核取方塊](media/fxt-cluster-create/management-network-filled.png)

* **管理 IP** - 指定您將用來存取叢集控制台的 IP 位址。 叢集的主要節點會宣告此位址，但如果原始的主要節點變成無法使用，就會自動移至狀況良好的節點。

  大部分叢集只使用一個管理 IP 位址。 如果您想要更多 IP 位址，可以在建立叢集之後使用 [叢集]   > [系統管理網路]  頁面來新增。 在[叢集設定指南](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html) \(英文\) 深入了解。

* **網路遮罩** - 指定管理網路的網路遮罩。

* **路由器** - 輸入管理網路所使用的預設閘道位址。

* **VLAN 標籤 (選用)** - 如果叢集使用 VLAN 標籤，請指定管理網路的標籤。

  在[叢集]   > [VLAN]  設定頁面中，設定其他 VLAN 設定。 閱讀《叢集設定指南》中的[使用 VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) \(英文\) 與 [叢集 > VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vlan.html) \(英文\) 以深入了解。

* **MTU** - 必要時，請為叢集的管理網路調整傳輸單元最大值 (MTU)。

* **使用 1Gb 管理網路** - 如果您只想要將 FXT 節點上的兩個 1GbE 網路連接埠指派給管理網路，請選取此方塊。 (您必須提供 25GbE/10GbE 連接埠，用於所有其他流量。)如果您不選取此方塊，管理網路會使用速度最快的可用連接埠。 

### <a name="configure-the-cluster-network"></a>設定叢集網路 

叢集網路設定會套用到叢集節點之間的流量，以及叢集節點與核心檔案管理工具之間的流量。

![[叢集] 區段的詳細資料，其中包含輸入六個值的欄位](media/fxt-cluster-create/cluster-network-filled.png)

* **第一個 IP** 與**最後一個 IP** - 輸入定義範圍的 IP 位址，以用於進行內部叢集通訊。 這裡必須使用連續且非由 DHCP 指派的 IP 位址。

  您可以在建立叢集之後，新增更多 IP 位址。 使用 [叢集]   > [叢集網路]  設定頁面 ([叢集設定指南文件](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_networks.html#gui-cluster-networks))。

  計算 [範圍中的 IP 數目]  中的值並自動顯示。

* **非管理網路遮罩 (選用)** - 指定叢集網路的網路遮罩。 

  系統會自動建議您為管理網路輸入的網路遮罩值，請視需要予以變更。

* **叢集路由器 (選用)** - 指定叢集網路使用的預設閘道位址。 

  系統會自動建議您為管理網路提供的相同閘道位址。

* **叢集 VLAN 標籤 (選用)** - 如果叢集使用 VLAN 標籤，請指定叢集網路的標籤。

* **非管理 MTU (選用)** - 必要時，請為叢集網路調整傳輸單元最大值 (MTU)。

### <a name="configure-cluster-dns-and-ntp"></a>設定叢集 DNS 和 NTP 

[叢集]  區段下方的欄位可用於指定 DNS 和 NTP 伺服器，以及啟用連結彙總。 這些設定會套用到叢集使用的所有網路。

![用於 DNS/NTP 設定的區段詳細資料，其中包含 DNS 伺服器的三個欄位、DNS 網域與 DNS 搜尋的欄位、NTP 伺服器的三個欄位，以及連結彙總選項的下拉式功能表](media/fxt-cluster-create/dns-ntp-filled.png)

* **DNS 伺服器** - 輸入網域名稱系統 (DNS) 伺服器的一或多個 IP 位址。

  建議所有叢集都使用 DNS，而且如果您想要使用 SMB、AD 或 Kerberos，則為 DNS 為必要項目。 
  
  為獲得最佳效能，請依照[設定 Azure FXT Edge Filer 叢集的 DNS](fxt-configure-network.md#configure-dns-for-load-balancing) 中所述，設定叢集的 DNS 伺服器以提供循環配置資源負載平衡。

* **DNS 網域** - 輸入叢集要使用的網路網域名稱。

* **DNS 搜尋** - (選用) 輸入系統應搜尋以解析 DNS 查詢的其他網域名稱。 您可以新增最多六個以空格分隔的網域名稱。

* **NTP 伺服器** - 在提供的欄位中，指定一或三個網路時間通訊協定 (NTP) 伺服器。 您可以使用主機名稱或 IP 位址。

* **連結彙總** - 連結彙總可讓您自訂叢集 FXT 節點上的乙太網路連接埠如何處理各種類型的流量。 若要深入了解，請參閱《叢集設定指南》中的[連結彙總](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_general_setup.html#link-aggregation) \(英文\)。

### <a name="click-the-create-button"></a>按一下建立按鈕

在表單中提供所有必要的設定之後，按一下 [建立叢集]  按鈕。

![已完成表單底部，其中有游標放在右下方建立按鈕上方](media/fxt-cluster-create/create-cluster.png)

建立叢集時，系統會顯示一則訊息。

![瀏覽器中的叢集設定狀態訊息：「FXT 節點目前正在建立叢集。 這將需要幾分鐘的時間。 建立叢集時，請瀏覽此連結來完成設定。」 「瀏覽此連結」為超連結](media/fxt-cluster-create/creating-message.png)

幾分鐘後，您可以按一下訊息中的連結，以前往叢集控制台。 (此連結會帶您前往 [管理 IP]  中指定的 IP 位址。)按一下建立按鈕後，需要 15 秒到一分鐘的時間，連結才會運作。 如果 Web 介面未載入，請多等候幾秒，然後再按一次連結。 

建立叢集需要一分鐘或幾分鐘的時間，但您可以在程序進行時登入控制台。 在叢集建立程序完成之前，控制台的儀表板頁面顯示警告，都是正常情況。 

## <a name="open-the-settings-pages"></a>開啟 [設定] 頁面 

建立叢集之後，您需要為網路與工作流程自訂其設定。 

使用控制台 Web 介面設定您的新叢集。 依照叢集建立狀態畫面中的連結，瀏覽到叢集上設定的管理 IP 位址。

以您在建立叢集時設定的使用者名稱 `admin` 與密碼登入 Web 介面。

![顯示控制台登入欄位的網頁瀏覽器](media/fxt-cluster-create/admin-login.png)

控制台隨即開啟，並顯示 [儀表板]  頁面。 當叢集建立完成時，應會清除畫面中的任何警告訊息。

按一下 [設定]  索引標籤來設定叢集。

在 [設定]  索引標籤上，左側資訊看板會顯示設定頁面的功能表。 依類別來組織頁面。 按一下類別名稱頂端的 + 或 - 控制項以展開或隱藏個別頁面。

![控制台的 [設定] 索引標籤 (在瀏覽器中)，其中已載入 [叢集] > [一般設定] 頁面](media/fxt-cluster-create/settings-tab-populated.png)

## <a name="cluster-setup-steps"></a>叢集設定步驟

程序進行到此時，您的叢集存在但只有一個節點、沒有面向用戶端的 IP 位址，而且沒有任何後端儲存體。 還需要其他設定步驟，新建立的叢集才能變成準備好可以處理工作流程的快取系統。

### <a name="required-configuration"></a>必要設定

大部分或所有叢集都需要這些步驟。 

* 將節點新增至叢集 

  標準為三個節點，但許多生產環境叢集有更多個，最多可有 24 個節點。

  若要了解如何將其他 Azure FXT Edge Filer 單位新增至叢集，以及啟用高可用性，請參閱[新增叢集節點](fxt-add-nodes.md)。

* 指定後端儲存體

  針對叢集將使用的每個後端儲存體系統，新增「核心檔案管理工具」  定義。 若要深入了解，請參閱[新增後端儲存體並設定虛擬命名空間](fxt-add-storage.md#about-back-end-storage)。

* 設定用戶端存取和虛擬命名空間 

  建立至少一部虛擬伺服器 (vserver)，並為它指派 IP 位址範圍以供用戶端電腦使用。 您也必須設定叢集命名空間 (有時稱為全域命名空間或 GNS)，此虛擬檔案系統功能可讓您將後端儲存體匯出項對應至虛擬路徑。 即使您切換後端儲存體媒體，叢集命名空間還是可為用戶端提供一致且可存取的檔案系統結構。 命名空間也可以為 Azure Blob 容器或其他支援的雲端物件儲存體，提供方便使用的虛擬儲存體階層。

  如需詳細資訊，請參閱[設定命名空間](fxt-add-storage.md#configure-the-namespace)。 此步驟包括：
  * 建立 vserver
  * 設定用戶端網路檢視和後端儲存體之間的連接點 
  * 定義每部 vserver 提供哪些用戶端 IP 位址

  > [!Note] 
  > 開始設定叢集的 GNS 之前，建議進行詳盡的規劃。 請參閱《叢集設定指南》中的[使用全域命名空間](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)英文[建立和使用 VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers) \(英文\) 兩節以取得說明。

* [調整網路設定](fxt-configure-network.md)

  新叢集有幾個網路相關設定應該加以驗證或自訂。 如需下列項目的詳細資訊，請參閱[調整網路設定](fxt-configure-network.md)：

  * 驗證 DNS 與 NTP 設定 
  * 設定目錄服務 (若有需要) 
  * 設定 VLAN
  * 設定 Proxy 伺服器
  * 將 IP 位址新增至叢集網路
  * 儲存加密憑證

* [設定支援監視](#enable-support)

  您必須接受設定工具的隱私權原則，並應該同時設定支援上傳設定。

  叢集可以自動上傳和您叢集相關的疑難排解資料，包括統計資料和偵錯檔案。 這些上傳項目可讓 Microsoft 客戶服務及支援提供可能的最佳服務。 您可以自訂受監視的項目，並選擇性地啟用主動支援和遠端疑難排解服務。  

### <a name="optional-configuration"></a>選用設定

這些並非所有叢集的必要步驟。 某些類型的工作流程或特定叢集管理樣式才需要進行。 

* 自訂節點設定

  您可以在整個叢集層級或個別地設定節點名稱，以及設定節點 IPMI 連接埠。 如果您在將節點新增至叢集之前就設定這些設定，新的節點可在加入時自動取得設定。 選項如舊版的叢集建立文件[自訂節點設定](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_node.html) \(英文\) 中所述。

  > [!TIP]
  > Microsoft Azure 文件網站尚未提供此產品的某些文件。 [叢集組態指南](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html) \(英文\) 與舊版[叢集建立指南](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/create_index.html) \(英文\) 的連結會帶您前往個別的 GitHub 裝載網站。 

* 設定 SMB

  如果您想讓叢集允許 SMB 存取以及 NFS，您必須設定 SMB 並將它開啟。 SMB (有時稱為 CIFS) 通常用來支援 Microsoft Windows 用戶端。

  規劃並設定 SMB 不光是按一下 [控制台] 中的幾個按鈕。 根據系統的需求，SMB 會影響您如何定義核心檔案管理工具、建立的 vserver 數、如何設定連接點與命名空間、存取權限以及其他設定。

  如需詳細資訊，請參閱《叢集設定指南》的[設定 SMB 存取](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/smb_overview.html) \(英文\) 一節。

* 安裝其他授權

  如果您想要使用 Azure Blob 以外的雲端儲存體，您必須安裝額外功能授權。 如需購買 FlashCloud<sup>TM</sup> 授權的詳細資訊，請連絡您的 Microsoft 業務代表。 [新增後端儲存體並設定虛擬命名空間](fxt-add-storage.md#about-back-end-storage)中會詳細說明。


### <a name="enable-support"></a>啟用支援

Azure FXT Edge Filer 可以自動上傳有關您叢集的支援資料。 這些上傳項目可讓人員提供可能的最佳服務。

請依照下列步驟來設定支援上傳項目。

1. 瀏覽至 [叢集]   > [支援]  設定頁面。 接受隱私權原則。 

   ![顯示控制台與快顯視窗的螢幕擷取畫面，視窗上有可接受隱私權原則的 [確認] 按鈕](media/fxt-cluster-create/fxt-privacy-policy.png)

1. 按一下 [Customer Info] \(客戶資訊\)  左側的三角形以展開該區段。
1. 按一下 [Revalidate upload information] \(重新驗證上傳資訊\)  按鈕。
1. 在 [Unique Cluster Name] \(唯一叢集名稱\)  中設定叢集的支援名稱 - 請確定它可讓支援人員唯一識別您的叢集。
1. 勾選 [Statistics Monitoring] \(統計資料監視\)  、[General Information Upload] \(一般資訊上傳\)  及 [Crash Information Upload] \(當機資訊上傳\)  方塊。
1. 按一下 [提交]  。  

   ![包含支援設定頁面上已完成的客戶資訊區段的螢幕擷取畫面](media/fxt-cluster-create/fxt-support-info.png)

1. 按一下 [Secure Proactive Support (SPS)] \(安全的主動式支援 (SPS)\)  左側的三角形以展開該區段。
1. 勾選 [Enable SPS Link] \(啟用 SPS 連結\)  方塊。
1. 按一下 [提交]  。

   ![包含支援設定頁面上已完成的安全主動式支援區段的螢幕擷取畫面](media/fxt-cluster-create/fxt-support-sps.png)

## <a name="next-steps"></a>後續步驟

在您建立基本叢集並接受隱私權原則之後，請新增其餘的叢集節點。 

> [!div class="nextstepaction"]
> [新增叢集節點](fxt-add-nodes.md)
