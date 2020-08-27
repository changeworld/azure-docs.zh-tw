---
title: 異地災害復原 - Azure 事件中樞 | Microsoft Docs
description: 如何使用地理區域，在 Azure 事件中樞中進行容錯移轉並執行災害復原
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 1807c22645c3246f4cf18d723fc19da475e4d4f4
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934067"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Azure 事件中樞 - 異地災害復原 
當整個 Azure 區域或資料中心 (如果未使用[可用性區域](../availability-zones/az-overview.md)) 遇到停機時，最重要的是資料處理作業能夠繼續在不同的區域或資料中心運作。 因此，「地理災害復原」和「異地複寫」對於任何企業而言都是重要的功能。 Azure 事件中樞支援命名空間層級的地理災害復原和異地複寫。 

> [!NOTE]
> 異地災害復原功能僅適用於[標準和專用 SKU](https://azure.microsoft.com/pricing/details/event-hubs/)。  

## <a name="outages-and-disasters"></a>中斷與災害

請務必注意「中斷」和「災害」之間的差異。 「中斷」是暫時無法使用 Azure 事件中樞，而且會影響服務的某些元件，例如訊息存放區，或甚至整個資料中心。 不過，修正問題之後，事件中樞就可再次使用。 中斷通常不會導致訊息或其他資料遺失。 這類中斷的範例可能是資料中心停電。 某些中斷只是因為暫時性或網路問題而造成的短暫連線中斷。 

「災害」定義為永久或較長期遺失事件中樞叢集、Azure 區域或資料中心。 區域或資料中心不一定能再次使用，也可能會關閉數小時或數天。 這類災害的範例包括火災、水災或地震。 會變成永久的災害可能會導致某些訊息、事件或其他資料遺失。 不過，在大部分情況下，應該不會遺失資料，而且在備份資料中心之後，就可以復原訊息。

Azure 事件中樞的地理災害復原功能就是一個災害復原解決方案。 本文中所述的概念和工作流程適用於災害案例，不適用暫時性或暫時中斷。 如需 Microsoft Azure 中災害復原的詳細討論，請參閱[本文](/azure/architecture/resiliency/disaster-recovery-azure-applications)。

## <a name="basic-concepts-and-terms"></a>基本概念與術語

災害復原功能會實作中繼資料災害復原，並依賴主要和次要災害復原命名空間。 

異地災害復原功能僅適用於[標準和專用 SKU](https://azure.microsoft.com/pricing/details/event-hubs/)。 您不需要進行任何連接字串變更，因為連接是透過別名建立的。

本文中使用下列術語：

-  *別名*：您所設定的災害復原設定的名稱。 別名提供單一穩定完整網域名稱 (FQDN) 連接字串。 應用程式會使用這個別名連接字串連接到命名空間。 

-  *主要/次要命名空間*：對應到別名的命名空間。 主要命名空間是「主動」，並且會接收訊息，可以是現有或新的命名空間。 次要命名空間是「被動」，並不會接收訊息。 這兩者間的中繼資料會進行同步處理，因此，這兩者均能順暢地接受訊息，而不需進行任何應用程式程式碼或連接字串變更。 若要確保只有主動命名空間會接收訊息，您必須使用別名。 

-  *中繼資料*：實體 (例如事件中樞和取用者群組)；以及與命名空間相關聯之服務的屬性。 只有實體及其設定會自動複寫。 不會複寫訊息和事件。 

-  *容錯移轉*：啟用次要命名空間的程序。

## <a name="supported-namespace-pairs"></a>支援的命名空間配對
以下是支援的主要和次要命名空間組合：  

| 主要命名空間 | 次要命名空間 | 支援 | 
| ----------------- | -------------------- | ---------- |
| 標準 | 標準 | 是 | 
| 標準 | 專用 | 是 | 
| 專用 | 專用 | 是 | 
| 專用 | 標準 | 否 | 

> [!NOTE]
> 您無法將位於相同專用叢集中的命名空間配對。 您可以配對位於不同叢集中的命名空間。 

## <a name="setup-and-failover-flow"></a>設定和容錯移轉流程

下一節是容錯移轉程序的概觀，並說明如何設定初始容錯移轉。 

![1][]

### <a name="setup"></a>安裝程式

首先建立或使用現有的主要命名空間，以及新的次要命名空間，然後將這兩個命名空間配對。 此配對會為您提供可用來連接的別名。 由於您使用別名，因此不需變更連接字串。 只需將新的命名空間新增至您的容錯移轉配對。 最後，您應該新增某些監視來偵測是否需要容錯移轉。 在大部分情況下，服務是大型生態系統的一部分，因此不太可能自動容錯移轉，容錯移轉通常都必須與其餘子系統或基礎結構同步執行。

### <a name="example"></a>範例

在此案例的一個範例中，請考慮銷售點 (POS) 解決方案，它會發出訊息或事件。 事件中樞會將這些事件傳遞至某些對應或重新格式化解決方案，接下來將對應資料轉接到另一個系統，以進一步處理。 屆時，這些系統都會裝載於相同的 Azure 區域。 關於何時容錯移轉及容錯移轉哪個部分的決策，取決於基礎結構中的資料流量。 

您可以使用監視系統或使用自訂監視解決方案來自動容錯移轉。 不過，這類自動化會採用額外的計劃和工作，這部分不在本文的範圍內。

### <a name="failover-flow"></a>容錯移轉流程

如果您初始化容錯移轉，需要兩個步驟：

1. 如果發生另一個中斷，您會希望能夠再次進行容錯移轉。 因此，請設定另一個被動命名空間，並更新配對。 

2. 一旦主要命名空間再次可供使用之後，從先前的主要命名空間提取訊息。 然後，針對地理復原設定以外的一般訊息使用該命名空間，或者刪除舊的主要命名空間。

> [!NOTE]
> 僅支援失敗轉接語意。 在此案例中，您容錯移轉然後與新的命名空間重新配對。 不支援容錯復原；例如，在 SQL 叢集。 

![2][]

## <a name="management"></a>管理性

如果您發生錯誤；例如，您在初始化安裝期間配對錯誤的區域，您可以隨時中斷兩個命名空間的配對。 如果您想要使用配對的命名空間作為一般命名空間，請刪除別名。

## <a name="samples"></a>範例

[GitHub 上的範例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient)示範如何設定及初始化容錯移轉。 此範例示範下列概念︰

- 在 Azure Active Directory 中使用 Azure Resource Manager 與事件中樞所需的設定。 
- 執行範例程式碼所需的步驟。 
- 從目前的主要命名空間傳送和接收。 

## <a name="considerations"></a>考量

請注意此版本的下列考量：

1. 設計上，事件中樞異地災害復原並不會複寫資料，因此您無法在次要事件中樞上重複使用主要事件中樞的舊位移值。 建議您使用下列其中一種方法來重新啟動您的事件接收器：

- *EventPosition.FromStart()* - 如果您想要讀取次要事件中樞上的所有資料。
- *EventPosition.FromEnd()* - 如果您想要讀取連線到次要事件中樞之後的所有新資料。
- *EventPosition.FromEnqueuedTime(dateTime)* - 如果您想要讀取次要事件中樞上自指定日期和時間之後接收的所有資料。

2. 在您的容錯移轉規劃中，您也應該考慮時間因素。 例如，如果您中斷連線時間超過 15 到 20 分鐘，您可能會決定初始化容錯移轉。 
 
3. 未複寫任何資料這個事實表示未複寫目前作用中工作階段。 此外，重複的偵測和排程訊息可能無法運作。 新的工作階段、排程訊息及新的重複項目會運作。 

4. 容錯移轉複雜分散式基礎結構應該至少[演練](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan)一次。 

5. 同步處理實體可能需要一些時間，大約每分鐘 50-100 個實體。

## <a name="availability-zones"></a>可用性區域 

事件中樞標準 SKU 支援[可用性區域](../availability-zones/az-overview.md)，在 Azure 區域內提供錯誤隔離位置。 

> [!NOTE]
> 「Azure 事件中樞標準」的「可用性區域」支援僅適用於有可用性區域存在的 [Azure 區域](../availability-zones/az-region.md)。

使用 Azure 入口網站時，只能在新的命名空間上啟用可用性區域。 事件中樞不支援移轉現有的命名空間。 在命名空間上啟用區域備援之後，便無法停用。

![3][]

## <a name="private-endpoints"></a>私人端點
本節提供將異地災害復原與使用私人端點的命名空間搭配使用時的其他考慮。 若要瞭解如何在一般情況下搭配使用私人端點與事件中樞，請參閱[設定私人端點](private-link-service.md)。

### <a name="new-pairings"></a>新配對
如果您嘗試在具有私人端點的主要命名空間與沒有私人端點的次要命名空間之間建立配對，配對將會失敗。 只有當主要和次要命名空間都具有私人端點時，配對才會成功。 建議您在主要和次要命名空間，以及在建立私人端點的所在虛擬網路上使用相同的設定。  

> [!NOTE]
> 當您嘗試將具有私人端點的主要命名空間和次要命名空間配對時，驗證程序只會檢查次要命名空間上是否存在私人端點。 不會檢查端點是否正常運作，或在容錯移轉之後是否可運作。 您必須負責確保具有私人端點的次要命名空間在容錯移轉之後能如預期般運作。
>
> 若要測試主要和次要命名空間上的私人端點設定是否相同，請從虛擬網路外部傳送讀取要求 (例如：[Get Event Hub](/rest/api/eventhub/get-event-hub)) 給次要命名空間，並確認您收到來自服務的錯誤訊息。

### <a name="existing-pairings"></a>現有配對
如果主要和次要命名空間之間的配對已存在，在主要命名空間上的私人端點建立將會失敗。 若要解決此問題，請先在次要命名空間上建立私人端點，然後再為主要命名空間建立私人端點。

> [!NOTE]
> 我們允許對次要命名空間唯讀存取，也允許更新私人端點設定。 

### <a name="recommended-configuration"></a>建議的設定
建立應用程式和事件中樞命名空間的災害復原設定時，您必須針對裝載您應用程式主要和次要執行個體的虛擬網路，建立主要和次要事件中樞命名空間的私人端點。 

假設您有兩個虛擬網路：VNET-1、VNET 2，主要和次要命名空間是：EventHubs-Namespace1-Primary 和 EventHubs-Namespace2-Secondary。 您需要執行下列步驟： 

- 在 EventHubs-Namespace1-Primary 上，建立兩個私人端點，且這兩個端點使用 VNET-1 和 VNET-2 中的子網路
- 在 EventHubs-Namespace2-Secondary 上，建立兩個私人端點，且這兩個端點使用 VNET-1 和 VNET-2 中的相同子網路 

![私人端點和虛擬網路](./media/event-hubs-geo-dr/private-endpoints-virtual-networks.png)

這種方法的優點是，容錯移轉可能會發生在與事件中樞命名空間無關的應用程式層。 請考慮下列案例： 

**僅應用程式的容錯移轉：** 此案例中，應用程式不會存在於 VNET-1 中，但會移至 VNET-2。 由於在主要和次要命名空間的 VNET-1 和 VNET-2 上都設定了這兩個私人端點，應用程式會正常執行。 

**僅事件中樞命名空間的容錯移轉**：同樣的，此案例中由於在主要和次要命名空間的虛擬網路上都設定了這兩個私人端點，應用程式會正常執行。 

> [!NOTE]
> 如需虛擬網路的異地災害復原指引，請參閱[虛擬網路 - 商務持續性](../virtual-network/virtual-network-disaster-recovery-guidance.md)。
 
## <a name="next-steps"></a>後續步驟

* [GitHub 上的範例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient)會逐步解說簡單工作流程，以建立地理配對並在災害復原情況下起始容錯移轉。
* [REST API 參考](/rest/api/eventhub/)描述用來執行地理災害復原設定的 API。

如需事件中樞的詳細資訊，請造訪下列連結：

- 開始使用事件中心
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
* [事件中樞常見問題集](event-hubs-faq.md)
* [使用事件中樞的完整範例應用程式](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png
