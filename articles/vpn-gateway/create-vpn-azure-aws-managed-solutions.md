---
title: 使用受控解決方案在 Azure 與 AWS 之間建立 VPN
description: 如何使用受控解決方案（而不是 Vm 或設備）建立 Azure 與 AWS 之間的 VPN 連線。
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: ricmmartins
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 01/22/2021
ms.author: ricmart
ms.openlocfilehash: a0655ce1d2e9939981bb4fd3280af80e359ea1e1
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737739"
---
# <a name="create-a-vpn-connection-between-azure-and-aws-using-managed-solutions"></a>使用受控解決方案建立 Azure 與 AWS 之間的 VPN 連線

您可以使用受控解決方案來建立 Azure 與 AWS 之間的連線。 先前，您必須使用設備或作為回應者的 VM。 現在，您可以直接將 AWS 虛擬私人閘道連接到 Azure VPN 閘道，而不必擔心如何管理 IaaS 資源，例如虛擬機器。 本文可協助您使用受控解決方案，在 Azure 與 AWS 之間建立 VPN 連線。

:::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/diagram.png" alt-text="架構圖表":::

## <a name="configure-azure"></a>設定 Azure

### <a name="configure-a-virtual-network"></a>設定虛擬網路

設定虛擬網路。 如需相關指示，請參閱 [虛擬網路快速入門](../virtual-network/quick-create-portal.md)。

本文中使用下列範例值：

* **資源群組：** rg-azure-aws
* **區域：** 美國東部
* **虛擬網路名稱：** vnet-azure
* **IPv4 位址空間：** 172.10.0.0/16
* **子網名稱：** subnet-01
* **子網位址範圍：** 172.10.1.0/24

### <a name="create-a-vpn-gateway"></a>建立 VPN 閘道

為您的虛擬網路建立 VPN 閘道。 如需相關指示，請參閱 [教學課程：建立和管理 VPN 閘道](tutorial-create-gateway-portal.md)。

本文會使用下列範例值和設定：

* **閘道名稱：** vpn-azure-aws
* **區域：** 美國東部
* **閘道類型：** VPN
* **VPN 類型：** 以路由為基礎
* **SKU：** VpnGw1
* **世代：** 第1代
* **虛擬網路：** 必須是您想要為其建立閘道的 VNet。
* **閘道子網位址範圍：** 172.10.0.0/27
* **公用 IP 位址：** 新建
* **公用 IP 位址名稱：** pip-vpn-azure-aws
* **啟用主動-主動模式：** 禁用
* **設定 BGP：** 禁用

範例：

:::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/summary.png" alt-text="虛擬網路閘道摘要":::

## <a name="configure-aws"></a>設定 AWS

1. 建立虛擬私用雲端 (VPC) 。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpc.png" alt-text="建立 VPC 資訊":::

1. 在 VPC (虛擬網路) 內建立子網。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-subnet-vpc.png" alt-text="建立子網":::

1. 建立客戶閘道以指向 Azure VPN 閘道的公用 IP 位址。 **客戶閘道** 是 AWS 資源，其中包含有關客戶閘道裝置的 AWS 資訊，在此案例中是 Azure VPN 閘道。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-customer-gw.png" alt-text="建立客戶閘道":::

1. 建立虛擬私人閘道。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpg.png" alt-text="建立虛擬私人網路關":::

1. 將虛擬私人閘道附加至 VPC。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-vpg.png" alt-text="將 VPG 附加至 VPC":::

1. 選取 VPC。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attaching-vpg.png" alt-text="附加":::

1. 建立站對站 VPN 連線。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpn-connection.png" alt-text="建立 VPN 連接":::

1. 將路由選項設定為 **靜態** ，並指向 Azure 子網-01 首碼 **(172.10.1.0/24) 。**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/set-static-route.png" alt-text="設定靜態路由":::

1. 填妥選項之後，請 **建立** 連接。

1. 下載設定檔。 若要下載正確的設定，請將廠商、平臺和軟體變更為「 **一般**」，因為 Azure 並不是有效的選項。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/download-config.png" alt-text="下載設定":::

1. 此設定檔包含 AWS 所建立的兩個 IPsec 通道中的預先共用金鑰和公用 IP 位址。

   **通道1**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-1.png" alt-text="通道1":::

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-1-config.png" alt-text="通道1設定":::

   **通道2**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-2.png" alt-text="通道2":::

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-2-config.png" alt-text="通道2設定":::

1. 建立通道之後，您會看到類似此範例的內容。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-connection-details.png" alt-text="AWS VPN 連線詳細資料":::

## <a name="create-local-network-gateway"></a>建立局域網路閘道

