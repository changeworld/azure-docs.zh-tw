---
title: Azure 事件方格-夥伴事件
description: 使用 Azure 事件方格，將來自協力廠商事件方格 SaaS 和 PaaS 合作夥伴的事件直接傳送至 Azure 服務。
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 87d1d40b3696229344b0b5c20d06d9d993a514a4
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93102881"
---
# <a name="partner-events-in-azure-event-grid-preview"></a>Azure 事件方格中的合作夥伴事件 (預覽) 
「 **夥伴事件** 」功能可讓協力廠商 SaaS 提供者從其服務發佈事件，讓可訂閱這些事件的取用者可以使用這些事件。 它藉由公開「訂閱者」用來取用事件的 [主題](concepts.md#topics) 類型（ **合作夥伴主題** ），為協力廠商事件來源提供第一方體驗。 它也提供乾淨的 pub sub 模型，方法是分隔事件發行者和訂閱者所使用之資源的考慮和擁有權。

> [!NOTE]
> 如果您是使用 Event Grid 的新手，請參閱 [總覽](overview.md)、 [概念](concepts.md)和 [事件處理常式](event-handlers.md)。

## <a name="what-is-partner-events-to-a-publisher"></a>什麼是發行者的夥伴活動？
若為事件發行者，「夥伴事件」功能可讓「發行者」執行下列工作：

- 將其事件來源上線至事件方格
- 建立命名空間 (可發佈事件的端點) 
- 在 Azure 中建立訂閱者擁有並用來取用事件的合作夥伴主題

## <a name="what-is-partner-events-to-a-subscriber"></a>什麼是訂閱者的合作夥伴事件？
若為訂閱者，合作夥伴事件功能可讓他們在 Azure 中建立合作夥伴主題，以取用協力廠商事件來源的事件。 事件耗用量的實現方式是建立事件訂閱，以將 (發送) 事件傳送給訂閱者的事件處理常式。

## <a name="why-should-i-use-partner-events"></a>為什麼應該使用合作夥伴事件？
如果您有下列一或多個需求，您可能會想要使用夥伴事件。

### <a name="for-publishers"></a>針對發行者

- 您想要能夠在 Azure 上使用您的事件的機制。 您的使用者可以使用他們所擁有和管理的夥伴主題和事件訂閱，來篩選和路由傳送這些事件。 您可以使用其他整合方法，例如 [主題](custom-topics.md) 和 [網域](event-domains.md)。 但是，他們不允許在發行者與訂閱者之間) 擁有權、管理和計費等資源 (夥伴主題之間進行清楚的分隔。 此外，此方法提供更直覺的使用者體驗，讓您輕鬆地探索及使用合作夥伴主題。
- 您想要將事件發行至單一端點，也就是命名空間的端點。 而且，您想要能夠篩選這些事件，以便只能使用其中的子集。 
- 您希望能夠看到與已發佈事件相關的計量。
- 您想要針對您的事件使用 [Cloud Events 1.0](https://cloudevents.io/) 架構。

### <a name="for-subscribers"></a>訂閱者

- 您想要從 [協力廠商發行者](#available-third-party-event-publishers) 訂閱事件，並使用 Azure 或其他位置上的事件處理常式來處理事件。
- 您想要利用一組豐富的路由功能和 [目的地/事件處理常式](overview.md#event-handlers) 來處理協力廠商來源的事件。 
- 您想要執行鬆散結合的架構，讓訂閱者/事件處理常式知道使用的訊息代理程式是否存在。 
- 您需要具有重試支援的彈性推送傳遞機制，以及至少一次的語法。
- 您想要針對您的事件使用 [Cloud Events 1.0](https://cloudevents.io/) 架構。 


## <a name="available-third-party-event-publishers"></a>可用的協力廠商事件發行者
協力廠商事件發行者必須經過 [上執行緒序，訂閱者才能開始](partner-onboarding-overview.md) 取用其事件。 

如果您是訂閱者，而且想要讓協力廠商服務透過事件方格公開其事件， 

### <a name="auth0"></a>Auth0
**Auth0** 是第一個可用的合作夥伴發行者。 您可以建立 [Auth0 合作夥伴主題](auth0-overview.md) ，以連接 Auth0 和 Azure 帳戶。 這項整合可讓您即時回應、記錄和監視 Auth0 事件。 若要試用，請參閱將 [Azure 事件方格與 Auto0 整合](auth0-how-to.md)

如果您想要讓協力廠商服務透過事件方格公開其事件，請在 [User Voice 入口網站](https://feedback.azure.com/forums/909934-azure-event-grid)上提交構想。
 
## <a name="resources-managed-by-event-publishers"></a>由事件發行者管理的資源
事件發行者會建立和管理下列資源：

### <a name="partner-registration"></a>夥伴註冊
註冊會保存與發行者相關的一般資訊。 它會定義一種夥伴主題，在使用者嘗試建立夥伴主題時，在 Azure 入口網站中顯示為選項。 發行者可能會公開一或多個合作夥伴主題類型，以符合其訂閱者的需求。 也就是說，發行者可能會為不同服務的事件建立不同的註冊 (夥伴主題類型) 。 例如，對於人力資源 (HR) 服務而言，發行者可能會定義事件的夥伴主題，例如員工加入、員工提升，以及員工離職。 

請記住下列幾點：

- 只有 Azure 核准的夥伴註冊可以看見。 
- 註冊是全域的。 也就是它們不會與特定的 Azure 區域相關聯。
- 註冊是選擇性的資源。 但是，我們建議您 (為發行者) 建立註冊。 它可讓使用者在 [Azure 入口網站](https://portal.azure.com/#create/Microsoft.EventGridPartnerTopic)的 [ **建立夥伴] 主題** 頁面上探索您的主題。 然後，使用者可以選取事件種類 (例如，已加入員工、員工離職等等。建立事件訂閱時，) 。

### <a name="namespace"></a>命名空間
就像 [自訂主題](custom-topics.md) 和 [網域](event-domains.md)一樣，夥伴命名空間是發佈事件的區域端點。 它是透過發行者建立和管理事件通道的命名空間。 命名空間也可作為事件通道的容器資源。

### <a name="event-channels"></a>活動通道
事件通道是夥伴主題的鏡像資源。 當發行者在發行者的 Azure 訂用帳戶中建立事件通道時，也會在訂閱者的 Azure 訂用帳戶下建立夥伴主題。 針對事件通道所執行的作業 (除了 GET) 之外，也會套用到對應的訂閱者夥伴主題，甚至是刪除。 不過，只有合作夥伴主題是可設定訂閱和事件傳遞的資源類型。

## <a name="resources-managed-by-subscribers"></a>由訂閱者管理的資源 
訂閱者可以使用「發行者」所定義的夥伴主題，而它是唯一看到及管理的資源類型。 建立夥伴主題之後，訂閱者使用者可以建立事件訂閱，將篩選規則定義至 [目的地/事件處理常式](overview.md#event-handlers)。 對訂閱者而言，夥伴主題及其相關聯的事件訂用帳戶會提供與 [自訂主題](custom-topics.md) 相同的豐富功能，以及其相關的訂用帳戶 (s) 有一個值得注意的差異：合作夥伴主題只支援 [Cloud Events 1.0 架構](cloudevents-schema.md)，提供比其他支援的架構更豐富的功能集。

## <a name="pricing"></a>定價
合作夥伴主題的計費方式是使用事件方格時所執行的作業數目。 如需使用做為計費和詳細價格資訊基礎之所有類型作業的詳細資訊，請參閱 [事件方格定價](https://azure.microsoft.com/pricing/details/event-grid/)。

## <a name="limits"></a>限制
如需合作夥伴主題之限制的詳細資訊，請參閱 [事件方格服務限制](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits) 。


## <a name="next-steps"></a>下一步

- [Auth0 合作夥伴主題](auth0-overview.md)
- [如何使用 Auth0 合作夥伴主題](auth0-how-to.md)
- [變成事件方格合作夥伴](partner-onboarding-overview.md)