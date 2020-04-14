---
title: 使用規則引擎在標準 Azure CDN 中強制實施 HTTPS |微軟文件
description: 使用 Microsoft 標準 Azure 內容傳遞網路 (Azure CDN) 的規則引擎自訂 Azure CDN 處理 HTTP 請求的方式,包括阻止某些類型的內容的傳遞、定義緩存策略和修改 HTTP 標頭。 在本文中,瞭解如何創建規則以將使用者重定向到 HTTPS。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: 91a442573139bf4fdd09978290bf2380c8bcb97e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259915"
---
# <a name="set-up-the-standard-rules-engine-for-azure-cdn"></a>為 Azure CDN 設定標準規則引擎

本文介紹如何為 Azure 內容傳遞網路 (Azure CDN) 設定和使用標準規則引擎。

## <a name="standard-rules-engine"></a>標準規則引擎

可以使用 Azure CDN 的標準規則引擎自訂 HTTP 請求的處理方式。 例如,可以使用規則引擎強制內容傳遞以使用特定協議、定義緩存策略或修改 HTTP 標頭。 本文演示如何創建自動將使用者重定向到 HTTPS 的規則。 

> [!NOTE]
> 本文中描述的規則引擎僅適用於 Microsoft 的標準 Azure CDN。 

## <a name="redirect-users-to-https"></a>將使用者重定到 HTTPS

1. 在 Microsoft 配置檔中,轉到 Azure 內容傳遞網路。

1. 在**CDN 設定檔**頁上,選擇要為其建立規則的終結點。
  
1. 選擇 **「規則引擎」** 選項卡。
   
    「**規則引擎」** 窗格將開啟並顯示可用的全域規則的清單。 
   
    [![Azure CDN 新規則頁](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > 列出多個規則的順序會影響規則的處理方式。 規則中指定的操作可能被後續規則覆蓋。
   >

1. 選擇 **「新增規則**」並輸入規則名稱。 規則名稱必須以字母開頭,並且只能包含數位和字母。

1. 要識別規則應用於的請求類型,請建立符合條件:
    1. 選擇 **'新增條件**',然後選擇**請求協定**匹配條件。
    1. 針對 [運算子]**** 選取 [等於]****。
    1. 對**值**,選擇**HTTP**。
   
   [![Azure CDN 規則符合條件](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > 您可以從 **「新增條件**」下拉清單中的多個匹配條件中選擇。 有關符合條件的詳細清單,請參閱[標準規則引擎中的符合條件](cdn-standard-rules-engine-match-conditions.md)。
   
1. 選擇要套用於符合符合條件的要求的操作:
   1. 選擇 **'新增操作**',然後選擇**網址重定向**。
   1. 對**類型**,選擇 **「找到 」(302)**。
   1. 在 [通訊協定]**** 中，選取 **HTTPS**。
   1. 將所有其他欄位留空以使用傳入值。
   
   [![Azure CDN 規則操作](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > 您可以從 **「新增操作**」下拉清單中的多個操作中選擇。 有關操作的詳細清單,請參閱[標準規則引擎中的操作](cdn-standard-rules-engine-actions.md)。

6. 選擇 **「儲存」** 以儲存新規則。 該規則現在可供使用。
   
   > [!IMPORTANT]
   > 規則更改可能需要長達 15 分鐘才能通過 Azure CDN 進行傳播。
   >
   

## <a name="next-steps"></a>後續步驟

- [Azure CDN 概觀](cdn-overview.md)
- [標準規則引擎參考](cdn-standard-rules-engine-reference.md)
- [標準規則引擎中的符合條件](cdn-standard-rules-engine-match-conditions.md)
- [標準規則引擎中的操作](cdn-standard-rules-engine-actions.md)
