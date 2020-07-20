---
title: 常見問題： SRE 和 DevOps |Microsoft Docs
titleSuffix: Azure
description: 常見問題：瞭解 SRE 和 DevOps 之間的關聯性
author: dnblankedelman
manager: efreeman
ms.service: site-reliability-engineering
ms.topic: article
ms.date: 04/22/2020
ms.author: dnb
ms.openlocfilehash: e917c609b484b1a58377fea2f6cdd75dde30ca6c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82196409"
---
# <a name="frequently-asked-questions-whats-the-relationship-between-sre-and-devops"></a>常見問題： SRE 與 DevOps 之間的關係為何？

有一組常見的問題會出現在網站可靠性工程和 DevOps 之間的關係，包括「它們是如何相同的？」 兩者有何不同？ 我們的組織中是否可以同時有這兩者？」。 本文會嘗試分享 SRE 和 DevOps 團體所提供的一些解答，讓我們更接近瞭解此關聯性。

## <a name="how-are-they-the-same"></a>它們有何不同？

SRE 和 DevOps 是為了回應包含的挑戰而建立和開發的現代化作業實務：

- 我們的生產環境和開發程式的複雜性日益增加
- 針對這些環境的持續運作增加商務相依性
- 無法以這些環境的大小線性調整工作力
- 需要更快移動，同時仍保有操作穩定性

這兩種作業實務都有重點，就是處理這些挑戰（例如監視/可檢視性、自動化、檔及共同作業軟體發展工具）的重要物件。

在 SRE 和 DevOps 之間的工具和工作區中，有相當大的差異。 當_網站可靠性活頁簿_將它放在外，「SRE 相信與 DevOps 相同，但有些許不同的原因。」

## <a name="three-different-ways-to-compare-the-two-operations-practices"></a>比較兩個作業實務的三種不同方式

SRE 與 DevOps 之間的相似之處是明確的。 其實有趣的是，兩者的差異或分離。 在這裡，我們提供三種方式來考慮其關聯性，以做為此問題的一些細微之處。 您可能不同意這些答案，但它們各自提供一個不錯的起點來進行討論。

### <a name="class-sre-implements-interface-devops"></a>「類別 SRE 實行介面 DevOps」

_網站可靠性活頁簿_（在我們的[資源書籍清單](../resources/books.md)中所述）討論 SRE 和 DevOps 的第一章。 該章節使用「類別 SRE 實作為介面 DevOps」片語作為其子標題。 這是為了建議（使用適用于開發人員的片語），SRE 可以被視為 DevOps 原理的特定執行方式。 隨著章節的說明，「DevOps 相對於如何在詳細層級執行作業是相當無提示的，而 SRE 在其實務方面更禁止使用。 對於這兩種關聯性的問題，其中一個可能的答案可能會被視為 DevOps 的其中一種可能的實現。

### <a name="sre-is-to-reliability-as-devops-is-to-delivery"></a>SRE 是 DevOps 到傳遞的可靠性

這項比較有點 muddied，因為 SRE 和 DevOps 都有多個定義，但它仍然有其效用。 從「如果您必須將每個作業實務提煉成一或兩個字，以反映其核心考慮」的問題開始，該怎麼做？」

如果我們從[網站可靠性工程中樞](../index.yml)使用此 SRE 定義：

> 網站可靠性工程是一項工程準則，其致力於協助組織持續達成適當的系統、服務和產品可靠性等級。

然後就可以輕鬆地說 SRE 的單字是「可靠性」。 將它直接放置在名稱的中間也會為此宣告提供一些絕佳的辨識項。

如果我們從[Azure DevOps 資源中心](https://docs.microsoft.com/azure/devops/learn/)使用此 DevOps 定義：

> DevOps 是人員、程序及產品的結合，以為使用者提供持續性的價值傳遞。

然後，類似的 DevOps 公司可以是「傳遞」。

因此，「SRE 的可靠性就是 DevOps 要傳遞」。

### <a name="direction-of-attention"></a>注意方向

這項答案會加上 Thomas Limoncelli 到我們的[資源書籍清單](../resources/books.md)中所述的_搜尋 SRE_書籍，並以 paraphrased 的貢獻來括住或略過。 他會注意到，DevOps 工程師主要專注于軟體發展生命週期管線，而不定期執行生產作業責任，同時 Sre 專注于生產作業，而不定期執行 SDLC 管線責任。

但更重要的是，他也繪製一個圖表，從一側的軟體發展程式開始，而生產作業則在另一方運作。 這兩個連線是由一般管線所連接，而這是為了讓開發人員使用程式碼、透過所需的測試和階段數 shepherd，然後將該程式碼移至生產環境。

Limoncelli 注意： DevOps 工程師會在開發環境開始，並自動執行生產的步驟。 完成後，他們會回到優化瓶頸。

另一方面，Sre 則著重于生產作業，並深入探討管線，做為改善最終結果的方法（基本上以相反方向運作）。

這是 SRE 和 DevOps 焦點方向的差異，可協助您加以區別。

## <a name="coexistence-in-the-same-organization"></a>相同組織中的共存

我們想要解決的最後一個問題是：「您在相同的組織中是否可以同時擁有 SRE 和 DevOps？」

這個問題的答案是「是！」 emphatic。

我們希望先前的答案能夠讓您瞭解這兩個作業實務如何重迭，而在不重迭時，如何在焦點方面互補。 具有既定 DevOps 實務的組織可以大規模試驗 SRE 實務（例如，嘗試 Sli 和 Slo），而不需要承諾建立 SRE 位置或小組。 這是相當常見的 SRE 採用模式。

## <a name="next-steps"></a>後續步驟

有興趣深入瞭解網站可靠性工程或 DevOps 嗎？ 查看我們的[網站可靠性工程中樞](../index.yml)和[Azure DevOps 資源中心](https://docs.microsoft.com/azure/devops/learn/)。
