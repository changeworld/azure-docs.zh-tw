---
title: 什麼是 Azure Private Link 服務？
description: 瞭解 Azure Private Link 服務。
services: private-link
author: sumeetmittal
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: sumi
ms.openlocfilehash: 27dba675f82c4d34ec793cf492c18b293a6c8c77
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95544253"
---
# <a name="what-is-azure-private-link-service"></a>什麼是 Azure Private Link 服務？

Azure Private Link 服務是由 Azure Private Link 所支援的自有服務參考。 您可以針對在 [Azure Standard Load Balancer](../load-balancer/load-balancer-overview.md) 後方執行的服務啟用 Private Link 存取權，讓服務的取用者可以從自己的 vnet 私下進行存取。 您的客戶可以在其 VNet 內建立私人端點，並將其對應到此服務。 本文說明與服務提供者端相關的概念。 

:::image type="content" source="./media/private-link-service-overview/consumer-provider-endpoint.png" alt-text="Private link 服務工作流程" border="true":::

*圖： Azure Private Link 服務。*

## <a name="workflow"></a>工作流程

![Private Link 服務工作流程](media/private-link-service-overview/private-link-service-workflow.png)


*圖： Azure Private Link 服務工作流程。*

### <a name="create-your-private-link-service"></a>建立您的 Private Link 服務

- 將您的應用程式設定為在虛擬網路中的標準負載平衡器後方執行。 如果您的應用程式已設定于標準負載平衡器後方，則可以略過此步驟。   
- 建立參考上述負載平衡器的 Private Link 服務。 在 [負載平衡器選取] 流程中，選擇您要接收流量的前端 IP 設定。 針對 Private Link 服務，選擇 NAT IP 位址的子網。 建議您在子網中至少有8個 NAT IP 位址可用。 所有取用者流量的來源都是來自此私人 IP 位址集區給服務提供者。 選擇 Private Link 服務的適當屬性/設定。    

    > [!NOTE]
    > 只有 Standard Load Balancer 才支援 Azure Private Link 服務。 
    
### <a name="share-your-service"></a>共用您的服務

建立 Private Link 服務之後，Azure 會根據您為服務提供的名稱，產生名為「別名」的全域唯一名稱。 您可以與您的客戶離線共用您服務的別名或資源 URI。 取用者可以使用別名或資源 URI 來啟動 Private Link 連接。
 
### <a name="manage-your-connection-requests"></a>管理您的連接要求

當取用者起始連接之後，服務提供者可以接受或拒絕連線要求。 所有連接要求都會列在 Private Link 服務的 **privateendpointconnections** 屬性之下。
 
### <a name="delete-your-service"></a>刪除您的服務

如果 Private Link 服務已不再使用，您可以將它刪除。 不過，在您刪除服務之前，請確定沒有相關聯的私人端點連接。 您可以拒絕所有連接並刪除服務。

## <a name="properties"></a>屬性

Private Link 服務會指定下列屬性： 

