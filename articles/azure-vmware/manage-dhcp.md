---
title: 如何建立和管理 DHCP
description: 本文說明如何在 Azure VMware 解決方案中管理 DHCP。
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 3fc3de228179925afdf1b7c1015c577fd9c4c924
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752168"
---
# <a name="how-to-create-and-manage-dhcp-in-azure-vmware-solution"></a>如何在 Azure VMWare 解決方案中建立和管理 DHCP

NSX-T 提供為您的私人雲端設定 DHCP 的能力。 如果您打算使用 NSX-T 來裝載您的 DHCP 伺服器，請參閱 [建立 dhcp 伺服器](#create-dhcp-server)。 否則，如果您的網路中有協力廠商外部 DHCP 伺服器，而且您想要將要求轉送到該 DHCP 伺服器，請參閱 [建立 dhcp 轉送服務](#create-dhcp-relay-service)。

## <a name="create-dhcp-server"></a>建立 DHCP 伺服器

使用下列步驟來設定 NSX-T 上的 DHCP 伺服器。

從 NSX manager 流覽至 [**網路**] 索引標籤，然後選取 [ **IP 管理**] 下的 [ **DHCP** ]。 選取 [ **新增伺服器** ] 按鈕。 然後提供伺服器名稱和伺服器 IP 位址。 完成之後，請選取 [ **儲存**]。

:::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="新增 DHCP 伺服器" border="true":::

### <a name="connect-dhcp-server-to-the-tier-1-gateway"></a>將 DHCP 伺服器連線至第1層閘道。

1. 選取**第1層閘道**，選取閘道，然後選取 [**編輯**]

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="選取要使用的閘道" border="true":::

1. 選取 [**無 IP 組態集**] 以新增子網

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="新增子網" border="true":::

1. 在下一個畫面中，從 [**類型**] 下拉式清單中選取 [ **DHCP 本機伺服器**]。 若為 **Dhcp 伺服器**，請選取 [ **預設 DHCP** ]，然後選取 [ **儲存**]。

   :::image type="content" source="./media/manage-dhcp/set-ip-address-management.png" alt-text="選取 dhcp 伺服器的選項" border="true":::

1. 在 **第1層閘道** 視窗上，選取 [ **儲存**]。 在下一個畫面中，您會看到 **已儲存的變更**，請選取 [ **關閉編輯** ] 以完成。

### <a name="add-a-network-segment"></a>新增網路區段

建立 DHCP 伺服器之後，您必須在其中新增網路區段。

1. 在 NSX-T 中選取 [**網路**功能] 索引標籤**Connectivity**，然後選取 [連線] 底下的**區段**。 選取 [ **新增區段**]。 將區段命名為第1層閘道和連線。 接下來，選取 [ **設定子網** ] 以設定新的子網。 

   :::image type="content" source="./media/manage-dhcp/add-segment.png" alt-text="新增網路區段" border="true":::

1. 在 [ **設定子網** ] 視窗中，選取 [ **新增子網**]。 輸入閘道 IP 位址和 DHCP 範圍 **，然後選取**[**新增**然後套用]

   :::image type="content" source="./media/manage-dhcp/add-subnet-segment.png" alt-text="新增網路區段" border="true":::

1. 完成時，請選取 [ **儲存** ] 以完成新增網路區段。

   :::image type="content" source="./media/manage-dhcp/segments-complete.png" alt-text="區段完成" border="true":::

## <a name="create-dhcp-relay-service"></a>建立 DHCP 轉送服務

1. 在 NXT-T 視窗中，選取 [ **網路** ] 索引標籤，然後在 [ **IP 管理**] 下選取 [ **DHCP**]。 選取 [ **新增伺服器**]。 選擇 [DHCP 轉送] 作為 [ **伺服器類型** ]，並輸入轉送伺服器的伺服器名稱和 IP 位址。 選取 [儲存] 來儲存變更。

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="建立 dhcp 轉送伺服器" border="true":::

1. 選取 [連線**能力**] 下**的第1層閘道**。 選取第1層閘道上的垂直省略號，然後選擇 [ **編輯**]。

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="編輯第1層閘道" border="true":::

1. 選取 [ **無 Ip 組態集** ] 來定義 ip 位址配置。

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="編輯 ip 位址配置" border="true":::

1. 在對話方塊中，針對 [ **類型**] 選取 [ **DHCP 轉送伺服器**]。 在 [ **Dhcp 轉送** ] 下拉式清單中，選取您的 DHCP 轉送伺服器。 完成時，請選取 [**儲存**]

   :::image type="content" source="./media/manage-dhcp/set-ip-address-management-relay.png" alt-text="設定 ip 位址管理" border="true":::

## <a name="specify-a-dhcp-range-ip-on-segment"></a>在區段上指定 DHCP 範圍 IP

> [!NOTE]
> 需要此設定，才能在 DHCP 用戶端區段上實現 DHCP 轉送功能。 

1. 在 [連線 **能力**] 底下，選取 [ **區段**]。 選取垂直省略號，然後選取 [ **編輯**]。 相反地，如果您想要新增區段，您可以選取 [ **新增區段** ] 來建立新的區段。

   :::image type="content" source="./media/manage-dhcp/edit-segments.png" alt-text="編輯網路子網" border="true":::

1. 加入區段的詳細資料。 選取 [ **子網** ] 下的值，或 **設定** 子網以新增或修改子網。

   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="網路區段" border="true":::

1. 選取垂直省略號，然後選擇 [ **編輯**]。 如果您需要建立新的子網，請選取 [ **新增子網** ] 以建立閘道並設定 DHCP 範圍。 提供 IP 集區的範圍，然後選取 [套用 **]，然後**選取 [**儲存**]。

   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="編輯子網" border="true":::

1. 現在會將 DHCP 伺服器集區指派給該區段。

   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="指派給區段的 DHCP 伺服器集區" border="true":::
