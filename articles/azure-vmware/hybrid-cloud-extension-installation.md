---
title: 安裝 VMware HCX
description: 為您的 Azure VMware 解決方案私人雲端設定 VMware HCX 解決方案
ms.topic: how-to
ms.date: 09/24/2020
ms.openlocfilehash: cdeffa41db5aac597d8dfcf3a735cbeb7f0d8a8e
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91370839"
---
# <a name="install-hcx-for-azure-vmware-solution"></a>安裝適用於 Azure VMware 解決方案的 HCX

在本文中，我們將逐步解說為您的 Azure VMWare 解決方案私人雲端設定 VMWare HCX 解決方案的程式。 HCX 可讓您將 VMware 工作負載遷移至雲端，以及透過各種內建 HCX 支援的遷移類型來進行其他已連線的網站。

HCX Advanced （預設安裝）最多支援三個網站連線 (內部部署或雲端到雲端) 。 如果需要三個以上的網站連線或 HCX [企業功能](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) ，客戶可以選擇透過支援來啟用 HCX Enterprise 附加元件，此功能目前為預覽狀態。 HCX EE 可搭配 AVS 作為預覽功能/服務。 HCX EE for AVS 處於預覽狀態，它是免費的函式/服務，受限於預覽服務的條款及條件。 HCX EE 服務正式推出後，您將會收到30天的通知，指出計費將會切換。 您也可以選擇切換關閉/退出服務。


