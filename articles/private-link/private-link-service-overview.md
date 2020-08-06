---
title: 什麼是 Azure 私人連結服務？
description: 瞭解 Azure 私人連結服務。
services: private-link
author: sumeetmittal
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: sumi
ms.openlocfilehash: a6bbb2abe24eba96fd2c55b7aaf15ccd8ae33530
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/04/2020
ms.locfileid: "87760937"
---
# <a name="what-is-azure-private-link-service"></a>什麼是 Azure 私人連結服務？

Azure Private Link 服務是由 Azure Private Link 所支援的自有服務參考。 您在[Azure Standard Load Balancer](../load-balancer/load-balancer-standard-overview.md)之後執行的服務可以啟用私人連結存取，讓服務的取用者可以從自己的 vnet 私下存取它。 您的客戶可以在其 VNet 內建立私人端點，並將其對應至此服務。 本文說明與服務提供者端相關的概念。 

:::image type="content" source="./media/private-link-service-overview/consumer-provider-endpoint.png" alt-text="私人連結服務工作流程" border="true":::

*圖： Azure 私人連結服務。*

## <a name="workflow"></a>工作流程

![私人連結服務工作流程](media/private-link-service-overview/private-link-service-workflow.png)


*圖： Azure 私人連結服務工作流程。*

### <a name="create-your-private-link-service"></a>建立您的私用連結服務

- 將您的應用程式設定為在虛擬網路中的標準負載平衡器後方執行。 如果您已在標準負載平衡器後方設定應用程式，您可以略過此步驟。   
- 建立參考上述負載平衡器的私用連結服務。 在負載平衡器選取程式中，選擇您想要接收流量的前端 IP 設定。 為私人連結服務選擇 NAT IP 位址的子網。 建議您在子網中至少有8個可用的 NAT IP 位址。 所有取用者流量都是源自于此私人 IP 位址集區到服務提供者。 為私人連結服務選擇適當的屬性/設定。    

    > [!NOTE]
    > 只有 Standard Load Balancer 支援 Azure 私人連結服務。 
    
### <a name="share-your-service"></a>共用您的服務

建立私用連結服務之後，Azure 會根據您為服務提供的名稱，產生名為「別名」的全域唯一名稱。 您可以將服務的別名或資源 URI 與您的客戶離線共用。 取用者可以使用別名或資源 URI 來啟動私人連結連接。
 
### <a name="manage-your-connection-requests"></a>管理您的連接要求

取用者起始連線之後，服務提供者可以接受或拒絕連線要求。 所有連線要求都會列在私人連結服務的**privateendpointconnections**屬性底下。
 
### <a name="delete-your-service"></a>刪除您的服務

如果私用連結服務不再使用，您可以將它刪除。 不過，在您刪除服務之前，請確定沒有與其相關聯的私用端點連線。 您可以拒絕所有連接並刪除服務。

## <a name="properties"></a>屬性

私人連結服務會指定下列屬性： 

