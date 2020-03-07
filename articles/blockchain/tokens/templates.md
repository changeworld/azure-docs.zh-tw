---
title: Azure 區塊鏈 token 範本
description: Azure 區塊鏈 token 範本是標準化且可重複使用的範本，可簡化建立和部署以總帳為基礎的權杖。
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9600a6a251552acd319cc68d2bd281584d65546d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78360726"
---
# <a name="azure-blockchain-tokens-templates"></a>Azure 區塊鏈 token 範本

[!INCLUDE [Preview note](./includes/preview.md)]

Azure 區塊鏈 token 範本是一種標準化且可重複使用的範本，可簡化建立和部署以總帳為基礎的權杖。 範本是由根據[Token 分類法架構（.ttf）](overview.md#token-taxonomy-framework)文法的公式所組成。 文法包含基底 token 類型，以及權杖的行為集合。  

例如， **τϜ {d，m，b，r}** token 範本描述變幻莫測基底 token，其為 dividable、mintable、burnable，並具有角色支援。
  
## <a name="base-token-types"></a>基底 token 類型

針對您的特定資產定義和建立以總帳為基礎的權杖時，請務必考慮要使用的基底權杖。

### <a name="fungible"></a>變幻莫測

變幻莫測 token （τF）可以彼此交換值，只要它們位於相同的類別或數列中即可。 一個權杖的值與另一個權杖相同，或指定的權杖數量與另一個相同數量的值相同。 例如，貨幣是變幻莫測 token。 如果兩個人都持有美元帳單，他們就可以交換這些貨幣帳單而不會產生任何結果。 貨幣物料單具有相等的值。 

### <a name="non-fungible"></a>非變幻莫測

非變幻莫測的權杖（τN）無法與相同類型的其他權杖互換，因為它們通常會有不同的值。 例如，屬性標題是不可變幻莫測的 token。 由於單位的位置或單位所在的樓層，屬性標題在單元複雜的兩個不同的單元中不一定是相等的值。 這兩個屬性標題標記的認知值不相等。

### <a name="hybrid"></a>混合式

混合式權杖是具有變幻莫測 token 和非變幻莫測 token 之元件的權杖。 「混合式權杖」是一種基底 token 類型，它擁有另一個 token 類型的類別。

#### <a name="hybrid-non-fungible-base-with-fungible-segments"></a>具有變幻莫測區段的混合式非變幻莫測基底

具有變幻莫測區段權杖的混合式非變幻莫測基底，其具有具有變幻莫測 token 區段的非變幻莫測基底。
例如，音樂會票證是混合式權杖，其中音樂會的日期和時間是非變幻莫測的基底 token。 給定音樂會的各種座位區段中的票證是具有變幻莫測 token 的區段。 這些票證會在其個別的座位區段中交換，但不會跨區段。

#### <a name="hybrid-fungible-base-with-non-fungible-segments"></a>具有非變幻莫測區段的混合式變幻莫測基底

具有非變幻莫測區段權杖的混合式變幻莫測基底具有非變幻莫測 token 區段的變幻莫測基底。 例如，以抵押支援的安全性是一種混合式權杖，其中多名擁有者是分散于許多擁有者的變幻莫測基底。 安全性是可互換的。 個別的還有房屋貨款是非變幻莫測的區段，代表特定的抵押支援安全性。

## <a name="token-behaviors"></a>權杖行為

權杖行為會定義權杖的功能或限制。 此行為包含屬於權杖定義之一部分的支援屬性。 行為可以套用至所有 token 類型或只套用到其中一個。 行為可以是內部或外部，視行為的影響而定。 內部行為會啟用或限制權杖本身的屬性。 外部行為可讓或限制外部執行者的行為調用。

如需 Azure 區塊鏈 token 支援的權杖分類法架構（.TTF）權杖行為的詳細資訊，請參閱[token 複合性](composability.md)。

## <a name="pre-built-token-templates"></a>預先建立的權杖範本

Azure 區塊鏈權杖提供四個預先建立的權杖範本，可在不修改的情況下使用。 您可以針對大部分的使用案例呼叫這些預先建立的範本，以快速開始建立、部署和管理您的權杖。

### <a name="commodity-tokens"></a>商品權杖

商品權杖具有一致的價值，且為可轉移。 例如，石油或能源的一個單位。

**τF {~ d，t，m，b，r}** -變幻莫測、整體、可轉換、mintable、burnable，並具有角色支援

許多區塊鏈案例都需要供應鏈或多個組織的透明度和可見度。 商品權杖是以這些常見的使用案例為基礎。 權杖可互換且一致。 商用 token 範本具有彈性且可自訂的中繼資料。

### <a name="qualified-tokens"></a>限定的權杖

限定的權杖代表所獲得的內容，且通常與一個實體相關聯，而且無法傳送。 例如，文憑或停車違規。

**τN {s，~ t}** -非變幻莫測、singleton 和不可轉移

各種不同的審查和證明案例都需要權杖的擁有權不能變更。 有一組使用案例，需要提供限定的 token，不論關聯性是否良好或不良。

### <a name="asset-tokens"></a>資產權杖

資產權杖具有與專案相依的唯一值，而且不會商品化。 例如，博物館成品或屬性標題。

**τN {s，t}** -非變幻莫測、singleton 和可轉換

資產權杖可能與商品權杖混淆。 這兩個權杖的主要差異在於資產權杖本質上是唯一的，而值則與其本身的權杖類型無關。 例如，建立的演出者之類的一段藝術圖案，就是資產 token。 不過，蒙娜 Lisa 的美工圖案會視為商用 token。 同樣地，屬性標題是資產 token，因為該值存在於屬性的主觀特性中。

### <a name="ticket-tokens"></a>票證權杖

票證權杖具有一致的值，但通常會過期。 例如，平面票證。

**τN {m，b，r}** -非變幻莫測、mintable、burnable，並具有角色支援。

票證權杖的到期日通常會與一般的商品權杖不同。 例如，飛機票證、音樂會票證或體育票證全都有指派的座位的選項，具有特定的使用日期。 您無法輕易地交換日期或座位區域之間的票證。

## <a name="next-steps"></a>後續步驟

如果您需要更多的案例彈性，請瞭解如何使用[權杖複合性](composability.md)建立您自己的權杖範本。
