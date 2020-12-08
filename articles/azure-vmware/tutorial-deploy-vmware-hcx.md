---
title: 教學課程 - 部署及設定 VMWare HCX
description: 了解如何為您的 Azure VMware 解決方案私人雲端部署及設定 VMware HCX 解決方案。
ms.topic: tutorial
ms.date: 11/25/2020
ms.openlocfilehash: e50454c2299ed0f7e249563eecac1935a29a9c47
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352453"
---
# <a name="deploy-and-configure-vmware-hcx"></a>部署及設定 VMWare HCX

本文說明如何為您 Azure VMware 解決方案私人雲端部署及設定內部部署 VMware HCX Connector。 VMware HCX 可讓您透過各種移轉類型，將您的 VMware 工作負載移轉至 Azure VMware 解決方案和其他已連線的網站。 因為 Azure VMware 解決方案會部署和設定 HCX 雲端管理員，所以您必須在內部部署 VMware 資料中心內下載、啟用及設定 HCX Connector。

VMware HCX Advanced Connector 已預先部署在 Azure VMware 解決方案中。 最多可支援三個網站連線 (內部部署至雲端或雲端到雲端)。 如果您需要三個以上的網站連線，請提交[支援要求](https://portal.azure.com/#create/Microsoft.Support)以啟用 [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) 附加元件。 此附加元件目前為預覽狀態。 

>[!TIP]
>雖然 VMware Configuration Maximum 工具描述，內部部署連接器與雲端管理員之間的網站配對上限為 25 個，但授權將此值限制為 3 個 (若為進階版) 和 10 個 (若為企業版)。

>[!NOTE]
>VMware HCX Enterprise 會以 Azure VMware 解決方案的預覽服務形式提供。 這是免費的附加元件，並且受限於預覽服務的條款及條件。 當 VMware HCX Enterprise 服務正式發行後，您會收到帳單即將切換的 30 天通知。 您也可以選擇關閉或退出服務。 從 VMware HCX Enterprise 到 VMware HCX Advanced 沒有簡單的降級路徑。 如果您決定要降級，就必須重新部署，因而會產生停機時間。

首先，請檢閱[開始之前](#before-you-begin)、[軟體版本需求](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)和[必要條件](#prerequisites)。 

然後，我們會逐步解說所有必要的程序，藉以：

> [!div class="checklist"]
> * 下載 VMware HCX Connector OVA。
> * 部署內部部署 VMware HCX OVA (VMware HCX Connector)。
> * 啟用 VMware HCX Connector。
> * 將您的內部部署 VMware HCX Connector 與您的 Azure VMware 解決方案 HCX 雲端管理員配對。
> * 設定互連 (網路設定檔、計算設定檔和服務網格)。
> * 藉由檢查設備狀態和驗證移轉的可行性來完成設定。

完成後，請依照本文結尾的建議後續步驟操作。  

## <a name="before-you-begin"></a>開始之前

在準備部署時，建議您檢閱下列 VMware 文件：

* [VMware HCX 使用者指南](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)
* [使用 VMware HCX 遷移虛擬機器](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* [VMware HCX 部署考量](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)
* [VMware 部落格系列 - 雲端移轉](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) 
* [VMware HCX 所需的網路連接埠](https://ports.vmware.com/home/VMware-HCX)


## <a name="prerequisites"></a>先決條件

如果您打算使用 VMware HCX Enterprise，請確定您已透過 Azure VMware 解決方案支援通道要求啟用。


### <a name="on-premises-vsphere-environment"></a>內部部署 vSphere 環境

請確定您的內部部署 vSphere 環境 (來源環境) 符合[最低需求](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)。 

### <a name="network-and-ports"></a>網路和連接埠

* [Azure ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md) 設定於內部部署與 Azure VMware 解決方案 SDDC ExpressRoute 線路之間。

* [所有必要的連接埠](https://ports.vmware.com/home/VMware-HCX)都應開啟，以在內部部署元件與 Azure VMware 解決方案 SDDC 之間進行通訊。

### <a name="ip-addresses"></a>IP 位址

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="download-the-vmware-hcx-connector-ova"></a>下載 VMware HCX Connector OVA

將虛擬設備部署至內部部署 vCenter 之前，您必須先下載 VMware HCX Connector OVA。  

1. 在 Azure 入口網站中，選取 Azure VMware 解決方案私人雲端。 

1. 選取 [管理] > [連線]，然後選取 [HCX] 索引標籤，以識別 Azure VMware 解決方案 HCX 管理員的 IP 位址。 

   :::image type="content" source="media/tutorial-vmware-hcx/find-hcx-ip-address.png" alt-text="VMware HCX IP 位址的螢幕擷取畫面。" lightbox="media/tutorial-vmware-hcx/find-hcx-ip-address.png":::

1. 選取 [管理] > [身分識別]，然後選取 [vCenter 管理員密碼]，以識別密碼。

   > [!TIP]
   > VCenter 密碼是在您設定私人雲端時所定義。 這是您用來登入 Azure VMware 解決方案 HCX 管理員的相同密碼。

   :::image type="content" source="media/tutorial-vmware-hcx/hcx-admin-password.png" alt-text="尋找 hcx 密碼。" lightbox="media/tutorial-vmware-hcx/hcx-admin-password.png":::

1. 開啟瀏覽器視窗，並使用 **cloudadmin\@vsphere.local** 使用者認證登入 `https://x.x.x.9` 連接埠 443 上的 Azure VMware 解決方案 HCX 管理員

1. 選取 [管理] > [系統更新]，然後選取 [要求下載連結]。

1. 選取您選擇的選項，以下載 VMware HCX Connector OVA 檔案。

## <a name="deploy-the-vmware-hcx-connector-ova-on-premises"></a>部署內部部署的 VMware HCX Connector OVA

1. 在您的內部部署 vCenter 中選取 [OVF 範本](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html)，以將 VMware HCX Connector 部署至您的內部部署 vCenter。 

   > [!TIP]
   > 您將選取您在上一節下載的 OVA 檔案。  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="瀏覽至 OVF 範本的螢幕擷取畫面。" lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. 選取名稱和位置，然後選取您要在其中部署 VMware HCX Connector 的資源或叢集。 然後，檢閱詳細資料和必要的資源，並選取 [下一步]。  

1. 檢閱授權條款。 如果您同意，請選取所需的儲存體和網路，然後選取 [下一步]。

1. 選取儲存體，然後選取 [下一步]。

1. 選取您先前在 [IP 位址必要條件](#ip-addresses)一節中定義的 VMware HCX 管理網路區段。  然後，選取 [下一步]。

1. 在 [自訂範本] 中輸入所有必要的資訊，然後選取 [下一步]。 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="自訂範本的方塊螢幕擷取畫面。" lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. 確認設定，然後選取 [完成] 以部署 VMware HCX Connector OVA。
   
   > [!IMPORTANT]
   > 您將需要手動開啟虛擬設備。  在開啟電源後，請等候 10-15 分鐘，再繼續進行下一個步驟。

如需此程序的端對端概觀，請檢視 [Azure VMware 解決方案：HCX 設備部署](https://www.youtube.com/embed/UKmSTYrL6AY)影片。 


## <a name="activate-vmware-hcx"></a>啟用 VMware HCX

部署 VMware HCX Connector OVA 內部部署環境並啟動設備之後，您就可以開始啟用了。 首先，您必須從 Azure VMware 解決方案入口網站取得授權金鑰。

1. 在 Azure VMware 解決方案入口網站中，移至 [管理] > [連線]，選取 [HCX] 索引標籤，然後選取 [新增]。

1. 在 `https://HCXManagerIP:9443` 上，使用 **管理員** 認證登入內部部署 VMware HCX 管理員。 

   > [!TIP]
   > 您已在 VMware HCX 管理員 OVA 檔案部署期間定義了 **管理** 使用者密碼。

   > [!IMPORTANT]
   > 請務必將 `9443` 連接埠號碼包含在 VMware HCX 管理員 IP 位址中。

1. 在 [授權] 中，輸入您的金鑰作為 [HCX 進階金鑰]，然後選取 [啟用]。  
   
    > [!NOTE]
    > VMware HCX 管理員必須有開放的網際網路存取或已設定 Proxy。

1. 在 [資料中心位置] 中，提供用來在內部部署環境中安裝 VMware HCX 管理員的最接近位置。 然後選取 [繼續]  。

1. 在 [系統名稱] 中修改名稱或接受預設值，然後選取 [繼續]。
   
1. 選取 [是，繼續]。

1. 在 [連線您的 vCenter] 中，提供 vCenter 伺服器的 FQDN 或 IP 位址以及適當的認證，然後選取 [繼續]。
   
   > [!TIP]
   > vCenter Server 是您在資料中心部署 VMware HCX Connector 的位置。

1. 在 [設定 SSO/PSC] 中，提供您 Platform Services Controller 的 FQDN 或 IP 位址，然後選取 [繼續]。
   
   > [!NOTE]
   > 一般來說，此項目會與您的 vCenter FQDN 或 IP 位址相同。

1. 確認輸入的資訊正確無誤，然後選取 [重新啟動]。
    
   > [!NOTE]
   > 在系統提示您進行下一個步驟之前，您將會在重新開機之後遇到延遲。

在服務重新開機之後，您會在出現的畫面上看到 vCenter 顯示為綠色。 VCenter 和 SSO 必須具有適當的組態參數，其應該與上一個畫面相同。

:::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="具有綠色 vCenter 狀態的儀表板螢幕擷取畫面。" lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

如需此程序的端對端概觀，請檢視 [Azure VMware 解決方案：啟用 HCX](https://www.youtube.com/embed/jzQZawslF8w) 的影片。

   > [!IMPORTANT]
   > 無論您使用 VMware HCX Advanced 還是 VMware HCX Enterprise，都可能需要從 VMware 的[知識庫文章 81558](https://kb.vmware.com/s/article/81558) 安裝修補程式。 

## <a name="configure-the-vmware-hcx-connector"></a>設定 VMware HCX Connector

現在您已準備好新增網站配對、建立網路和計算設定檔，以及啟用服務，例如移轉、網路延伸模組或災害復原。 

### <a name="add-a-site-pairing"></a>新增網站配對

您可以透過資料中心內的 VMware HCX Connector，連線或配對 Azure VMware 解決方案中的 VMware HCX 雲端管理員。 

1. 登入您的內部部署 vCenter，然後在 [主資料夾] 底下，選取 [HCX]。

1. 在 [基礎結構] 底下，選取 [網站配對]，然後選取 [連線到遠端網站] 選項 (在畫面中間)。 

1. 輸入 Azure VMware 解決方案 HCX 雲端管理員 URL 或您先前記下的 IP 位址 `https://x.x.x.9`、Azure VMware 解決方案 cloudadmin@vsphere.local 使用者名稱和密碼。 然後選取 [連線]  。

   > [!NOTE]
   > 若要成功建立網站配對：
   > * 您的 VMware HCX Connector 必須能夠透過連接埠 443 路由至您的 HCX 雲端管理員 IP。
   >
   > * 使用您用來登入 vCenter 的相同密碼。 您已在初始部署畫面上定義此密碼。

   您會看到一個畫面，其中顯示您 Azure VMware 解決方案中的 VMware HCX 雲端管理員，以及您的內部部署 VMware HCX Connector 已連線 (配對)。

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="此螢幕擷取畫面顯示 Azure VMware 解決方案中的 HCX 管理員與 VMware HCX Connector 的配對。":::

如需此程序的端對端概觀，請檢視 [Azure VMware 解決方案：HCX 網站配對](https://www.youtube.com/embed/iTJtjbiwvsw)的影片。

### <a name="create-network-profiles"></a>建立網路設定檔

VMware HCX Connector 會部署需要多個 IP 區段的幾個虛擬設備 (自動化)。 當您建立網路設定檔時，您會使用在 [VMware HCX 網路區段預先部署準備和規劃階段](production-ready-deployment-steps.md#vmware-hcx-network-segments)期間所識別的 IP 區段。

您將建立四個網路設定檔：

   - 管理
   - vMotion
   - 複寫
   - 上行

1. 在 [基礎結構] 下，選取 [互連] > [多網站服務網格] > [網路設定檔] > [建立網路設定檔]。

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="開始建立網路設定檔的選項螢幕擷取畫面。" lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. 為每個網路設定檔選取網路和連接埠群組，並提供名稱，然後建立該區段的 IP 集區。 然後選取 [建立]。 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="新網路設定檔的詳細資料螢幕擷取畫面。":::

如需此程序的端對端概觀，請檢視 [Azure VMware 解決方案：HCX 網路設定檔](https://www.youtube.com/embed/O0rU4jtXUxc)的影片。


### <a name="create-a-compute-profile"></a>建立計算設定檔

1. 在 [基礎結構] 底下，選取 [互連] > [計算設定檔] > [建立計算設定檔]。

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="顯示開始建立計算設定檔的選項螢幕擷取畫面。" lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. 輸入設定檔的名稱，然後選取 [繼續]。  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="顯示計算設定檔名稱項目和 [繼續] 按鈕的螢幕擷取畫面。" lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. 選取要啟用的服務，例如移轉、網路延伸模組或災害復原，然後選取 [繼續]。
  
   > [!NOTE]
   > 一般來說，這裡沒有任何變更。

1. 在 [選取服務資源] 中，選取要用於啟用所選 VMware HCX 服務的一個或多個服務資源 (叢集)。  

1. 當您看到內部部署資料中心內的叢集時，請選取 [繼續]。

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="顯示所選服務資源和 [繼續] 按鈕的螢幕擷取畫面。" lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. 從 [選取資料存放區] 中，選取用於部署 VMware HCX 互連設備的資料存放區儲存體資源。 然後選取 [繼續]。

   選取多個資源時，VMware HCX 會使用第一個選取的資源，直到其容量用盡為止。   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="顯示所選資料儲存體資源和 [繼續] 按鈕的螢幕擷取畫面。" lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. 從 [選取管理網路設定檔] 中，選取您在先前步驟中建立的管理網路設定檔。 然後選取 [繼續]。  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="顯示管理網路設定檔和 [繼續] 按鈕選項的螢幕擷取畫面。" lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

1. 從 [選取上行網路設定檔] 中，選取您在先前的程序中建立的上行網路設定檔。 然後選取 [繼續]。

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="顯示上行網路設定檔和 [繼續] 按鈕選項的螢幕擷取畫面。" lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. 從 [選取 vMotion 網路設定檔] 中，選取您在先前步驟中建立的 vMotion 網路設定檔。 然後選取 [繼續]。

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="顯示 vMotion 網路設定檔和 [繼續] 按鈕選項的螢幕擷取畫面。" lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. 從 [選取 vSphere複寫網路設定檔] 中，選取您在先前步驟中建立的複寫網路設定檔。 然後選取 [繼續]。

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="顯示複寫網路設定檔和 [繼續] 按鈕選項的螢幕擷取畫面。" lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. 從 [選取網路擴充功能的分散式交換器] 中選取交換器，其中包含要遷移至第 2 層擴充網路上的 Azure VMware 解決方案的虛擬機器。 然後選取 [繼續]  。

   > [!NOTE]
   > 如果您不要在第 2 層擴充網路上遷移虛擬機器，可以略過此步驟。
   
   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="顯示分散式虛擬交換器和 [繼續] 按鈕選項的螢幕擷取畫面。" lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. 檢閱連線規則，然後選取 [繼續]。  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="顯示連線規則和 [繼續] 按鈕的螢幕擷取畫面。" lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. 選取 [完成] 以建立計算設定檔。


   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="顯示計算設定檔資訊的螢幕擷取畫面。" lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

如需此程序的端對端概觀，請檢視 [Azure VMware 解決方案：計算設定檔](https://www.youtube.com/embed/e02hsChI3b8)的影片。

### <a name="create-a-service-mesh"></a>建立服務網格

現在可以開始在內部部署與 Azure VMware 解決方案 SDDC 之間設定服務網格。



> [!NOTE]
> 若要使用 Azure VMware 解決方案成功建立服務網格：
>
> * 連接埠 UDP 500/4500 會在內部部署 VMware HCX Connector 的「上行」網路設定檔位址與 Azure VMware 解決方案 HCX 雲端「上行」網路設定檔位址之間開啟。
>
> * 請務必參閱 [VMware HCX 所需的連接埠](https://ports.vmware.com/home/VMware-HCX)。

1. 在 [基礎結構] 下，選取 [互連] > [服務網格] > [建立服務網格]。    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="開始建立服務網格的選項螢幕擷取畫面。" lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. 檢閱預先填入的網站，然後選取 [繼續]。 

   > [!NOTE]
   > 如果這是您的第一個服務網格設定，則不需要修改此畫面。  

1. 從下拉式清單選取來源和遠端計算設定檔，然後選取 [繼續]。  

   這些選項會定義 VM 可使用 VMware HCX 服務的資源。  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="顯示選取來源計算設定檔的螢幕擷取畫面。" lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="顯示選取遠端計算設定檔的螢幕擷取畫面。" lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. 檢閱將啟用的服務，然後選取 [繼續]。  

1. 在 [進階設定 - 覆寫上行連結網路設定檔] 中選取 [繼續]。  

   上行連結網路設定檔會連線到可觸達遠端網站的互連設備所用的網路。  
  
1. 在 [進階組態 - 網路延伸模組設備擴增] 中，檢閱並選取 [繼續]。 

   每個設備最多可以有 8 個 VLAN，但您可以部署另一個設備，並另行新增 8 個 VLAN。 您也必須有 IP 空間供額外的設備使用，每個設備各一個 IP。  如需詳細資訊，請參閱 [VMware HCX 設定限制](https://configmax.vmware.com/guest?vmwareproduct=VMware%20HCX&release=VMware%20HCX&categories=41-0,42-0,43-0,44-0,45-0)。
   
   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png" alt-text="顯示要在何處增加 VLAN 計數的螢幕擷取畫面。" lightbox="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png":::

1. 在 [進階組態 - 流量工程] 中，檢閱並進行任何您認為必要的修改，然後選取 [繼續]。

1. 檢閱拓撲預覽，然後選取 [繼續]。

1. 輸入此服務網格的好記名稱，然後選取 [完成] 以完成。  

1. 選取 [檢視工作] 監視部署。 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="顯示檢視工作按鈕的螢幕擷取畫面。":::

   當服務網格部署成功完成時，您會看到服務為綠色。

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="顯示服務上綠色指示器的螢幕擷取畫面。" lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. 檢查設備狀態，以確認服務網格的健康情況。 

1. 選取 [互連] > [設備]。

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="顯示用來檢查設備狀態的選項螢幕擷取畫面。" lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

如需此程序的端對端概觀，請檢視 [Azure VMware 解決方案：服務網格](https://www.youtube.com/embed/COY3oIws108)的影片。

### <a name="optional-create-a-network-extension"></a>(選擇性) 建立網路延伸模組

如果您想要將內部部署環境中的任何網路延伸至 Azure VMware 解決方案，請遵循下列步驟：

1. 在 [服務] 底下，選取 [網路擴充功能] > [建立網路擴充功能]。

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="顯示用來開始建立網路延伸模組的選項螢幕擷取畫面。" lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. 選取您想要延伸至 Azure VMware 解決方案的每個網路，然後選取 [下一步]。

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="顯示網路選取項目的螢幕擷取畫面。":::

1. 針對您要延伸的每個網路輸入內部部署閘道 IP，然後選取 [提交]。 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="顯示閘道 IP 位址項目的螢幕擷取畫面。":::

   網路延伸模組需要幾分鐘的時間才能完成。 當其執行時，您會看到狀態變更為 [延伸模組完成]。

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="顯示延伸模組完成狀態的螢幕擷取畫面。" lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

如需此程序的端對端概觀，請檢視 [Azure VMware 解決方案：網路延伸模組](https://www.youtube.com/embed/gYR0nftKui0)的影片。


## <a name="next-steps"></a>後續步驟

如果設備互連的通道狀態為 [啟動] 和綠色，您可以使用 VMware HCX 來遷移和保護 Azure VMware 解決方案 VM。 Azure VMware 解決方案支援工作負載移轉 (使用或不使用網路延伸模組)。 您仍可在 vSphere 環境中遷移工作負載，並在內部部署環境中建立網路及將 VM 部署到這些網路。  

如需有關使用 HCX 的詳細資訊，請移至 VMware 技術文件：

* [VMware HCX 文件](https://docs.vmware.com/en/VMware-HCX/index.html)
* [使用 VMware HCX 遷移虛擬機器](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* [HCX 所需的連接埠](https://ports.vmware.com/home/VMware-HCX)
