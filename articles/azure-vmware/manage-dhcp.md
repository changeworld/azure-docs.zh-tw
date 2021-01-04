---
title: 管理 Azure VMware 解決方案的 DHCP
description: 瞭解如何建立和管理 Azure VMware 解決方案私人雲端的 DHCP。
ms.topic: how-to
ms.custom: contperf-fy21q2
ms.date: 11/09/2020
ms.openlocfilehash: bcaba4274b0e6b423e9fa490c80fc57204d4e153
ms.sourcegitcommit: d488a97dc11038d9cef77a0235d034677212c8b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2020
ms.locfileid: "97708546"
---
# <a name="manage-dhcp-for-azure-vmware-solution"></a>管理 Azure VMware 解決方案的 DHCP

在私用雲端環境中執行的應用程式和工作負載需要 DHCP 服務來進行 IP 位址指派。  本文說明如何以兩種方式在 Azure VMware 解決方案中建立和管理 DHCP：

- 如果您是使用 NSX-T 來裝載 DHCP 伺服器，您必須[建立 DHCP 伺服器](#create-a-dhcp-server)並[轉送到該伺服器](#create-dhcp-relay-service)。 當您建立 DHCP 伺服器時，也會新增網路區段，並指定 DHCP IP 位址範圍。   

- 如果您在網路中使用第三方外部 DHCP 伺服器，您必須[建立 DHCP 轉送服務](#create-dhcp-relay-service)。 當您在 DHCP 伺服器上建立轉送時，無論是使用 NSX-T 或協力廠商來裝載您的 DHCP 伺服器，您都必須指定 DHCP IP 位址範圍。

>[!IMPORTANT]
>當 DHCP 伺服器位於內部部署資料中心時，DHCP 不適用於 VMware HCX L2 stretch network 上的虛擬機器 (Vm) 。  根據預設，NSX 會封鎖所有 DHCP 要求，使其無法通過 L2 延展。 如需解決方案，請參閱將 [DHCP 要求傳送至內部部署 dhcp 伺服器](#send-dhcp-requests-to-the-on-premises-dhcp-server) 程式。


## <a name="create-a-dhcp-server"></a>建立 DHCP 伺服器

如果您想要使用 NSX-T 來裝載您的 DHCP 伺服器，您將建立 DHCP 伺服器。 然後，您將新增網路區段，並指定 DHCP IP 位址範圍。

1. 在 [NSX-T 管理員] 中，選取 [**網路**  >  **DHCP**]，然後選取 [**新增伺服器**]。

1. 針對 [**伺服器類型**] 選取 [ **DHCP** ]，提供伺服器名稱和 IP 位址，然後選取 [**儲存**]。

   :::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="新增 DHCP 伺服器" border="true":::

1. 選取 [ **第1層閘道**]，選取第1層閘道上的垂直省略號，然後選取 [ **編輯**]。

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="選取要使用的閘道" border="true":::

1. 選取 [ **無 IP 組態集** ] 以新增子網。

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="新增子網" border="true":::

1. 在 [ **類型**] 中，選取 [ **DHCP 本機伺服器**]。 
   
1. 若為 **Dhcp 伺服器**，請選取 [ **預設 DHCP**]，然後選取 [ **儲存**]。

1. 再次選取 [ **儲存** ]，然後選取 [ **關閉編輯**]。

### <a name="add-a-network-segment"></a>新增網路區段

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]


## <a name="create-dhcp-relay-service"></a>建立 DHCP 轉送服務

如果您想要使用協力廠商外部 DHCP 伺服器，則必須建立 DHCP 轉送服務。 您也會在 [NSX-T 管理員] 中指定 DHCP IP 位址範圍。 

1. 在 [NSX-T 管理員] 中，選取 [**網路**  >  **DHCP**]，然後選取 [**新增伺服器**]。

1. 針對 [**伺服器類型**] 選取 [ **DHCP 轉送**]，提供伺服器名稱和 IP 位址，然後選取 [**儲存**]。

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="建立 dhcp 轉送服務" border="true":::

1. 選取 [ **第1層閘道**]，選取第1層閘道上的垂直省略號，然後選取 [ **編輯**]。

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="編輯第1層閘道" border="true":::

1. 選取 [ **無 Ip 組態集** ] 來定義 ip 位址配置。

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="編輯 ip 位址配置" border="true":::

1. 在 [ **類型**] 中，選取 [ **DHCP 伺服器**]。 
   
1. 若為 **Dhcp 伺服器**，請選取 [ **dhcp 轉送**]，然後選取 [ **儲存**]。

1. 再次選取 [ **儲存** ]，然後選取 [ **關閉編輯**]。


## <a name="specify-the-dhcp-ip-address-range"></a>指定 DHCP IP 位址範圍

1. 在 [NSX-T Manager] 中，選取 [網路] > [區段]。 
   
1. 選取區段名稱上的垂直省略號，然後選取 [ **編輯**]。
   
1. 選取 [ **設定子網** ] 以指定子網的 DHCP IP 位址。 
   
   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="網路區段" border="true":::
      
1. 視需要修改閘道 IP 位址，然後輸入 DHCP 範圍 IP。 
      
   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="編輯子網" border="true":::
      
1. 選取 **[** 套用]，然後 **儲存**。 區段會被指派 DHCP 伺服器集區。
      
   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="指派給區段的 DHCP 伺服器集區" border="true":::


## <a name="send-dhcp-requests-to-the-on-premises-dhcp-server"></a>將 DHCP 要求傳送至內部部署 DHCP 伺服器

如果您想要從 L2 擴充區段上的 Azure VMware 解決方案 Vm 將 DHCP 要求傳送至內部部署 DHCP 伺服器，則會建立安全區段設定檔。 

1. 登入您的內部部署 vCenter，然後在 [主資料夾] 底下，選取 [HCX]。

1. 選取 [**服務**] 底下的 [**網路擴充** 功能]。

1. 選取您要支援從 Azure VMware 解決方案到內部部署的 DHCP 要求的網路擴充功能。 

1. 記下目的地網路名稱。  

   :::image type="content" source="media/manage-dhcp/hcx-find-destination-network.png" alt-text="VMware vSphere 用戶端中網路擴充功能的螢幕擷取畫面" lightbox="media/manage-dhcp/hcx-find-destination-network.png":::

1. 在 Azure VMware 解決方案的 [NSX-T 管理員] 中，選取 [**網路**  >  **區段**  >  **區段設定檔**]。 

1. 選取 [ **新增區段設定檔** ]，然後選取 [ **安全性] 區段**。

   :::image type="content" source="media/manage-dhcp/add-segment-profile.png" alt-text="如何在 NSX 中新增區段設定檔的螢幕擷取畫面-T" lightbox="media/manage-dhcp/add-segment-profile.png":::

1. 提供名稱和標記，然後將 [ **BPDU 篩選** ] 切換為 [開啟]，並將所有 DHCP 切換為 [關閉]。

   :::image type="content" source="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png" alt-text="螢幕擷取畫面，顯示已切換開啟 BPDU 篩選器，且 DHCP 切換關閉" lightbox="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png":::

1. 移除 **BPDU 濾波器允許清單** 下的所有 MAC 位址（如果有的話）。  接著，選取 [儲存]。

   :::image type="content" source="media/manage-dhcp/add-segment-profile-bpdu-filter-allow-list.png" alt-text="顯示 BPDU 篩選允許清單中 MAC 位址的螢幕擷取畫面":::

1. 在 [**網路**  >  **區段**]  >  **區段** 的 [搜尋] 區域中，輸入定義網路名稱。

   :::image type="content" source="media/manage-dhcp/networking-segments-search.png" alt-text="網路 > 區段篩選欄位的螢幕擷取畫面":::

1. 選取區段名稱上的垂直省略號，然後選取 [ **編輯**]。

   :::image type="content" source="media/manage-dhcp/edit-network-segment.png" alt-text="區段 [編輯] 按鈕的螢幕擷取畫面" lightbox="media/manage-dhcp/edit-network-segment.png":::

1. 將 **區段安全性** 變更為您稍早建立的區段設定檔。

   :::image type="content" source="media/manage-dhcp/edit-segment-security.png" alt-text="[區段安全性] 欄位的螢幕擷取畫面" lightbox="media/manage-dhcp/edit-segment-security.png":::

## <a name="next-steps"></a>後續步驟

深入瞭解 [主機維護和生命週期管理](concepts-private-clouds-clusters.md#host-maintenance-and-lifecycle-management)。