[在開始之前](#before-you-begin)，請先仔細檢閱[軟體版本需求](#software-version-requirements)和[先決條件](#prerequisites)。 

接著，我們將逐步解說所有必要的程式，如下所示：

> [!div class="checklist"]
> * 部署內部部署 HCX OVA (連接器) 
> * 啟用和設定 HCX
> * 設定網路上行連結和服務網格
> * 藉由檢查設備狀態來完成設定

完成設定之後，您可以遵循本文最後提供的建議後續步驟。  

## <a name="before-you-begin"></a>開始之前
    
* 請參閱基本的 Azure VMware 解決方案軟體定義資料中心 (SDDC) [教學課程系列](tutorial-network-checklist.md)。
* 複習和參考 [VMWARE HCX 檔](https://docs.vmware.com/en/VMware-HCX/index.html) ，包括 HCX 使用者指南。
* 參閱 [使用 VMWARE HCX 遷移虛擬機器的](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)vmware 檔。
* （選擇性）查看 [VMWARE HCX 部署考慮](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)。
* 可選檢閱 HCX 上的相關 VMware 資料，例如 HCX 上 的 VMware vSphere [部落格系列](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html)。 
* 透過 Azure VMware 解決方案支援通道，要求 Azure VMware 解決方案 HCX Enterprise 啟用。

當您準備使用 Azure VMware 解決方案私用雲端 HCX 解決方案時，調整工作負載與計算和儲存體資源的大小是個基本的規劃步驟。 在初始私用雲端環境規劃中解決調整大小步驟。 

您也可以在 Azure Migrate 入口網站中完成 [Azure VMware 解決方案評估](../migrate/how-to-create-azure-vmware-solution-assessment.md) ，以調整工作負載的大小。

## <a name="software-version-requirements"></a>軟體版本需求

基礎結構元件必須執行所需的最低版本。 
                                                         
| 元件類型    | 來源環境需求    | 目的地環境需求   |
| --- | --- | --- |
| vCenter Server   | 5.1<br/><br/>如果使用 5.5 U1 或更早版本，請使用獨立 HCX 使用者介面進行 HCX 作業。  | 6.0 U2 及更高版本   |
| ESXi   | 5.0    | ESXi 6.0 及更高版本   |
| NSX    | 適用于來源的邏輯交換器 HCX 網路延伸模組： NSXv 6.2 + 或 NSX-T 2.4 +   | NSXv 6.2+ 或 NSX-T 2.4+<br/><br/>針對 HCX 近接路由： NSXv 6.4 + (不支援使用的相近路由-T)  |
| vCloud Director   | 非必要 - 來源網站沒有與 vCloud Director 的互通性 | 將目的地環境與 Vcloud 進行整合 Director 整合時，最小值為9.1.0.2。  |

## <a name="prerequisites"></a>Prerequisites

* 您應在內部部署與 Azure VMware Solution SDDC ExpressRoute 線路之間設定 ExpressRoute Global 觸及範圍。

* 所有必要的埠都應該在內部部署和 Azure VMware Solution SDDC 之間開啟 (請參閱 [VMWARE HCX 檔](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)) 。

* HCX Manager 在內部部署的一個 IP 位址，至少有兩個 IP 位址可供互連 (IX) 和網路延伸 (NE) 設備。

* 內部部署 HCX IX 和 NE 設備應能觸及 vCenter 和 ESXi 基礎結構。

* 若要部署 WAN 互連設備，已針對私人雲端建立的客戶提供了 \ 22 的特定 CIDR 範圍。

## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>部署 VMware HCX OVA 內部部署

1. `https://x.x.x.9`使用**cloudadmin**的使用者認證，在埠443上登入 AZURE VMware Solution HCX Manager，然後移至 [**支援**]。

1. 選取要部署到 vCenter 的 VMware HCX OVA 檔案的下載連結。

1. 移至內部部署 vCenter，然後選取新下載的 OVF 範本以部署至您的內部部署 vCenter。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-template.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::

1. 選取名稱和位置，然後選取需要部署 HCX 的資源/叢集。 然後，檢閱詳細資料和所需的資源。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-template.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::

1. 檢閱授權條款，如果您同意，請選取所需的儲存體和網路。 然後選取 [下一步]。

1. 在 [自訂範本] 中，輸入全部所需的資訊。 

   :::image type="content" source="media/hybrid-cloud-extension-installation/customize-template.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::

1. 選取 [下一步]，並驗證設定，然後選取 [完成] 以部署 HCX OVA。

## <a name="activate-hcx"></a>啟動 HCX

安裝後，執行下列步驟。

1. 登入內部部署 HCX 管理員 `https://HCXManagerIP:9443` ，並使用系統 **管理員** 使用者名稱認證登入。 

   > [!IMPORTANT]
   > 請務必包含 `9443` HCX MANAGER IP 位址的埠號碼。

1. 在 [授權] 中，輸入您的 **HCX 進階金鑰**。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-key.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::
    
    > [!NOTE]
    > HCX 管理員必須有開放的網際網路存取或已設定 Proxy。

1. 在 **vcenter**中，如有需要，請編輯 vcenter 資訊。

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-vcenter.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::

1. 在 [資料中心位置] 中，如有需要，請編輯資料中心位置。

   :::image type="content" source="media/hybrid-cloud-extension-installation/system-location.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::

## <a name="configure-hcx"></a>設定 HCX 

1. 登入內部部署 vCenter，然後選取 [ **首頁**] 下的 [ **HCX**]。

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-vcenter.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::

1. 在 [**基礎結構**] 之下，選取 [**網站配對**] 以  >  **新增網站配對**。

   :::image type="content" source="media/hybrid-cloud-extension-installation/site-pairing.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::

1. 輸入遠端 HCX URL 或 IP 位址、Azure VMware Solution cloudadmin 使用者名稱和密碼，然後選取 **[連線]**。

   系統會顯示連線的網站。

   :::image type="content" source="media/hybrid-cloud-extension-installation/site-connection.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::

1. 在 [**基礎結構**] 之下，選取 [**互連**  >  **多網站服務網格**  >  **網路設定檔**  >  **建立網路設定檔**]。

   :::image type="content" source="media/hybrid-cloud-extension-installation/create-network-profile.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::

1. 針對新的網路設定檔，輸入 HCX IX 和 NE IP 位址範圍 (IX 和 NE 設備) 至少需要兩個 IP 位址。

   :::image type="content" source="media/hybrid-cloud-extension-installation/enter-address-ranges.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::
  
   > [!NOTE]
   > 網路延伸設備 (HCX-NE) 與分散式虛擬交換器 (DVS) 有一對一的關係。  

1. 此時，請選取 [計算設定檔] > [建立計算設定檔]。

1. 輸入計算設定檔名稱，然後選取 [繼續]。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/create-compute-profile.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::

1. 選取要啟用的服務，例如 [遷移]、[網路擴充] 或 [嚴重損壞修復]，然後選取 [ **繼續**]。

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-services.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::

1. 在 [選取服務資源] 中，選取所選 HCX 服務所應該啟用的一個或多個服務資源。 選取 [繼續]。

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-service-resources.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::
  
   > [!NOTE]
   > 選取特定叢集，其中已使用 HCX 鎖定來源 VM 進行移轉。

1. 選取 [資料存放區]，然後選取 [繼續]。 
      
   選取每個用於部署 HCX 互連設備的計算和儲存體資源。 選取多個資源時，HCX 會使用第一個選取的資源，直到其容量用盡為止。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/deployment-resources.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::

1. 選取在 [網路設定檔] 中建立的管理網路設定檔，然後選取 [繼續]。  
      
   選取可觸達 vCenter 和 ESXi 主機管理介面的網路設定檔。 如果您尚未定義這類網路設定檔，您可以在這裡建立。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/management-network-profile.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::

1. 選取 [網路上行連結]，然後選取 [繼續]。
      
   選取一或多個網路設定檔，讓下列其中一項成立：  
   * 可以透過此網路觸達遠端網站上的互連設備  
   * 遠端設備可以透過此網路觸達本機互連設備。  
    
   如果您的點對點網路 (例如 Direct Connect) 不會跨多個網站共用，您可以略過此步驟，因為計算設定檔會與多個網站共用。 在這種情況下，您可以在建立互連服務網格期間覆寫和指定上行連結網路設定檔。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/uplink-network-profile.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::

1. 選取 [VMotion 網路設定檔]，然後選取 [繼續]。
      
   選取可用來觸達 ESXi 主機 VMotion 介面的網路設定檔。 如果您尚未定義這類網路設定檔，您可以在這裡建立。 如果您沒有 VMotion 網路，請選取 [管理網路設定檔]。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/vmotion-network-profile.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::

1. 從 [ **選取 vSphere 複寫網路設定檔**] 中，選取網路設定檔 vSphere 的複寫介面 ESXi 主機，然後選取 [ **繼續**]。
      
   在大多數情況下，此設定檔與管理網路設定檔相同。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/vsphere-replication-network-profile.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::

1. 從 [ **選取網路擴充功能的分散式交換器**] 中，選取您的網路上將整合並聯機之 vm 的網路 DVS。  選取 [繼續]。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/distributed-switches.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::

1. 請檢查連接規則，然後選取 [ **繼續**]。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/complete-compute-profile.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::

1.  選取 [完成] 以建立計算設定檔。

## <a name="configure-network-uplink"></a>設定網路上行連結

現在，在 Azure VMware Solution SDDC 中針對網路上行連結設定網路設定檔變更。

1. 登入 SDDC NSX-T 以建立新的邏輯交換器，或使用可用於內部部署與 Azure VMware Solution SDDC 之間的網路上行連結的現有邏輯交換器。

1. 在 Azure VMware Solution SDDC 中建立 HCX 上行連結的網路設定檔，可用於內部部署至 Azure VMware Solution SDDC 通訊。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/network-profile-uplink.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::

1. 輸入網路設定檔的名稱，並根據所需的 L2 網路延伸模組，輸入至少 4 至 5 個可用的 IP 位址。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-uplink-profile.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::

1. 選取 [ **建立** ] 以完成 Azure VMWARE Solution SDDC 設定

## <a name="configure-service-mesh"></a>設定服務網格

現在在內部部署與 Azure VMware Solution SDDC 之間設定服務網格。

1. 登入 Azure VMware Solution SDDC vCenter，然後選取 [ **HCX**]。

2. 在 [**基礎結構**] 之下，選取 [**互連**  >  **服務網格**  >  **建立服務網格**]，以設定在先前步驟中建立的網路和計算設定檔。    

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-service-mesh.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::

3. 選取 [配對的網站] 以啟用混合式功能，然後選取 [ **繼續**]。   

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-paired-sites.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::

4. 選取來源和遠端計算設定檔以啟用混合服務，然後選取 [ **繼續**]。
      
   這些選項會定義資源，讓 Vm 可以使用 HCX 服務。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/enable-hybridity.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::

5. 選取要啟用的服務，然後選取 [ **繼續**]。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-services.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::

6. 在 [進階設定 - 覆寫上行連結網路設定檔] 中選取 [繼續]。  
      
   上行連結網路設定檔是用來連線至網路，可透過此網路連線到遠端網站的互連設備。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/override-uplink-profiles.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::

7. 選取 **[設定網路延伸設備] Scale Out**。 

   :::image type="content" source="media/hybrid-cloud-extension-installation/network-extension-scale-out.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::

8. 輸入對應於 DVS 交換器計數的設備計數。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/appliance-scale.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::

9. 選取 [ **繼續** 跳過]。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/traffic-engineering.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::

10. 檢閱拓撲預覽，然後選取 [繼續]。 

11. 輸入此服務網格的使用者易記名稱，然後選取 **[完成]** 以完成。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/complete-service-mesh.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::

   如此便完成服務網格的部署和設定。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/deployed-service-mesh.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::

## <a name="check-appliance-status"></a>檢查設備狀態
若要檢查設備的狀態，請選取 [互連] > [設備]。 

:::image type="content" source="media/hybrid-cloud-extension-installation/appliance-status.png" alt-text="接下來，移至內部部署 vCenter，然後選取 OVF 範本以部署至您的內部部署 vCenter。":::

## <a name="next-steps"></a>後續步驟

當設備互連通道 **狀態** 為 [已 **啟動** ] 和 [綠色] 時，您就可以使用 HCX 來遷移和保護 Azure VMware 解決方案 vm。 請參閱 VMware 技術文件中的 [VMware HCX 文件](https://docs.vmware.com/en/VMware-HCX/index.html)和[移轉虛擬機器與 VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)。
