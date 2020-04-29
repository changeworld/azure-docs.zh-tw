---
title: 使用規則引擎來強制執行標準 Azure CDN 中的 HTTPS |Microsoft Docs
description: 使用適用于 Microsoft 標準 Azure 內容傳遞網路（Azure CDN）的規則引擎，自訂 Azure CDN 處理 HTTP 要求的方式，包括封鎖特定類型內容的傳遞、定義快取原則，以及修改 HTTP 標頭。 在本文中，您將瞭解如何建立規則，以將使用者重新導向至 HTTPS。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: 91a442573139bf4fdd09978290bf2380c8bcb97e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81259915"
---
# <a name="set-up-the-standard-rules-engine-for-azure-cdn"></a>設定適用于 Azure CDN 的標準規則引擎

本文說明如何設定及使用適用于 Azure 內容傳遞網路（Azure CDN）的標準規則引擎。

## <a name="standard-rules-engine"></a>標準規則引擎

您可以使用適用于 Azure CDN 的標準規則引擎來自訂 HTTP 要求的處理方式。 例如，您可以使用規則引擎來強制傳遞內容，以使用特定的通訊協定、定義快取原則，或修改 HTTP 標頭。 本文示範如何建立自動將使用者重新導向至 HTTPS 的規則。 

> [!NOTE]
> 本文所述的規則引擎僅適用于來自 Microsoft 的標準 Azure CDN。 

## <a name="redirect-users-to-https"></a>將使用者重新導向至 HTTPS

1. 在您的 Microsoft 設定檔中，移至 [Azure 內容傳遞網路]。

1. 在 [ **CDN 設定檔**] 頁面上，選取您想要建立規則的端點。
  
1. 選取 [**規則引擎**] 索引標籤。
   
    [**規則引擎**] 窗格隨即開啟，並顯示可用的全域規則清單。 
   
    [![Azure CDN 新規則頁面](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > 列出多個規則的順序會影響規則的處理方式。 後續規則可能會覆寫規則中指定的動作。
   >

1. 選取 [**新增規則**]，然後輸入規則名稱。 規則名稱必須以字母開頭，而且只能包含數位和字母。

1. 若要識別規則適用的要求類型，請建立比對條件：
    1. 選取 [**新增條件**]，然後選取 [**要求通訊協定**比對條件]。
    1. 針對 [運算子]**** 選取 [等於]****。
    1. 針對 [**值**]，選取 [ **HTTP**]。
   
   [![Azure CDN 規則比對條件](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > 您可以從 [**新增條件**] 下拉式清單中選取多個比對條件。 如需相符條件的詳細清單，請參閱[標準規則引擎中](cdn-standard-rules-engine-match-conditions.md)的比對條件。
   
1. 選取要套用至符合比對條件之要求的動作：
   1. 選取 [**新增動作**]，然後選取 [ **URL 重新導向**]。
   1. 針對 [**類型**]，選取 **[找到（302）**]。
   1. 在 [通訊協定]**** 中，選取 **HTTPS**。
   1. 將所有其他欄位保留為空白，以使用傳入的值。
   
   [![Azure CDN 規則動作](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > 您可以在 [**新增動作**] 下拉式清單中選取多個動作。 如需動作的詳細清單，請參閱[標準規則引擎中的動作](cdn-standard-rules-engine-actions.md)。

6. 選取 [**儲存**] 以儲存新的規則。 規則現在可供使用。
   
   > [!IMPORTANT]
   > 規則變更最多可能需要15分鐘的時間，才能透過 Azure CDN 傳播。
   >
   

## <a name="next-steps"></a>後續步驟

- [Azure CDN 概觀](cdn-overview.md)
- [標準規則引擎參考](cdn-standard-rules-engine-reference.md)
- [標準規則引擎中的比對條件](cdn-standard-rules-engine-match-conditions.md)
- [標準規則引擎中的動作](cdn-standard-rules-engine-actions.md)
