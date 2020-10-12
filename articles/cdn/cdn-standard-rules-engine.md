---
title: 使用規則引擎在標準 Azure CDN 中強制執行 HTTPS | Microsoft Docs
description: 使用 Microsoft 標準 Azure 內容傳遞網路 (Azure CDN) 的規則引擎來自訂 Azure CDN 處理 HTTP 要求的方式，包括禁止傳遞特定類型的內容、定義快取原則，以及修改 HTTP 標頭。 在本文中，您將了解如何建立將使用者重新導向至 HTTPS 的規則。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: 1c596bf10d8afbce504b5abf04eacb877989521a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84887540"
---
# <a name="set-up-the-standard-rules-engine-for-azure-cdn"></a>設定 Azure CDN 的標準規則引擎

本文描述如何設定及使用 Azure 內容傳遞網路 (Azure CDN) 的標準規則引擎。

## <a name="standard-rules-engine"></a>標準規則引擎

您可使用 Azure CDN 的標準規則引擎自訂 HTTP 要求的處理方式。 例如，您可使用規則引擎強制內容傳遞使用特定的通訊協定、定義快取原則，或修改 HTTP 標頭。 本文會示範如何建立自動將使用者重新導向至 HTTPS 的規則。 

> [!NOTE]
> 本文所述規則引擎僅供 Microsoft 的標準 Azure CDN 使用。 

## <a name="redirect-users-to-https"></a>將使用者重新導向至 HTTPS

1. 在 Microsoft 設定檔中，移至 Azure 內容傳遞網路。

1. 在 [CDN 設定檔] 頁面中，選取想要建立規則的端點。
  
1. 選取 [規則引擎] 索引標籤。
   
    [規則引擎] 窗格隨即開啟，並顯示可用的全域規則清單。 
   
    [![Azure CDN 的 [新增規則] 頁面](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > 多項規則的排列順序會影響規則處理方式。 後續規則可能會覆寫某項規則中指定的動作。
   >

1. 選取 [新增規則]，然後輸入規則名稱。 規則名稱必須以字母開頭，且只能包含數字與字母。

1. 若要識別規則適用的要求類型，請建立比對條件：
    1. 選取 [新增條件]，然後選取 [要求通訊協定] 比對條件。
    1. 針對 [運算子] 請選取 [等於]。
    1. 針對 [值] 請選取 [HTTP]。
   
   [![Azure CDN 規則比對條件](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > 您可在 [新增條件] 下拉式清單的多個比對條件中選取。 如需比對條件的詳細清單，請參閱[標準規則引擎中的比對條件](cdn-standard-rules-engine-match-conditions.md)。
   
1. 選取要套用至符合比對條件要求的動作：
   1. 選取 [新增動作]，然後選取 [URL 重新導向]。
   1. 針對 [類型]，請選取 [找到 (302)]。
   1. 在 [通訊協定]中，選取 **HTTPS**。
   1. 將所有其他欄位保留為空白，以使用傳入的值。
   
   [![Azure CDN 規則動作](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > 您可在 [新增動作] 下拉式清單的多個比對條件中選取。 如需動作的詳細清單，請參閱[標準規則引擎中的動作](cdn-standard-rules-engine-actions.md)。

6. 選取 [儲存] 以儲存新規則。 規則現可供使用。
   
   > [!IMPORTANT]
   > 規則變更最多可能需要 15 分鐘才能傳遍 Azure CDN。
   >
   

## <a name="next-steps"></a>後續步驟

- [Azure CDN 概觀](cdn-overview.md)
- [標準規則引擎參考](cdn-standard-rules-engine-reference.md)
- [標準規則引擎中的比對條件](cdn-standard-rules-engine-match-conditions.md)
- [標準規則引擎中的動作](cdn-standard-rules-engine-actions.md)