|屬性 |說明  |
|---------|---------|
|布建狀態 (provisioningState)   |唯讀屬性，列出私人連結服務目前的布建狀態。 適用的布建狀態為： "刪除;發生均正在更新 "。 當布建狀態為「成功」時，您已成功布建您的私用連結服務。        |
|別名 (別名)      | Alias 是您服務的全域唯一隻讀字串。 它可協助您遮罩服務的客戶資料，同時為您的服務建立容易共用的名稱。 當您建立私人連結服務時，Azure 會為您的服務產生可與您的客戶共用的別名。 您的客戶可以使用此別名來要求服務的連線。          |
|可見度 (可見度)      | 可見度是控制私用連結服務之公開設定的屬性。 服務提供者可以選擇使用角色型存取控制來限制服務對訂用帳戶的風險， (RBAC) 許可權、一組有限的訂用帳戶或所有 Azure 訂用帳戶。          |
|自動核准 (自動核准)     |   自動核准會控制對私人連結服務的自動化存取。 從這些訂用帳戶中的私人端點要求連線時，會自動核准自動核准清單中指定的訂閱。          |
|Load Balancer 前端 IP 設定 (loadBalancerFrontendIpConfigurations)     |    私人連結服務會系結至 Standard Load Balancer 的前端 IP 位址。 所有以服務為目標的流量都會到達 SLB 的前端。 您可以設定 SLB 規則，將此流量導向您的應用程式執行所在的適當後端集區。 負載平衡器前端 IP 設定與 NAT IP 設定不同。      |
|NAT IP 設定 (Ipconfiguration)     |    此屬性會參照適用于私人連結服務的 NAT (網路位址轉譯) IP 設定。 您可以從服務提供者之虛擬網路中的任何子網選擇 NAT IP。 私用連結服務會在私人連結流量上執行目的地端 NAT。 這可確保來源 (取用者端) 與目的地 (服務提供者) 位址空間之間沒有 IP 衝突。 在目的地端 (服務提供者端) 上，NAT IP 位址會顯示為服務所接收之所有封包的來源 IP，以及您的服務所傳送之所有封包的目的地 IP。       |
| (privateEndpointConnections) 的私人端點連接     |  此屬性會列出連接到私人連結服務的私人端點。 多個私人端點可以連接到相同的私用連結服務，而服務提供者可以控制個別私人端點的狀態。        |
|TCP Proxy V2 (EnableProxyProtocol)      |  這個屬性可讓服務提供者使用 tcp proxy v2 來抓取關於服務取用者的連線資訊。 服務提供者負責設定接收者配置，使其能夠剖析 proxy 通訊協定 v2 標頭。        |
|||


### <a name="details"></a>詳細資料

- 您可以從任何公用區域中已核准的私人端點存取私人連結服務。 您可以使用私人 VPN 或 ExpressRoute 連線，從相同的虛擬網路、區域內對等互連 Vnet、全域對等互連 Vnet 和內部部署存取私用端點。 
 
- 建立私用連結服務時，會為資源的生命週期建立網路介面。 客戶無法管理此介面。
 
- 私人連結服務必須部署在與虛擬網路和 Standard Load Balancer 相同的區域中。  
 
- 單一私人連結服務可以從屬於不同 Vnet、訂用帳戶及/或 Active Directory 租使用者的多個私人端點存取。 連接是透過連接工作流程所建立。 
 
- 您可以使用不同的前端 IP 設定，在相同的 Standard Load Balancer 上建立多個私人連結服務。 針對每個 Standard Load Balancer 和每個訂用帳戶，您可以建立的私人連結服務數目有所限制。 如需詳細資訊，請參閱  [Azure 限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits) \(部分機器翻譯\)。
 
- 私人連結服務可以有一個以上的 NAT IP 設定連結。 選擇一個以上的 NAT IP 設定可協助服務提供者進行調整。 目前，服務提供者最多可以為每個私人連結服務指派八個 NAT IP 位址。 您可以使用每個 NAT IP 位址，為您的 TCP 連線指派更多埠，進而相應放大。將多個 NAT IP 位址新增到私人連結服務之後，您就無法刪除 NAT IP 位址。 這是為了確保在刪除 NAT IP 位址時，不會影響作用中的連接。


## <a name="alias"></a>Alias

**別名**是您服務的全域唯一名稱。 它可協助您遮罩服務的客戶資料，同時為您的服務建立容易共用的名稱。 當您建立私人連結服務時，Azure 會為您的服務產生可與您的客戶共用的別名。 您的客戶可以使用此別名來要求服務的連線。

別名是由三個部分組成：*前置*詞。*GUID*。*尾碼*

- 前置詞是服務名稱。 您可以挑選自己的前置詞。 建立「別名」之後，您就無法加以變更，因此請適當地選取您的前置詞。  
- GUID 將由平臺提供。 這有助於讓名稱成為全域唯一的。 
- 尾碼會附加 Azure： *region*. privatelinkservice 

完成別名：*前置*詞。 {GUID}。* *privatelinkservice  

## <a name="control-service-exposure"></a>控制服務公開

