---
title: 建立自訂端點 | Microsoft Docs
description: 在此文章中，了解如何設定自訂端點，以使用您的 Internet Analyzer 資源進行測量。
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: quickstart
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 6a781e767393f35a1ec22f195ba787e3018d8d1e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "76713093"
---
# <a name="measure-custom-endpoints-to-evaluate-in-your-internet-analyzer-tests"></a>測量要在 Internet Analyzer 測試中評估的自訂端點 

此文章示範如何設定自訂端點，以便在 Internet Analyzer 測試中進行測量。 自訂端點有助於評估內部部署工作負載、在其他雲端提供者上執行的工作負載，以及自訂的 Azure 設定。  如果兩個自訂端點中有一個是 Azure 資源，則可在一個測試中比較這兩者。 如需 Internet Analyzer 的詳細資訊，請參閱[概觀](internet-analyzer-overview.md)。 

> [!IMPORTANT]
> 此公開預覽版是在沒有服務等級協定的情況下提供，不得用於生產工作負載。 可能不支援特定功能、可能已經限制功能，或者可能無法在所有 Azure 位置提供使用。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

## <a name="before-you-begin"></a>開始之前

確定您會設定 Internet Analyzer 資源，然後選取 [自訂端點] 選項。 Internet Analyzer 假設您的自訂端點可供網際網路存取。 如需詳細資訊，請參閱[建立 Internet Analyzer 資源](internet-analyzer-create-test-portal.md)。


## <a name="create-custom-endpoint"></a>建立自訂端點

1. 在[這裡](https://fpc.msedge.net/apc/trans.gif)下載透明的單一像素測試影像。 這個單一像素影像就是用戶端 JavaScript 將擷取來測量效能的資產。
2. 在您的自訂 Web 應用程式中，將此測試影像部署於可公開存取的路徑中。 此路徑應透過 HTTPS 才能運作。 
3. 在您的測試建立期間，將完整的自訂端點 URL (例如 https://contoso.com/test/trans.gif) 複製到自訂端點欄位中。

## <a name="next-steps"></a>後續步驟

閱讀 [Internet Analyzer 常見問題集](internet-analyzer-faq.md)