在 Azure 中，局域網路閘道是通常代表內部部署位置的 Azure 資源。 它會填入用來連接到內部部署 VPN 裝置的資訊。 不過，在此設定中，會建立局域網路閘道，並填入 AWS 虛擬私人閘道連線資訊。 如需 Azure 局域網路閘道的詳細資訊，請參閱 [AZURE VPN 閘道設定](vpn-gateway-about-vpn-gateway-settings.md#lng)。

在 Azure 中建立局域網路閘道。 如需相關步驟，請參閱 [建立局域網路閘道](tutorial-site-to-site-portal.md#LocalNetworkGateway)。

指定下列值：

* **名稱：** 在此範例中，我們會使用 lng-azure-aws。
* **端點：** IP 位址
* **IP 位址：** AWS 虛擬私人閘道的公用 IP 位址，以及 VPC CIDR 前置詞。 您可以在先前下載的設定檔中找到公用 IP 位址。

AWS 會建立兩個 IPsec 通道以供高可用性之用。 下列範例會顯示 IPsec 通道 #1 的公用 IP 位址。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/local-network-gateway.png" alt-text="局域網路閘道":::

## <a name="create-vpn-connection"></a>建立 VPN 連接

在本節中，您會在 Azure 虛擬網路閘道和 AWS 閘道之間建立 VPN 連線。

1. 建立 Azure 連接。 如需建立連線的步驟，請參閱 [建立 VPN 連接](tutorial-site-to-site-portal.md#CreateConnection)。

   在下列範例中，是從您稍早下載的設定檔取得共用金鑰。 在此範例中，我們會使用 AWS 所建立的 IPsec 通道 #1 值，並在設定檔中說明。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-connection.png" alt-text="Azure connection 物件":::

1. 查看連接。 幾分鐘之後，就會建立連接。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/connection-established.png" alt-text="工作連接":::

1. 確認 AWS IPsec 通道 #1 已 **啟動**。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-connection-established.png" alt-text="確認 AWS 通道已啟動":::

1. 編輯與 VPC 相關聯的路由表。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/edit-aws-route.png" alt-text="編輯路由":::

1. 將路由新增至 Azure 子網。 此路由會流經 VPC 閘道。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/save-aws-route.png" alt-text="儲存路由設定":::

## <a name="configure-second-connection"></a>設定第二個連接

在本節中，您會建立第二個連接，以確保高可用性。

1. 建立另一個局域網路閘道，指向 AWS 上 IPsec 通道 #2 的公用 IP 位址。 這是待命閘道。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-lng-standby.png" alt-text="建立區域網路閘道":::

1. 建立從 Azure 到 AWS 的第二個 VPN 連線。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-connection-standby.png" alt-text="建立待命局域網路閘道連線":::

1. 查看 Azure VPN 閘道連線。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-tunnels.png" alt-text="Azure 連接狀態":::

1. 查看 AWS 連接。 在此範例中，您可以看到現在已建立連接。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-tunnels.png" alt-text="AWS 連接狀態":::

## <a name="to-test-connections"></a>測試連接

1. 將 **網際網路閘道** 新增至 AWS 上的 VPC。 網際網路閘道是 Amazon VPN 與網際網路之間的邏輯連線。 此資源可讓您透過網際網路從 AWS 公用 IP 透過測試 VM 連接。 VPN 連接不需要此資源。 我們只會使用它來進行測試。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-igw.png" alt-text="建立網際網路閘道":::

1. 選取 [ **附加至 VPC**]。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-igw.png" alt-text="將網際網路閘道附加至 VPC":::

1. 選取 VPC 並 **連接網際網路閘道**。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-igw-2.png" alt-text="附加閘道":::

1. 建立路由，以允許透過網際網路閘道連接至 **0.0.0.0/0** (網際網路) 。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/allow-internet-igw.png" alt-text="設定透過閘道的路由":::

1. 在 Azure 中，會自動建立路由。 您可以選取 **VM > 網路 > 網路介面 > 有效路由**，來檢查 Azure vm 的路由。 您會看到2個路由，每個連接1個路由。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-effective-routes.png" alt-text="檢查有效路由":::

1. 您可以從 Azure 上的 Linux VM 進行測試。 結果看起來會與下列範例類似。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-overview.png" alt-text="Linux VM 的 Azure 總覽":::

1. 您也可以從 AWS 上的 Linux VM 進行測試。 結果看起來會與下列範例類似。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-overview.png" alt-text="Linux VM 的 AWS 總覽":::

1. 測試 Azure VM 的連線能力。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-ping.png" alt-text="從 Azure 進行 Ping 測試":::

1. 測試 AWS VM 的連線能力。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-ping.png" alt-text="從 AWS 進行 Ping 測試":::

## <a name="next-steps"></a>後續步驟

如需有關 AWS 支援 IKEv2 的詳細資訊，請參閱 [AWS 文章](https://aws.amazon.com/about-aws/whats-new/2019/02/aws-site-to-site-vpn-now-supports-ikev2/)。
