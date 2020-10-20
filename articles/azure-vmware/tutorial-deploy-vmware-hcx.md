---
title: 教學課程 - 部署及設定 VMWare HCX
description: 了解如何為您的 Azure VMware 解決方案私人雲端部署及設定 VMware HCX 解決方案。
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 58fd8b4518f60f1f736d8c19ddcf62729353f251
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057990"
---
# <a name="deploy-and-configure-vmware-hcx"></a>部署及設定 VMWare HCX

在本文中，我們將逐步解說為 Azure VMware 解決方案私人雲端部署及設定 VMware HCX 內部部署 Connector 的程序。 VMware HCX 可讓您透過各種移轉類型，將您的 VMware 工作負載移轉至 Azure VMware 解決方案和其他已連線的網站。 Azure VMware 解決方案已部署並設定「雲端管理員」，因此客戶必須在其內部部署 VMware 資料中心下載、啟用和設定 Connector。

VMware HCX Advanced Connector (預先部署在 Azure VMware 解決方案中) 支援最多三個網站連線 (內部部署至雲端或雲端到雲端)。 如果需要三個以上的網站連線，客戶可以選擇提交[支援要求](https://rc.portal.azure.com/#create/Microsoft.Support)，藉以啟用 [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) 附加元件 (目前為「預覽」)。 VMware HCX Enterprise Edition (EE) 可供使用，並有 Azure VMware 解決方案做為「預覽」函式/服務。 雖然適用於 Azure VMware 的 VMware HCX EE 解決方案處於「預覽」，但其為免費的函式/服務，並受預覽版服務條款及條件的約束。 一旦 VMware HCX EE 服務正式發行後，您將會收到 30 天的通知，指出帳單將會切換。 您也可以選擇關閉/退出服務。

開始之前，請仔細檢閱[在開始之前](#before-you-begin)、[軟體版本需求](#software-version-requirements)和[先決條件](#prerequisites)。 

然後，我們會逐步解說所有必要的程序，藉以：

> [!div class="checklist"]
> * 部署內部部署 VMware HCX OVA (HCX Connector)
> * 啟用 VMware HCX Connector
> * 將您的內部部署 HCX Connector 與您的 Azure VMware 解決方案 HCX 雲端管理員配對
> * 設定互連 (網路設定檔、計算設定檔和服務網格)
> * 藉由檢查設備狀態和驗證移轉的可行性來完成設定

完成後，您可以遵循本文結尾的建議後續步驟。  

## <a name="before-you-begin"></a>開始之前
   
* 檢閱基本 Azure VMware 解決方案軟體定義資料中心 (SDDC) [教學課程系列](tutorial-network-checklist.md)。
* 檢閱並參考 [VMware HCX 文件](https://docs.vmware.com/en/VMware-HCX/index.html)，包括 HCX 使用者指南。
* 檢閱 VMware 文件[使用 VMware HCX 移轉虛擬機器](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)。
* 可選擇檢閱 [VMware HCX 部署考量因素](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)。
* 可選檢閱 HCX 上的相關 VMware 資料，例如 HCX 上 的 VMware vSphere [部落格系列](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html)。 
* 選擇性地透過 Azure VMware 解決方案支援管道，要求 Azure VMware 解決方案 HCX 企業啟用。
* (選擇性) [檢閱 HCX 所需的網路連接埠](https://ports.vmware.com/home/VMware-HCX)。
* 雖然 Azure VMware 解決方案 HCX 雲端管理員是從為 Azure VMware 解決方案私人雲端提供的 /22 預先設定的，但 HCX 內部部署 Connector 需要客戶從其內部部署網路設定網路範圍。 這些網路和範圍會在文件中進一步說明。

針對計算和儲存體資源調整工作負載的大小，是不可或缺的規劃步驟。 在開始私人雲端環境的規劃時處理調整大小的步驟。 

您也可以在 Azure Migrate 入口網站中完成 [Azure VMware 解決方案評量](../migrate/how-to-create-azure-vmware-solution-assessment.md)，以調整工作負載大小。

## <a name="software-version-requirements"></a>軟體版本需求

基礎結構元件必須執行所需的最低版本。 
                                                         
| 元件類型    | 來源環境需求    | 目的地環境需求   |
| --- | --- | --- |
| vCenter Server   | 5.1<br/><br/>如果使用 5.5 U1 或更早的版本，請使用獨立 HCX 使用者介面來進行 HCX 作業。  | 6.0 U2 及更高版本   |
| ESXi   | 5.0    | ESXi 6.0 及更高版本   |
| NSX    | 針對位於來源的邏輯交換器 HCX 網路延伸模組：NSXv 6.2+ 或 NSX-T 2.4+   | NSXv 6.2+ 或 NSX-T 2.4+<br/><br/>用於 HCX 鄰近性路由：NSXv 6.4 + (不支援以 NSX-T 進行的鄰近性路由) |
| vCloud Director   | 非必要 - 來源網站沒有與 vCloud Director 的互通性 | 整合目的地環境與 vCloud Director 時，最低需求版本為 9.1.0.2。  |

## <a name="prerequisites"></a>必要條件

### <a name="network-and-ports"></a>網路和連接埠

* 在內部部署與 Azure VMware 解決方案 SDDC ExpressRoute 電路之間設定的 [ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md)。

* 所有必要的連接埠都應該開放，以在內部部署元件與內部部署至 Azure VMware 解決方案 SDDC 之間進行通訊 (請參閱 [VMware HCX 文件](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html))。


### <a name="ip-addresses"></a>IP 位址

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="deploy-the-vmware-hcx-connector-ova-on-premises"></a>部署內部部署的 VMware HCX Connector OVA

>[!NOTE]
>將虛擬設備部署至內部部署 vCenter 之前，您必須先下載 VMware HCX Connector OVA。 

1. 開啟瀏覽器視窗，並使用 **cloudadmin** 使用者認證登入 `https://x.x.x.9` 連接埠 443 上的 Azure VMware 解決方案 HCX 管理員，然後移至**支援**。

   >[!TIP]
   >請記下 Azure VMware 解決方案中的 HCX 雲端管理員的 IP。 若要識別 HCX 雲端管理員的 IP 位址，請在 [Azure VMware 解決方案] 刀鋒視窗中，移至 [管理] > [連線]，然後選取 [HCX] 索引標籤。 
   >
   >VCenter 密碼是在您設定私人雲端時所定義。

1. 選取 [下載] 連結以下載 VMware HCX Connector OVA 檔案。

1. 移至內部部署 vCenter 並選取 OVF 範本 (也就是您下載的 OVA 檔案)，將 HCX Connector 部署至您的內部部署 vCenter。  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="移至內部部署 vCenter 並選取 OVF 範本，以部署至您的內部部署 vCenter。" lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. 選取 [名稱] 和 [位置]、要部署 HCX Connector 的資源/叢集，然後檢閱詳細資料和所需的資源。  

   :::image type="content" source="media/tutorial-vmware-hcx/configure-template.png" alt-text="移至內部部署 vCenter 並選取 OVF 範本，以部署至您的內部部署 vCenter。" lightbox="media/tutorial-vmware-hcx/configure-template.png":::

1. 檢閱授權條款，如果您同意，請選取所需的儲存體和網路，然後選取 [下一步]。

1. 在 [自訂範本] 中，輸入全部所需的資訊。 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="移至內部部署 vCenter 並選取 OVF 範本，以部署至您的內部部署 vCenter。" lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. 選取 [下一步]、驗證設定，然後選取 [完成] 以部署 HCX Connector OVA。
     
   >[!NOTE]
   >一般來說，您目前部署的 VMware HCX Connector 會部署至叢集的管理網路。  
   
   > [!IMPORTANT]
   > 部署完成之後，您可能需要手動開啟虛擬設備的電源。
   > 在 HCX 設備開機後等候 10-15 分鐘，以移至下一個步驟。

如需此步驟的端對端概觀，請檢視 [Azure VMware 解決方案 - VMware HCX 設備部署](https://www.youtube.com/embed/BwSnQeefnso)影片。 


## <a name="activate-vmware-hcx"></a>啟用 VMware HCX

部署內部部署的 VMware HCX Connector OVA 並啟動設備之後，您即可開始啟用，但必須先從 Azure 中的 Azure VMware 解決方案入口網站取得授權金鑰。

1. 在 Azure VMware 解決方案入口網站中，移至 [管理] > [連線]，選取 [HCX] 索引標籤，然後選取 [新增]。

1. 在 `https://HCXManagerIP:9443` 登入內部部署 VMware HCX 管理員，並登入**系統管理員**使用者認證。 

   > [!IMPORTANT]
   > 請務必將 `9443` 連接埠號碼包含在 VMware HCX 管理員 IP 位址中。

1. 在 [授權] 中，輸入您的 **HCX 進階金鑰**。  
   
    > [!NOTE]
    > VMware HCX 管理員必須有開放的網際網路存取或已設定 Proxy。

1. 在 [資料中心位置] 中，提供您在內部部署環境中安裝 VMware HCX 管理員的最接近位置。

1. 修改 [系統名稱] 或接受預設值。
   
1. 您可以 [稍後完成] 或 [繼續]，請選取 [是，繼續] 選項以繼續進行。
    
1. 在 [連線您的 vCenter] 中，提供 vCenter 伺服器的 FQDN 或 IP 位址以及適當的認證，然後選取 [繼續]。
   
1. 在 [設定 SSO/PSC] 中，提供您 PSC 的 FQDN 或 IP 位址，然後選取 [繼續]。
   
   >[!NOTE]
   >通常與您的 vCenter FQDN/IP 相同。

1. 確認所有輸入都正確，然後選取 [重新開機]。
    
   > [!NOTE]
   > 在系統提示您進行下一個步驟之前，您將會在重新開機之後遇到延遲。
   >
   >在服務重新開機之後，請務必在出現的畫面上看到 [vCenter] 顯示為綠色。 VCenter 和 SSO 皆具有適當的組態參數，其應該與上一個畫面相同。

   :::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="移至內部部署 vCenter 並選取 OVF 範本，以部署至您的內部部署 vCenter。" lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

如需此步驟的端對端概觀，請檢視 [Azure VMware 解決方案 - VMware HCX 啟用](https://www.youtube.com/embed/BkAV_TNYxdE)影片。


## <a name="configure-vmware-hcx-connector"></a>設定 VMware HCX Connector

現在您已準備好新增網站配對、建立網路和計算設定檔，以及啟用服務，例如移轉、網路延伸模組或災害復原。 

### <a name="add-a-site-pairing"></a>新增網站配對

您可以透過資料中心內的 VMware HCX Connector，連線 (配對) Azure VMware 解決方案中的 VMware HCX 雲端管理員。 

1. 登入您的內部部署 vCenter，然後在 [主資料夾] 底下，選取 [HCX]。

   :::image type="content" source="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png" alt-text="移至內部部署 vCenter 並選取 OVF 範本，以部署至您的內部部署 vCenter。" lightbox="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png":::

1. 在 [基礎結構] 底下，選取 [網站配對]，然後選取 [連線到遠端網站] 選項 (在畫面中間)。 

   :::image type="content" source="media/tutorial-vmware-hcx/connect-remote-site.png" alt-text="移至內部部署 vCenter 並選取 OVF 範本，以部署至您的內部部署 vCenter。" lightbox="media/tutorial-vmware-hcx/connect-remote-site.png":::

1. 輸入您先前記下的**遠端 HCX URL 或 IP 位址**、Azure VMware 解決方案 cloudadmin@vsphere.local 使用者名稱和**密碼**，然後選取 [連線]。

   > [!NOTE]
   > **遠端 HCX URL** 是您的 Azure VMware 解決方案私人雲端 HCX 雲端管理員 IP，通常是管理網路的 ".9" 位址。  例如，如果您的 vCenter 是 192.168.4.2，則您的 HCX URL 將會是 192.168.4.9。
   >
   > **密碼**會與您用來登入 vCenter 的密碼相同。 您已在初始部署畫面上定義此密碼。

   您會看到一個畫面，其中顯示您 Azure VMware 解決方案中的 HCX 雲端管理員，以及您的 HCX Connector 內部部署連線 (配對)。

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="移至內部部署 vCenter 並選取 OVF 範本，以部署至您的內部部署 vCenter。":::

如需此步驟的端對端概觀，請檢視 [Azure VMware 解決方案 - VMware HCX 網站配對](https://www.youtube.com/embed/sKizDCRHOko)影片。



### <a name="create-network-profiles"></a>建立網路設定檔

VMware HCX 會部署需要多個 IP 區段的幾個虛擬設備 (自動化)。  當您建立網路設定檔時，您會定義在 [VMware HCX 網路區段預先部署準備和規劃階段](production-ready-deployment-steps.md#vmware-hcx-network-segments)期間所識別的 IP 區段。

您將建立四個網路設定檔：

   - 管理
   - vMotion
   - 複寫
   - 上行

1. 在 [基礎結構] 下，選取 [互連] > [多網站服務網格] > [網路設定檔] > [建立網路設定檔]。

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="移至內部部署 vCenter 並選取 OVF 範本，以部署至您的內部部署 vCenter。" lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. 針對每個網路設定檔，選取網路、連接埠群組、提供名稱、建立該特定區段的 IP 集區，然後選取 [建立]。 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="移至內部部署 vCenter 並選取 OVF 範本，以部署至您的內部部署 vCenter。":::

如需此步驟的端對端概觀，請檢視 [Azure VMware 解決方案 - VMware HCX 建立網路設定檔](https://www.youtube.com/embed/NhyEcLco4JY)影片。


### <a name="create-compute-profile"></a>建立計算設定檔

1. 選取 [計算設定檔] > [建立計算設定檔]。

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="移至內部部署 vCenter 並選取 OVF 範本，以部署至您的內部部署 vCenter。" lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. 輸入設定檔的名稱，然後選取 [繼續]。  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="移至內部部署 vCenter 並選取 OVF 範本，以部署至您的內部部署 vCenter。" lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. 選取要啟用的服務，例如移轉、網路延伸模組或災害復原，然後選取 [繼續]。
  
   > [!NOTE]
   > 一般來說，這裡不會有任何變更。

1. 在 [選取服務資源] 中，選取要針對所選 VMware HCX 服務啟用的一個或多個服務資源 (叢集)。  

1. 當您看到內部部署資料中心內的叢集時，請選取 [繼續]。

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="移至內部部署 vCenter 並選取 OVF 範本，以部署至您的內部部署 vCenter。" lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. 從 [選取資料存放區]，選取用於部署 VMware HCX 互連設備的資料存放區儲存體資源，然後選取 [繼續]。

   選取多個資源時，VMware HCX 會使用第一個選取的資源，直到其容量用盡為止。   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="移至內部部署 vCenter 並選取 OVF 範本，以部署至您的內部部署 vCenter。" lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. 選取 [管理網路設定檔]，這是您在先前步驟中建立的管理網路設定檔，然後選取 [繼續]。  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="移至內部部署 vCenter 並選取 OVF 範本，以部署至您的內部部署 vCenter。" lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

   > [!NOTE]
   > 管理網路設定檔可讓 VMware HCX 設備與 vCenter 通訊，且可以連線到 ESXi 主機。

1. 選取 [上行網路設定檔]，這是您在先前步驟中建立的上行網路設定檔，然後選取 [繼續]。

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="移至內部部署 vCenter 並選取 OVF 範本，以部署至您的內部部署 vCenter。" lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. 選取 [vMotion 網路設定檔]，這是您在先前步驟中建立的 vMotion 網路設定檔，然後選取 [繼續]。

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="移至內部部署 vCenter 並選取 OVF 範本，以部署至您的內部部署 vCenter。" lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. 選擇 [vSphere 複寫網路設定檔]，這是您在先前步驟中建立的複寫網路設定檔，然後選擇 [繼續]。

   在大多數情況下，複寫網路設定檔與管理網路設定檔相同。  

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="移至內部部署 vCenter 並選取 OVF 範本，以部署至您的內部部署 vCenter。" lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. 選取 [網路延伸模組的分散式交換器]，這是分散式虛擬交換器，其包含要遷移至第 2 層擴充網路上 Azure VMware 解決方案的虛擬機器，然後選取 [繼續]。

   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="移至內部部署 vCenter 並選取 OVF 範本，以部署至您的內部部署 vCenter。" lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. 檢閱連線規則，然後選取 [繼續]。  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="移至內部部署 vCenter 並選取 OVF 範本，以部署至您的內部部署 vCenter。" lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. 選取 [完成] 以建立計算設定檔。

   您會看到類似下面所示的畫面。

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="移至內部部署 vCenter 並選取 OVF 範本，以部署至您的內部部署 vCenter。" lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

如需此步驟的端對端概觀，請檢視 [Azure VMware 解決方案 - VMware HCX 建立計算設定檔](https://www.youtube.com/embed/qASXi5xrFzM)影片。




### <a name="create-service-mesh"></a>建立服務網格

現在可以開始在內部部署與 Azure VMware 解決方案 SDDC 之間設定服務網格。

1. 在 [基礎結構] 下，選取 [互連] > [服務網格] > [建立服務網格]。    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="移至內部部署 vCenter 並選取 OVF 範本，以部署至您的內部部署 vCenter。" lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. 檢閱預先填入的網站，然後選取 [繼續]。 

   >[!NOTE]
   >如果這是您的第一個服務網格設定，則不需要修改此畫面。  

1. 選取來源和遠端計算設定檔的下拉式選單，然後選取 [繼續]。  

   這些選項會定義 VM 可使用 VMware HCX 服務的資源。  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="移至內部部署 vCenter 並選取 OVF 範本，以部署至您的內部部署 vCenter。" lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="移至內部部署 vCenter 並選取 OVF 範本，以部署至您的內部部署 vCenter。" lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. 檢閱將啟用的服務，然後選取 [繼續]。  

1. 在 [進階設定 - 覆寫上行連結網路設定檔] 中選取 [繼續]。  上行連結網路設定檔會連線到可觸達遠端網站的互連設備所用的網路。  
  
1. 在 [進階組態 - 網路延伸模組設備擴增] 中，檢閱並選取 [繼續]。 

1. 在 [進階組態 - 流量工程] 中，檢閱並進行任何您認為必要的修改，然後選取 [繼續]。

1. 檢閱拓撲預覽，然後選取 [繼續]。

1. 輸入此服務網格的好記名稱，然後選取 [完成] 以完成。  

1. 選取 [檢視工作] 監視部署。 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="移至內部部署 vCenter 並選取 OVF 範本，以部署至您的內部部署 vCenter。":::

   當服務網格部署成功完成時，您會看到服務為綠色。

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="移至內部部署 vCenter 並選取 OVF 範本，以部署至您的內部部署 vCenter。" lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. 檢查設備狀態以確認服務網格的健全狀況，選取 [互連] > [設備]。

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="移至內部部署 vCenter 並選取 OVF 範本，以部署至您的內部部署 vCenter。" lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

如需此步驟的端對端概觀，請檢視 [Azure VMware 解決方案 - VMware HCX 建立服務網格](https://www.youtube.com/embed/FyZ0d3P_T24)影片。



### <a name="optional-create-a-network-extension"></a>(選擇性) 建立網路延伸模組

如果您想要將內部部署環境中的任何網路延伸至 Azure VMware 解決方案，請遵循下列步驟來延伸這些網路。

1. 在 [服務] 底下，選取 [網路延伸模組]，然後選取 [建立網路延伸模組]。

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="移至內部部署 vCenter 並選取 OVF 範本，以部署至您的內部部署 vCenter。" lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. 選取您想要延伸至 Azure VMware 解決方案的每個網路，然後選取 [下一步]。

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="移至內部部署 vCenter 並選取 OVF 範本，以部署至您的內部部署 vCenter。":::

1. 針對您要延伸的每個網路輸入內部部署閘道 IP，然後選取 [提交]。 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="移至內部部署 vCenter 並選取 OVF 範本，以部署至您的內部部署 vCenter。":::

   網路延伸模組需要幾分鐘的時間才能完成。 當其執行時，您會看到狀態變更為 [延伸模組完成]。

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="移至內部部署 vCenter 並選取 OVF 範本，以部署至您的內部部署 vCenter。" lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

如需此步驟的端對端概觀，請檢視 [Azure VMware 解決方案 - VMware HCX 網路延伸模組](https://www.youtube.com/embed/cNlp0f_tTr0)影片。


## <a name="next-steps"></a>後續步驟

如果您已達到這個點，且設備互連 [通道狀態] 為 [啟動] 和綠色，您可以使用 VMware HCX 來遷移和保護 Azure VMware 解決方案 VM。  Azure VMware 解決方案支援工作負載移轉 (使用或搭配網路延伸模組)。  您仍可在 vSphere 環境中執行工作負載移轉，內部部署建立網路及將 VM 部署到這些網路。  如需詳細資訊，請參閱 VMware 技術文件中的 [VMware HCX 文件](https://docs.vmware.com/en/VMware-HCX/index.html)和[移轉虛擬機器與 VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)，以取得使用 HCX 的詳細資料。