|屬性 |說明  |
|---------|---------|
|布建狀態 (provisioningState)   |唯讀屬性，會列出 Private Link 服務的目前布建狀態。 適用的布建狀態為：「正在刪除;沒有均正在更新」。 當布建狀態為「成功」時，您已成功布建您的 Private Link 服務。        |
|別名 (別名)      | 別名是您服務的全域唯一隻讀字串。 它可協助您為服務遮罩客戶資料，同時為您的服務建立容易共用的名稱。 當您建立 Private Link 服務時，Azure 會為您的服務產生可與客戶共用的別名。 您的客戶可以使用此別名來要求您的服務連接。          |
|可見度 (可見度)      | 可見度是控制 Private Link 服務之曝光設定的屬性。 服務提供者可以選擇使用 Azure 角色型存取控制來限制其服務對訂用帳戶的風險， (Azure RBAC) 許可權、一組受限制的訂用帳戶或所有 Azure 訂用帳戶。          |
|自動核准 (autoApproval)     |   自動核准會控制 Private Link 服務的自動存取。 當您從這些訂用帳戶中的私人端點要求連線時，自動核准清單中指定的訂閱會自動核准。          |
|Load Balancer 前端 IP 設定 (loadBalancerFrontendIpConfigurations)     |    Private Link 服務會系結至 Standard Load Balancer 的前端 IP 位址。 所有以服務為目的地的流量都會到達 SLB 前端。 您可以設定 SLB 規則，將此流量導向至您的應用程式執行所在的適當後端集區。 負載平衡器前端 IP 設定與 NAT IP 配置不同。      |
| (Ipconfiguration) 的 NAT IP 設定    |    此屬性是指 Private Link 服務) IP 設定的 NAT (網路位址轉譯。 您可以從服務提供者的虛擬網路中的任何子網中選擇 NAT IP。 Private Link 服務會在 Private Link 流量上執行目的地端的 NAT。 這可確保來源 (取用者端) 和目的地 (服務提供者) 位址空間之間沒有 IP 衝突。 在 (服務提供者端) 的目的地端，NAT IP 位址會顯示為服務所接收所有封包的來源 IP，以及服務所傳送之所有封包的目的地 IP。       |
|私人端點連接 (privateEndpointConnections)      |  這個屬性會列出連接到 Private Link 服務的私人端點。 多個私人端點可以連接到相同的 Private Link 服務，而服務提供者可以控制個別私用端點的狀態。        |
|TCP Proxy V2 (EnableProxyProtocol)      |  這個屬性可讓服務提供者使用 tcp proxy v2 來抓取服務取用者的連接資訊。 服務提供者負責設定接收者設定，以便能夠剖析 proxy 通訊協定 v2 標頭。        |
|||


### <a name="details"></a>詳細資料

- 您可以從任何公用區域中的已核准私人端點存取 Private Link 服務。 您可以使用私人 VPN 或 ExpressRoute 連線，從相同的虛擬網路（地區對等互連 Vnet、全域對等互連 Vnet 和內部部署）連接私人端點。 
 
- 建立 Private Link 服務時，會建立資源生命週期的網路介面。 客戶無法管理這個介面。
 
- Private Link 服務必須部署在與虛擬網路和 Standard Load Balancer 相同的區域中。  
 
- 您可以從屬於不同 Vnet、訂用帳戶和/或 Active Directory 租使用者的多個私人端點存取單一 Private Link 服務。 連接會透過連接工作流程建立。 
 
- 您可以使用不同的前端 IP 設定，在相同的 Standard Load Balancer 上建立多個 Private Link 服務。 您可以針對每個 Standard Load Balancer 和每個訂用帳戶建立的 Private Link 服務數目有所限制。 如需詳細資訊，請參閱  [Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) \(部分機器翻譯\)。
 
- Private Link 服務可以有一個以上的 NAT IP 設定連結。 選擇一個以上的 NAT IP 設定可協助服務提供者調整規模。 目前，服務提供者可以針對每個 Private Link 服務指派最多8個 NAT IP 位址。 使用每個 NAT IP 位址，您可以為您的 TCP 連線指派更多埠，進而相應放大。將多個 NAT IP 位址新增至 Private Link 服務之後，您就無法刪除 NAT IP 位址。 這樣做的目的是為了確保在刪除 NAT IP 位址時，使用中的連線不會受到影響。


## <a name="alias"></a>Alias

**別名** 是您服務的全域唯一名稱。 它可協助您為服務遮罩客戶資料，同時為您的服務建立容易共用的名稱。 當您建立 Private Link 服務時，Azure 會為您的服務產生一個別名，讓您可以與客戶共用。 您的客戶可以使用此別名來要求您的服務連接。

別名是由三個部分所組成： *前置* 詞。*GUID*。*尾碼*

- 前置詞是服務名稱。 您可以選擇自己的前置詞。 建立「別名」之後，您就無法加以變更，因此請適當地選取您的首碼。  
- 將由平臺提供 GUID。 這有助於讓名稱成為全域唯一的。 
- 尾碼會附加 *azure： privatelinkservice* 

完成別名：  *前置* 詞。 {GUID}。** privatelinkservice  

## <a name="control-service-exposure"></a>控制服務暴露

Private Link 服務會提供選項，讓您透過「可見度」設定來控制服務的曝光。 您可以將服務設為私用，使其可從您所擁有的不同 Vnet 中取用 (僅) 的 Azure RBAC 許可權、限制暴露于您信任的有限訂用帳戶集，或將其設為公用，讓所有 Azure 訂用帳戶都能要求 Private Link 服務的連線。 您的可見度設定會決定取用者是否可以連接到您的服務。 

## <a name="control-service-access"></a>控制服務存取

當取用者 (由可見度設定) Private Link 服務的公開時，您可以在其 Vnet 中建立私人端點，並要求您的 Private Link 服務連接。 私人端點連線會在 Private Link 服務物件上的「擱置」狀態中建立。 服務提供者負責在連接要求上採取行動。 您可以核准連接、拒絕連接或刪除連接。 只有已核准的連線才能將流量傳送到 Private Link 服務。

您可以使用 Private Link 服務上的自動核准屬性，將核准連接的動作自動化。 自動核准是讓服務提供者 preapprove 一組訂用帳戶以自動存取其服務的能力。 客戶必須離線共用其訂閱，服務提供者才能將其新增至自動核准清單。 自動核准是可見度陣列的子集。 可見度會控制公開設定，而自動核准會控制您服務的核准設定。 如果客戶在自動核准清單中要求來自訂用帳戶的連線，則會自動核准連線，並建立連線。 服務提供者不需要再手動核准要求。 另一方面，如果客戶要求可見度陣列中訂用帳戶的連線，而不是自動核准陣列中的連線，則要求將會到達服務提供者，但服務提供者必須手動核准連接。

## <a name="getting-connection-information-using-tcp-proxy-v2"></a>使用 TCP Proxy v2 取得連接資訊

使用私人連結服務時，來自私人端點的封包來源 IP 位址是在服務提供者端使用從提供者的虛擬網路配置的 NAT IP (NAT) 轉譯的網路位址。 因此，應用程式會接收配置的 NAT IP 位址，而不是服務取用者的實際來源 IP 位址。 如果您的應用程式需要來自取用者的實際來源 IP 位址，您可以在服務上啟用 Proxy 通訊協定，並從 Proxy 通訊協定標頭取得資訊。 除了來源 IP 位址，proxy 通訊協定標頭也會攜帶私人端點的 LinkID。 來源 IP 位址和 LinkID 的組合可協助服務提供者唯一識別其取用者。 如需有關 Proxy 通訊協定的詳細資訊，請造訪 [這裡](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt)。 

這項資訊會使用自訂的型別長度值 (TLV) 向量進行編碼，如下所示：

自訂的 TLV 詳細資料：

|欄位 |長度 (八位)   |描述  |
|---------|---------|----------|
|類型  |1        |PP2_TYPE_AZURE (0xEE) |
|長度  |2      |值的長度|
|值  |1     |PP2_SUBTYPE_AZURE_PRI加值稅EENDPOINT_LINKID (0x01) |
|  |4        |UINT32 (4 個位元組) 代表私用端點的 LINKID。 以位元組由小到大格式編碼。|

 > [!NOTE]
 > 服務提供者負責確保在私人連結服務上啟用 proxy 通訊協定時，標準負載平衡器後方的服務會設定為根據 [規格](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt) 來剖析 proxy 通訊協定標頭。 如果私人連結服務上已啟用 proxy 通訊協定設定，但未設定服務提供者的服務來剖析標頭，要求將會失敗。 同樣地，如果服務提供者的服務需要 proxy 通訊協定標頭，而未在私人連結服務上啟用設定，則要求將會失敗。 一旦啟用 proxy 通訊協定設定，proxy 通訊協定標頭也會包含在從主機到後端虛擬機器的 HTTP/TCP 健康情況探查中，即使標頭中沒有任何用戶端資訊也一樣。 

## <a name="limitations"></a>限制

以下是使用 Private Link 服務時的已知限制：
- 僅 Standard Load Balancer 支援 
- 僅支援 IPv4 流量
- 僅支援 TCP 流量

## <a name="next-steps"></a>後續步驟
- [使用 Azure PowerShell 建立私人連結服務](create-private-link-service-powershell.md)
- [使用 Azure CLI 建立私人連結服務](create-private-link-service-cli.md)