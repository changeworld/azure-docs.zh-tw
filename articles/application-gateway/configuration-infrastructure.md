---
title: Azure 應用程式閘道基礎結構設定
description: 本文說明如何設定 Azure 應用程式閘道基礎結構。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: f214b0b0751f44ea1357f569fd814a7621af61ab
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397615"
---
# <a name="application-gateway-infrastructure-configuration"></a>應用程式閘道基礎結構設定

應用程式閘道基礎結構包括虛擬網路、子網、網路安全性群組和使用者定義的路由。

## <a name="virtual-network-and-dedicated-subnet"></a>虛擬網路和專用子網

應用程式閘道是您虛擬網路中的專用部署。 在您的虛擬網路中，應用程式閘道需要專用子網。 子網中可以有指定應用程式閘道部署的多個實例。 您也可以在子網中部署其他應用程式閘道。 但您無法在應用程式閘道子網中部署任何其他資源。 您無法在相同的子網上混合 Standard_v2 和標準 Azure 應用程式閘道。

> [!NOTE]
> 應用程式閘道子網路中目前不支援[虛擬網路服務端點原則](../virtual-network/virtual-network-service-endpoint-policies-overview.md)。

### <a name="size-of-the-subnet"></a>子網路的大小

應用程式閘道會針對每個實例使用一個私人 IP 位址，再加上另一個私人 IP 位址（若已設定私人前端 IP 位址）。

Azure 也會在每個子網中保留5個 IP 位址供內部使用：前四個和最後一個 IP 位址。 例如，請考慮15個沒有私人前端 IP 的應用程式閘道實例。 此子網至少需要20個 IP 位址：五個供內部使用，而15個用於應用程式閘道實例。

請考慮具有27個應用程式閘道實例的子網，以及私人前端 IP 的 IP 位址。 在此情況下，您需要33個 IP 位址：27個用於應用程式閘道實例，一個用於私人前端，而五個供內部使用。

 (Standard 或 WAF) SKU 的應用程式閘道最多可支援32個實例 (32 實例 IP 位址 + 1 個私人前端 IP + 5 個 Azure 保留的) –因此建議的子網大小下限為/26

應用程式閘道 (Standard_v2 或 WAF_v2 SKU) 最多可支援125個實例 (125 個實例 IP 位址 + 1 個私人前端 IP + 5 個 Azure 保留) –因此建議的子網大小下限為/24

## <a name="network-security-groups"></a>網路安全性群組

應用程式閘道上支援 (Nsg) 的網路安全性群組。 但有一些限制：

- 針對應用程式閘道 v1 SKU 的 TCP 通訊埠 65503-65534，以及 v2 SKU 的 TCP 通訊埠 65200-65535，您必須允許目的地子網路為 **Any** 且來源為 **GatewayManager** 服務標記的連入網際網路流量。 Azure 基礎結構通訊需要此連接埠範圍。 這些埠受到保護， (Azure 憑證) 鎖定。 外部實體（包括這些閘道的客戶）無法在這些端點上進行通訊。

- 無法封鎖輸出網際網路連線。 NSG 中的預設輸出規則允許網際網路連線能力。 建議您：

  - 請勿移除預設輸出規則。
  - 請勿建立拒絕任何輸出連線能力的其他輸出規則。

- 必須允許來自 **AzureLoadBalancer** **標記與目的地** 子網的流量。

### <a name="allow-access-to-a-few-source-ips"></a>允許存取一些來源 Ip

針對此案例，請在應用程式閘道子網上使用 Nsg。 依優先順序將下列限制放在子網上：

1. 允許來自來源 IP 或 IP 範圍的連入流量，以目的地為整個應用程式閘道子網位址範圍和目的地埠作為您的輸入存取埠，例如 HTTP 存取的埠80。
2. 允許來自來源作為 **GatewayManager** 服務標籤和目的地的連入要求，作為應用程式閘道 v1 SKU 的 **任何** 和目的地埠（65503-65534），以及用於 [後端健康狀態通訊](./application-gateway-diagnostics.md)的 v2 SKU 埠65200-65535。 Azure 基礎結構通訊需要此連接埠範圍。 這些埠受到保護， (Azure 憑證) 鎖定。 若沒有適當的憑證，外部實體就無法在這些端點上起始變更。
3. 允許傳入的 Azure Load Balancer 探查 ( *AzureLoadBalancer* 標籤) 以及 [網路安全性群組](../virtual-network/network-security-groups-overview.md)上 ( *VirtualNetwork* 標籤) 的輸入虛擬網路流量。
4. 使用全部拒絕規則來封鎖所有其他連入流量。
5. 允許所有目的地的網際網路輸出流量。