私人連結服務可讓您透過「可見度」設定來控制服務的公開。 您可以將服務設為私用，以從您所擁有的不同 Vnet 取用 (RBAC 許可權僅) 、限制暴露于您信任的一組有限的訂用帳戶，或將其設為公開，讓所有 Azure 訂用帳戶都可以要求私人連結服務上的連線。 您的可見度設定會決定取用者是否可以連接到您的服務。 

## <a name="control-service-access"></a>控制服務存取

 (由可見度設定控制的取用者，) 到您的私人連結服務，可以在其 Vnet 中建立私用端點，並要求連線到您的私人連結服務。 私人端點連線將會在私人連結服務物件上以「擱置」狀態建立。 服務提供者會負責處理連接要求。 您可以核准連接、拒絕連線，或刪除連接。 只有已核准的連線可以將流量傳送至私用連結服務。

您可以使用私人連結服務上的自動核准屬性，將核准連接的動作自動化。 自動核准是讓服務提供者 preapprove 一組訂用帳戶以自動存取其服務的功能。 客戶必須離線共用其訂閱，服務提供者才能新增至自動核准清單。 自動核准是可見度陣列的子集。 可見度可控制公開設定，而自動核准則會控制您服務的核准設定。 如果客戶從自動核准清單中的訂用帳戶要求連線，則會自動核准連線，並建立連接。 服務提供者不需要再手動核准該要求。 另一方面，如果客戶要求可見度陣列中的訂用帳戶連線，而不是自動核准陣列中的連接，則要求會到達服務提供者，但服務提供者必須手動核准連線。

## <a name="getting-connection-information-using-tcp-proxy-v2"></a>使用 TCP Proxy v2 取得連接資訊

使用私人連結服務時，來自私人端點之封包的來源 IP 位址是使用從提供者的虛擬網路配置的 NAT IP，在服務提供者端 (NAT) 轉譯的網路位址。 因此，應用程式會接收配置的 NAT IP 位址，而不是服務取用者的實際來源 IP 位址。 如果您的應用程式需要取用者端的實際來源 IP 位址，您可以在您的服務上啟用 Proxy 通訊協定，並從 proxy 通訊協定標頭抓取資訊。 除了來源 IP 位址之外，proxy 通訊協定標頭也會攜帶私用端點的 LinkID。 來源 IP 位址和 LinkID 的組合可協助服務提供者唯一識別其取用者。 如需 Proxy 通訊協定的詳細資訊，請造訪[這裡](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt)。 

此資訊會使用自訂的類型-長度值（ (TLV) 向量）進行編碼，如下所示：

自訂的 TLV 詳細資料：

|欄位 |長度 (八位)   |描述  |
|---------|---------|----------|
|類型  |1        |PP2_TYPE_AZURE (0xEE) |
|長度  |2      |值的長度|
|值  |1     |PP2_SUBTYPE_AZURE_PRI加值稅EENDPOINT_LINKID (0x01) |
|  |4        |UINT32 (4 位元組) 代表私用端點的 LINKID。 以小 endian 格式編碼。|

 > [!NOTE]
 > 服務提供者會負責確保標準負載平衡器後方的服務已設定為在私人連結服務上啟用 proxy 通訊協定時，根據[規格](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt)來剖析 proxy 通訊協定標頭。 如果已在私人連結服務上啟用 proxy 通訊協定設定，但未將服務提供者的服務設定為剖析標頭，則要求將會失敗。 同樣地，如果服務提供者的服務預期會有 proxy 通訊協定標頭，而私人連結服務並未啟用此設定，則要求將會失敗。 一旦啟用 proxy 通訊協定設定，proxy 通訊協定標頭也會包含在從主機到後端虛擬機器的 HTTP/TCP 健全狀況探查中，即使標頭中不會有用戶端資訊也一樣。 

## <a name="limitations"></a>限制

以下是使用私用連結服務時的已知限制：
- 僅在 Standard Load Balancer 上支援 
- 僅支援 IPv4 流量
- 僅支援 TCP 流量

## <a name="next-steps"></a>後續步驟
- [使用 Azure PowerShell 建立私人連結服務](create-private-link-service-powershell.md)
- [使用 Azure CLI 建立私人連結服務](create-private-link-service-cli.md)
