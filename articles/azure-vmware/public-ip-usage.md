---
title: 如何使用 Azure VMware 解決方案中的公用 IP 功能
description: 本文說明如何使用 Azure 虛擬 WAN 中的公用 IP 功能。
ms.topic: how-to
ms.date: 10/28/2020
ms.openlocfilehash: 63475b478a951632c068b168353acf2e0bb7061c
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94490384"
---
# <a name="how-to-use-the-public-ip-functionality-in-azure-vmware-solution"></a>如何使用 Azure VMware 解決方案中的公用 IP 功能

公用 IP 是 Azure VMware 解決方案連線能力中的一項新功能。 它會使資源（例如 web 伺服器、虛擬機器 (Vm) ，以及可透過公用網路存取的主機）。 

您可以透過兩種方式來啟用公用網際網路存取。 

- 應用程式可以在 HTTP/HTTPS 流量的應用程式閘道負載平衡器底下進行託管和發佈。
- 透過 Azure 虛擬 WAN 中的公用 IP 功能發佈。

作為 Azure VMware 解決方案私人雲端部署的一部分，在啟用公用 IP 功能時，會建立並啟用自動化的必要元件：

-  Virtual WAN

-  具有 ExpressRoute 連線能力的虛擬 WAN 中樞

-  具有公用 IP 的 Azure 防火牆服務

本文會詳細說明如何在虛擬 WAN 中使用公用 IP 功能。

## <a name="prerequisites"></a>先決條件

- Azure VMware 解決方案環境
- 在 Azure VMware 解決方案環境中執行的 web 伺服器。
- 虛擬 WAN 中樞部署的新非重迭 IP 範圍，通常是 `/24` 。

## <a name="reference-architecture"></a>參考架構

:::image type="content" source="media/public-ip-usage/public-ip-architecture-diagram.png" alt-text="公用 IP 架構圖表" border="false" lightbox="media/public-ip-usage/public-ip-architecture-diagram.png":::

架構圖顯示 Azure VMware 解決方案環境中裝載的客戶 web 伺服器，並使用 RFC1918 的私人 IP 位址進行設定。  此 web 服務可透過虛擬 WAN 公用 IP 功能提供給網際網路。  公用 IP 通常是在 Azure 防火牆中轉譯的目的地 NAT。 使用 DNAT 規則，防火牆原則會將公用 IP 位址要求轉譯為私人位址 (web 伺服器) 與埠。

使用者要求會叫用公用 IP 上的防火牆，然後再使用 Azure 防火牆中的 DNAT 規則，將其轉譯為私人 IP。 防火牆會檢查 NAT 資料表，如果要求符合某個專案，則會將流量轉送至 Azure VMware 解決方案環境中的已轉譯位址和埠。

Web 服務器會接收要求，並以要求的資訊或頁面回復至防火牆，然後防火牆會將資訊轉送至公用 IP 位址上的使用者。

## <a name="test-case"></a>測試案例
在此案例中，您必須將 IIS web 伺服器發佈到網際網路。 使用 Azure VMware 解決方案中的公用 IP 功能，在公用 IP 位址上發佈網站。  我們將在防火牆上設定 NAT 規則，並將 Azure VMware 解決方案資源 (具有公用 IP 之 web 伺服器) 的 Vm。

## <a name="deploy-virtual-wan"></a>部署虛擬 WAN

1. 登入 Azure 入口網站，然後搜尋並選取 [ **Azure VMware 解決方案** ]。

1. 選取 Azure VMware 解決方案私人雲端。

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-resource.png" alt-text="Azure VMware 解決方案私人雲端的螢幕擷取畫面。" border="true" lightbox="media/public-ip-usage/avs-private-cloud-resource.png":::

1. 在 [ **管理** ] 底下，選取 [連線 **能力** ]。

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-manage-menu.png" alt-text="連接區段的螢幕擷取畫面。" border="true" lightbox="media/public-ip-usage/avs-private-cloud-manage-menu.png":::

1. 選取 [ **公用 IP** ] 索引標籤，然後選取 [ **設定** ]。

   :::image type="content" source="media/public-ip-usage/connectivity-public-ip-tab.png" alt-text="顯示開始設定公用 IP 位置的螢幕擷取畫面" border="true" lightbox="media/public-ip-usage/connectivity-public-ip-tab.png":::

1. 接受預設值或加以變更，然後選取 [ **建立** ]。

   - 虛擬廣域網路資源群組

   - 虛擬廣域網路名稱

   - 虛擬中樞位址區塊 (使用新的非重迭 IP 範圍) 

   -  (1-100) 的公用 Ip 數目