## <a name="supported-user-defined-routes"></a>支援的使用者定義路由 

> [!IMPORTANT]
> 在應用程式閘道子網上使用 Udr 可能會導致 [後端健康狀態視圖](./application-gateway-diagnostics.md#back-end-health) 中的健全狀況狀態顯示為 [ **不明** ]。 它也可能會造成應用程式閘道記錄和計量的產生失敗。 建議您不要在應用程式閘道子網上使用 Udr，如此您就可以查看後端健康情況、記錄和計量。

- **v1**

   在 v1 SKU 中，應用程式閘道子網 (Udr) 支援使用者定義的路由，只要它們不會改變端對端要求/回應通訊。 例如，您可以在應用程式閘道子網中設定 UDR，以指向防火牆設備以進行封包檢查。 但是，您必須確定封包在檢查之後可以到達其預定目的地。 如果無法這麼做，可能會導致健康情況探查或流量路由行為不正確。 這包括學習到的路由，或虛擬網路中的 Azure ExpressRoute 或 VPN 閘道所傳播的預設 0.0.0.0/0 路由。 必須將 0.0.0.0/0 重新導向至內部部署 (強制通道) 不支援 v1 的任何情況。

- **v2**

   針對 v2 SKU，有支援和不支援的案例：

   **v2 支援的案例**
   > [!WARNING]
   > 路由表的設定不正確可能會導致應用程式閘道 v2 中的非對稱路由。 確定所有管理/控制平面流量都會直接傳送到網際網路，而不是透過虛擬裝置。 記錄和計量也可能受到影響。


  **案例 1** ： UDR 停用邊界閘道協定 (BGP) 將傳播傳送至應用程式閘道子網

   有時候預設閘道路由 (0.0.0.0/0) 是透過與應用程式閘道虛擬網路相關聯的 ExpressRoute 或 VPN 閘道來通告。 這會中斷管理平面流量，這需要網際網路的直接路徑。 在這種情況下，UDR 可用來停用 BGP 路由傳播。 

   若要停用 BGP 路由傳播，請使用下列步驟：

   1. 在 Azure 中建立路由表資源。
   2. 停用 **虛擬網路閘道路由傳播** 參數。 
   3. 將路由表與適當的子網產生關聯。 

   啟用此案例的 UDR 應該不會中斷任何現有的設置。

  **案例 2** ： UDR 至將 0.0.0.0/0 導向至網際網路

   您可以建立 UDR，將 0.0.0.0/0 流量直接傳送到網際網路。 

  **案例 3** ：使用 kubenet Azure Kubernetes Service 的 UDR

  如果您使用 kubenet 搭配 Azure Kubernetes Service (AKS) 和應用程式閘道輸入控制器 (AGIC) ，您將需要路由表，以允許從應用程式閘道傳送至 pod 的流量路由傳送至正確的節點。 如果您使用 Azure CNI，就不需要這麼做。 

  若要使用路由表來允許 kubenet 運作，請依照下列步驟執行：

  1. 移至 AKS 所建立的資源群組 (資源群組的名稱應該以 "MC_" 開頭 ) 
  2. 在該資源群組中尋找 AKS 所建立的路由表。 路由表應填入下列資訊：
     - 位址首碼應該是您想要在 AKS 中到達之 pod 的 IP 範圍。 
     - 下一個躍點類型應該是虛擬裝置。 
     - 下一個躍點位址應該是裝載 pod 之節點的 IP 位址。
  3. 將此路由表與應用程式閘道子網建立關聯。 
    
  **v2 不支援的案例**

  **案例 1** ：虛擬裝置的 UDR

  任何需要透過任何虛擬裝置、中樞/輪輻虛擬網路或內部部署來重新導向 0.0.0.0/0 的案例，都不支援 V2 的 (強制通道) 。

## <a name="next-steps"></a>後續步驟

- [瞭解前端 IP 位址](configuration-front-end-ip.md)設定。