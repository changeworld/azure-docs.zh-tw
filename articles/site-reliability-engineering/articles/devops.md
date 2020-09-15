---
title: 常見問題： SRE 和 DevOps |Microsoft Docs
titleSuffix: Azure
description: 常見問題：瞭解 SRE 與 DevOps 之間的關聯性
author: dnblankedelman
manager: ScottCa
ms.service: site-reliability-engineering
ms.topic: article
ms.date: 09/14/2020
ms.author: dnb
ms.openlocfilehash: 3d0698f2780a4ccc41eedbde70fddea1766c5f21
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90090324"
---
# <a name="frequently-asked-questions-whats-the-relationship-between-sre-and-devops"></a>常見問題： SRE 與 DevOps 之間的關係為何？

網站可靠性工程與 DevOps 之間的關係有一組常見的問題，包括「兩者有何不同？ 兩者有何不同？ 我們可以在我們的組織中嗎？」。 本文嘗試分享 SRE 和 DevOps 團體提供的一些解答，讓我們更深入瞭解此關聯性。

## <a name="how-are-they-the-same"></a>它們有何不同？

SRE 和 DevOps 都是為了回應所包含的挑戰而建立和開發的新式作業實務：

- 生產環境和開發程式的不斷成長複雜性
- 對於那些環境持續運作而持續增加的商業相依性
- 無法根據這些環境的大小來線性擴縮工作力
- 需要更快速地移動，同時仍保有營運穩定性

這兩種作業實務都重視處理這些挑戰的重要主題，例如監視/可檢視性、自動化、檔和共同作業軟體發展工具。

在 SRE 與 DevOps 之間的工具和工作區中，有相當大的重迭。 當 _網站可靠性活頁簿_ 放入它時，「SRE 相信與 DevOps 相同，但有些許不同的原因。」

## <a name="three-different-ways-to-compare-the-two-operations-practices"></a>有三種不同的方式可比較這兩個作業實務

SRE 與 DevOps 之間的相似之處很清楚。 真正有趣的是，兩者有何不同或彼此分離。 在這裡，我們提供三種方式來思考其關聯性，使其成為這個問題的一些微妙之處。 您可能不同意這些答案，但每個都提供一個良好的起點供討論之用。

### <a name="class-sre-implements-interface-devops"></a>"class SRE implements interface DevOps"

我們的[資源書籍清單](../resources/books.md)中_所述的網站可靠性活頁簿_ () 討論 SRE 和 DevOps 的第一章。 該章節使用 "class SRE implements interface DevOps" 一詞作為其子標題。 這是為了建議 (使用以開發人員為目標的片語，) 可將 SRE 視為 DevOps 原理的特定執行。 如章節所述，「DevOps 是如何在詳細層級上執行作業的無訊息方式」，而 SRE 在其實務上會有更多禁止使用。 因此，這兩個關聯性的問題可能有一項可能會被視為 DevOps 的許多可能實施方式之一。

### <a name="sre-is-to-reliability-as-devops-is-to-delivery"></a>SRE 的可靠性是為了提供 DevOps

這項比較有點 muddied，因為 SRE 和 DevOps 有多個定義，但仍有其效用。 這一開始會有一個問題：「如果您必須將每個作業實務提煉成一個或兩個字，以反映其核心考慮，該怎麼辦？」

如果我們使用來自「 [網站可靠性工程中樞](../index.yml)」的 SRE 定義：

> 網站可靠性工程是一項工程準則，其致力於協助組織持續達成適當的系統、服務和產品可靠性等級。

然後，您可以輕鬆地說 SRE 的單字是「可靠性」。 在名稱的中間，也可以為此宣告提供一些絕佳的辨識項。

如果我們使用 [Azure DevOps 資源中心](https://docs.microsoft.com/azure/devops/learn/)的此 DevOps 定義：

> DevOps 是人員、程序與產品的結合，可為終端使用者提供價值的持續傳遞。

然後，類似的 DevOps 公司可能是「傳遞」。

因此，「SRE 的可靠性與 DevOps 是傳遞的可靠性」。

### <a name="direction-of-attention"></a>關注方向

從 Thomas Limoncelli 到我們的[資源書籍清單](../resources/books.md)中所述的「_搜尋 SRE_ 」書籍，此答案會以引號括住或稍微摘錄。 他注意的是，DevOps 工程師大多專注于軟體發展生命週期管線，但偶爾會有生產作業的職責，而 Sre 專注于生產作業，但不定期 SDLC 管線責任。

但更重要的是，他也會從一側的軟體發展程式開始繪製一個圖表，而生產作業也會在另一個階段工作。 這兩個連接的一般管線是由開發人員所建立的程式碼、透過所需的測試和階段 shepherd，然後將該程式碼移至生產環境。

Limoncelli 請注意，DevOps 工程師是從開發環境開始，並自動執行生產環境的步驟。 完成後，他們會回到將瓶頸優化。

另一方面，Sre 會專注于生產作業，並將焦點移到管線中，以改善最終結果 (基本上是以相反的方向) 。

這與 SRE 和 DevOps 焦點的方向有這項差異，可協助您區分。

## <a name="coexistence-in-the-same-organization"></a>在相同組織中共存

我們想要解決的最後一個問題是：「您可以同時在相同的組織中同時有 SRE 和 DevOps 嗎？」

這個問題的答案是 emphatic "yes！"。

我們希望先前的答案能讓您瞭解這兩個作業實務如何重迭，而在不重迭時，如何以焦點來互補。 具有既定 DevOps 實務的組織可以在小規模 (上實驗 SRE 實務，例如，嘗試 Sli 和 Slo) ，而不需要認可以建立 SRE 位置或小組。 這是相當常見的 SRE 採用模式。

## <a name="next-steps"></a>後續步驟

有興趣深入瞭解網站可靠性工程或 DevOps 嗎？ 請查看我們的 [網站可靠性工程中樞](../index.yml) ，並 [Azure DevOps 資源中心](https://docs.microsoft.com/azure/devops/learn/)。
