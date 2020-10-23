---
title: 如何建立和管理 DHCP
description: 本文說明如何在 Azure VMware 解決方案中管理 DHCP。
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: cb74ed4474cc14081e59142f2f60915fccd47559
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461051"
---
# <a name="how-to-create-and-manage-dhcp-in-azure-vmware-solution"></a>如何在 Azure VMware 解決方案中建立和管理 DHCP

NSX-T 提供為您的私人雲端設定 DHCP 的能力。 如果您使用 NSX-T 來裝載您的 DHCP 伺服器，請參閱 [建立 dhcp 伺服器](#create-dhcp-server)。 否則，如果您的網路中有協力廠商外部 DHCP 伺服器，請參閱 [建立 DHCP 轉送服務](#create-dhcp-relay-service)。

## <a name="create-dhcp-server"></a>建立 DHCP 伺服器

使用下列步驟來設定 NSX-T 上的 DHCP 伺服器。

1. 在 [NSX 管理員] 中，流覽至 [ **網路** ] 索引標籤並選取 [ **DHCP**]。 
1. 選取 [ **新增伺服器** ]，然後提供伺服器名稱和 IP 位址。 
1. 選取 [儲存]。

:::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="新增 DHCP 伺服器" border="true":::

### <a name="connect-dhcp-server-to-the-tier-1-gateway"></a>將 DHCP 伺服器連線至第1層閘道。

1. 從清單中選取 [ **第1層閘道**]、[閘道]，然後選取 [ **編輯**]。

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="新增 DHCP 伺服器" border="true":::

1. 選取 [ **無 IP 組態集** ] 以新增子網。

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="新增 DHCP 伺服器" border="true":::

1. 選取 [ **DHCP 本機伺服器** ] 作為 **類型**。 
1. 選取**Dhcp 伺服器**的 [**預設 dhcp** ]，然後選取 [**儲存**]。


1. 在 **第1層閘道** 視窗上，選取 [ **儲存**]。 
1. 選取 [ **關閉編輯** ] 以完成。

### <a name="add-a-network-segment"></a>新增網路區段

建立 DHCP 伺服器之後，您必須在其中新增網路區段。

1. 在 NSX-T 中選取 [**網路**功能] 索引標籤**Connectivity**，然後選取 [連線] 底下的**區段**。 
1. 選取 [ **新增區段** ]，並將區段和連接命名為第1層閘道。 
1. 選取 [ **設定子網** ] 以設定新的子網。 

   :::image type="content" source="./media/manage-dhcp/add-segment.png" alt-text="新增 DHCP 伺服器" border="true":::

1. 在 [ **設定子網** ] 視窗中，選取 [ **新增子網**]。 
1. 輸入閘道 IP 位址和 DHCP 範圍 **，然後選取**[**新增**然後套用]

1. 選取 [ **儲存** ]，以加入新的網路區段。

## <a name="create-dhcp-relay-service"></a>建立 DHCP 轉送服務

1. 選取 [ **網路** ] 索引標籤，然後在 [ **IP 管理**] 下選取 [ **DHCP**]。 
1. 選取 [ **新增伺服器**]。 
1. 針對 [ **伺服器類型** ] 選取 [DHCP 轉送]，然後輸入轉送伺服器的伺服器名稱和 IP 位址。 
1. 選取 [儲存]。

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="新增 DHCP 伺服器" border="true":::

1. 選取 [連線**能力**] 下**的第1層閘道**。 
1. 選取第1層閘道上的垂直省略號，然後選取 [ **編輯**]。

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="新增 DHCP 伺服器" border="true":::

1. 選取 [ **無 Ip 組態集** ] 來定義 ip 位址配置。

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="新增 DHCP 伺服器" border="true":::

1. 選取 [ **DHCP 轉送伺服器****類型**]。
1. 選取 dhcp 轉送伺服器以進行 **Dhcp 轉送**。 
1. 選取 [儲存]。


## <a name="specify-a-dhcp-range-ip-on-a-segment"></a>指定區段上的 DHCP 範圍 IP

> [!NOTE]
> 需要此設定，才能在 DHCP 用戶端區段上實現 DHCP 轉送功能。 

1. 在 [連線 **能力**] 底下，選取 [ **區段**]。 
1. 選取垂直省略號，然後選取 [ **編輯**]。 

   >[!TIP]
   >如果您想要加入新的區段，請選取 [ **新增區段**]。

1. 加入區段的詳細資料。 
1. 選取 [ **設定子網** ] 底下的值，以新增或修改子網。

   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="新增 DHCP 伺服器" border="true":::

1. 選取垂直省略號，然後選擇 [ **編輯**]。 如果您需要建立新的子網，請選取 [ **新增子網** ] 以建立閘道並設定 DHCP 範圍。 
1. 提供 IP 集區的範圍，然後選取 [套用 **]，然後**選取 [**儲存**]。

   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="新增 DHCP 伺服器" border="true":::

   DHCP 伺服器集區會指派給該區段。

   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="新增 DHCP 伺服器" border="true":::
