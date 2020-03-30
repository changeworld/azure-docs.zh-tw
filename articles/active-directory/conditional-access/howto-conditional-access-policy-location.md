---
title: 條件訪問 - 按位置阻止訪問 - Azure 活動目錄
description: 創建自訂條件訪問策略以阻止按 IP 位置訪問資源
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34b29ceadaaf85e69d1214039fa1b563ed21a77d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295193"
---
# <a name="conditional-access-block-access-by-location"></a>條件訪問：按位置阻止訪問

使用條件訪問中的位置條件，您可以根據使用者的網路位置控制對雲應用的訪問。 位置條件通常用於阻止從您的組織知道流量不應來自的國家/地區進行訪問。

## <a name="define-locations"></a>定義位置

1. 以全域管理員、安全管理員或條件訪問管理員的身份登錄到**Azure 門戶**。
1. 流覽到**Azure 活動目錄** > **安全** > **條件訪問** > **命名位置**。
1. 選擇 **"新建位置**"。
1. 給您所在的位置起個名字。
1. 如果您知道構成該位置的特定外部可訪問 IPv4 位址範圍或**國家/地區**，請選擇**IP 範圍**。
   1. 提供**IP 範圍**或選擇您指定的位置**的國家/地區**。
      * 如果選擇"國家/地區"，您可以選擇包括未知區域。
1. 選擇 **"保存"**

有關條件訪問中的位置條件的詳細資訊，請參閱["Azure 活動目錄條件訪問中的位置條件是什麼](location-condition.md)"

## <a name="create-a-conditional-access-policy"></a>建立條件式存取原則

1. 以全域管理員、安全管理員或條件訪問管理員的身份登錄到**Azure 門戶**。
1. 流覽到**Azure 活動目錄** > **安全** > **條件訪問**。
1. 選取 [新增原則]****。
1. 為您的策略指定一個名稱。 我們建議組織為其策略的名稱創建有意義的標準。
1. 在 **"分配"** 下，選擇 **"使用者"和"組**"
   1. 在 **"包括**"下，選擇**所有使用者**。
   1. 選擇 **"完成**"。
1. 在 **"雲應用"或"操作** > **包括**"下，選擇"**所有雲應用****"，然後選擇"完成**"。
1. 在**條件** > **位置**下 。
   1. 將 **"配置"** 設置為 **"是"**
   1. **包括**選擇**選定位置**
   1. 選擇為您的組織創建的阻止位置。
   1. 按一下 **"選擇** > **完成** > **完成**"。
1. 在**條件** > **用戶端應用（預覽）** 下，將 **"配置配置為** **"，** 然後選擇 **"完成**"。
1. 在 **"訪問控制項** > **塊**"下，並**選擇"選擇**"。
1. 確認設置並將 **"啟用策略"** 設置為 **"打開**"。
1. 選擇 **"創建**"以啟用策略。

## <a name="next-steps"></a>後續步驟

[條件訪問通用策略](concept-conditional-access-policy-common.md)

[使用僅條件訪問報告模式確定影響](howto-conditional-access-report-only.md)

[使用條件訪問"如果"工具類比登錄行為](troubleshoot-conditional-access-what-if.md)