需要大約一小時的時間才能完成所有元件的部署。 此部署只需要發生一次，即可支援此 Azure VMware 解決方案環境的所有未來公用 Ip。  

>[!TIP]
>您可以從 **通知** 區域監視狀態。 

## <a name="view-and-add-public-ip-addresses"></a>查看並新增公用 IP 位址

我們可以遵循下列步驟來檢查並新增更多公用 IP 位址。

1. 在 [Azure 入口網站中，搜尋並選取 [ **防火牆** ]。

1. 選取已部署的防火牆，然後選取 [ **造訪 Azure 防火牆管理員以設定及管理此防火牆** 。

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="顯示設定和管理防火牆選項的螢幕擷取畫面" border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. 選取 [ **安全虛擬中樞** ]，然後從清單中選取虛擬中樞。

   :::image type="content" source="media/public-ip-usage/select-virtual-hub.png" alt-text="防火牆管理員的螢幕擷取畫面" lightbox="media/public-ip-usage/select-virtual-hub.png":::

1. 在 [虛擬中樞] 頁面上，選取 [ **公用 ip** 設定]，並新增更多公用 ip 位址，然後選取 [ **新增** ]。 

   :::image type="content" source="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png" alt-text="如何在防火牆管理員中新增公用 IP 設定的螢幕擷取畫面" border="true" lightbox="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png":::

1. 提供所需的 Ip 數目，然後選取 [ **新增** ]。

   :::image type="content" source="media/public-ip-usage/add-number-of-ip-addresses-required.png" alt-text="新增指定數目之公用 IP 設定的螢幕擷取畫面" border="true":::


## <a name="create-firewall-policies"></a>建立防火牆原則

部署所有元件之後，您可以在新增的資源群組中看到它們。 下一步是新增防火牆原則。

1. 在 [Azure 入口網站中，搜尋並選取 [ **防火牆** ]。

1. 選取已部署的防火牆，然後選取 [ **造訪 Azure 防火牆管理員以設定及管理此防火牆** 。

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="顯示設定和管理防火牆選項的螢幕擷取畫面" border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. 選取 [ **Azure 防火牆** 原則]，然後選取 [ **建立 azure 防火牆原則** ]。

   :::image type="content" source="media/public-ip-usage/create-firewall-policy.png" alt-text="如何在防火牆管理員中建立防火牆原則的螢幕擷取畫面" border="true" lightbox="media/public-ip-usage/create-firewall-policy.png":::

1. 在 [ **基本** ] 索引標籤下提供必要的詳細資料，然後選取 **[下一步： DNS 設定]** 。 

1. 在 [ **DNS** ] 索引標籤下選取 [ **停** 用]，然後選取 **[下一步：規則]**

1. 選取 [ **新增規則集合** ]，提供下列詳細資料，然後選取 [ **新增** ]，然後選取 **[下一步：威脅情報]** 。

   -  名稱
   -  規則集合類型-DNAT
   -  優先順序
   -  規則集合動作-允許
   -  規則的名稱
   -  來源類型- **IPaddress**
   -  來源-* *\** _
   -  通訊協定– _ *TCP**
   -  目的地埠– **80**
   -  目的地類型- **IP 位址**
   -  目的地– **公用 IP 位址**
   -  轉譯的位址- **Azure VMware 解決方案 Web 服務器私人 IP 位址**
   -  轉譯的埠- **Azure VMware 解決方案 Web 服務器埠**

1. 保留預設值，然後選取 **[下一步：中樞]** 。

1. 選取 [ **建立虛擬中樞的關聯** ]。

1. 從清單中選取一個中樞，然後選取 [ **新增** ]。

   :::image type="content" source="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png" alt-text="螢幕擷取畫面，顯示將轉換成 Scecured 虛擬中樞的選定中樞。" border="true" lightbox="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png":::

1. 選取 [下一步:標籤]。 

1.  (選擇性) 建立名稱和值組以將您的資源分類。 

1. 選取 **[下一步]： [檢查 + 建立]** ，然後選取 [ **建立** ]。

## <a name="limitations"></a>限制

您可以為每個 SDDCs 擁有100個公用 Ip。

## <a name="next-steps"></a>後續步驟

深入瞭解如何使用 [Azure 虛擬 WAN](../virtual-wan/virtual-wan-about.md)來使用公用 IP 位址。

