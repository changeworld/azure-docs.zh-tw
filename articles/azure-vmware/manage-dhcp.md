---
title: 如何管理 DHCP
description: 本文說明如何管理 Azure VMware 解決方案（AVS）中的 DHCP
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 80791dd2041fb9d6fbc7c67f2d7d7b2d0b6c977e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84148356"
---
# <a name="how-to-manage-dhcp-in-azure-vmware-solution-avs-preview"></a>如何管理 Azure VMWare 解決方案（AVS） Preview 中的 DHCP

NSX-T 提供為您的私人雲端設定 DHCP 的功能。 如果您打算使用 NSX-T 來裝載 DHCP 伺服器，請參閱[建立 dhcp 伺服器](#create-dhcp-server)。 否則，如果您的網路中有協力廠商外部 DHCP 伺服器，而且您想要將要求轉送到該 DHCP 伺服器，請參閱[建立 dhcp 轉送服務](#create-dhcp-relay-service)。

## <a name="create-dhcp-server"></a>建立 DHCP 伺服器

使用下列步驟，在 NSX-T 上設定 DHCP 伺服器。

從 [NSX manager] 流覽至 [**網路**] 索引標籤，然後選取 [ **IP 管理**] 底下的 [ **DHCP** ] 選取 [**新增伺服器**] 按鈕。 然後提供伺服器名稱和伺服器 IP 位址。 完成後，選取 [**儲存**]。

:::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="新增 DHCP 伺服器" border="true":::

### <a name="connect-dhcp-server-to-the-tier-1-gateway"></a>將 DHCP 伺服器連線到第1層閘道。

選取 [**第1層閘道**]，選取閘道，然後選取 [**編輯**]

:::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="選取要使用的閘道" border="true":::

選取 [**沒有 IP 組態集**] 來新增子網

:::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="新增子網" border="true":::

在下一個畫面上，從 [**類型**] 下拉式清單中選取 [ **DHCP 本機伺服器**]。 若為**Dhcp 伺服器**，請選取 [**預設 DHCP** ]，然後選取 [**儲存**]。

:::image type="content" source="./media/manage-dhcp/set-ip-address-management.png" alt-text="選取 dhcp 伺服器的選項" border="true":::

在 [**第1層閘道**] 視窗中，選取 [**儲存**]。 在下一個畫面上，您會看到 [**已儲存變更**]，請選取 [**關閉編輯**] 完成。

### <a name="add-a-network-segment"></a>新增網路區段

建立 DHCP 伺服器之後，您必須在其中新增網路區段。

在 [NSX-T] 中 **，選取 [** **網路**] 索引標籤，然後選取 [連線**能力**] 選取 [**新增區段**]。 將區段命名為第1層閘道並聯機。 接下來，選取 [**設定子網**] 來設定新的子網。 

:::image type="content" source="./media/manage-dhcp/add-segment.png" alt-text="新增網路區段" border="true":::

在 [**設定子網**] 視窗中，選取 [**新增子網**]。 輸入閘道 IP 位址和 DHCP 範圍，然後選取 [**新增**] **，然後套用**

:::image type="content" source="./media/manage-dhcp/add-subnet-segment.png" alt-text="新增網路區段" border="true":::

完成時，選取 [**儲存**] 以完成新增網路區段。

:::image type="content" source="./media/manage-dhcp/segments-complete.png" alt-text="區段完成" border="true":::

## <a name="create-dhcp-relay-service"></a>建立 DHCP 轉送服務

在 [NXT-T] 視窗中，選取 [**網路**] 索引標籤，然後在 [ **IP 管理**] 下選取 [ **DHCP**]。 選取 [**新增伺服器**]。 針對 [**伺服器類型**] 選擇 [DHCP 轉送]，然後輸入轉送伺服器的伺服器名稱和 IP 位址。 選取 [儲存] 來儲存變更。

:::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="建立 dhcp 轉送伺服器" border="true":::

選取 [連線**能力**] 下**的 [第1層閘道**]。 選取第1層閘道上的垂直省略號，然後選擇 [**編輯**]。

:::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="編輯第1層閘道" border="true":::

選取 [**無 Ip 組態集**] 來定義 ip 位址配置。

:::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="編輯 ip 位址配置" border="true":::

在對話方塊中，針對 [**類型**] 選取 [ **DHCP 轉送伺服器**]。 在 [ **Dhcp 轉送**] 下拉式清單中，選取您的 DHCP 轉送伺服器。 完成後，選取 [**儲存**]

:::image type="content" source="./media/manage-dhcp/set-ip-address-management-relay.png" alt-text="設定 ip 位址管理" border="true":::

指定區段上的 DHCP 範圍 IP：

> [!NOTE]
> 需要進行此設定，才能在 DHCP 用戶端區段上實現 DHCP 轉送功能。 

在 **[** 連線] 底下，選取 [**區段**]。 選取垂直省略號，然後選取 [**編輯**]。 相反地，如果您想要新增區段，則可以選取 [新增**區段**] 來建立新的區段。

:::image type="content" source="./media/manage-dhcp/edit-segments.png" alt-text="編輯網路子網" border="true":::

新增有關區段的詳細資料。 選取 [ **子**網] 或 [**設定子**網] 底下的值，以新增或修改子網。

:::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="網路區段" border="true":::

選取垂直省略號，然後選擇 [**編輯**]。 如果您需要建立新的子網，請選取 [**新增子網**] 來建立閘道並設定 DHCP 範圍。 提供 IP 集區的範圍並選取 [套用]，**然後選取 [****儲存**]

:::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="編輯子網" border="true":::

現在會將 DHCP 伺服器集區指派給該區段。

:::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="指派給區段的 DHCP 伺服器集區" border="true":::
