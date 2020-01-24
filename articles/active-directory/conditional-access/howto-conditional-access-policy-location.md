---
title: 條件式存取-依位置封鎖存取-Azure Active Directory
description: 建立自訂的條件式存取原則，以依 IP 位置封鎖對資源的存取
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 204026825a5132464e1adda611227db2ac7cf91f
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76543982"
---
# <a name="conditional-access-block-access-by-location"></a>條件式存取：依位置封鎖存取

透過條件式存取中的位置條件，您可以根據使用者的網路位置來控制雲端應用程式的存取權。 「位置」條件通常用來封鎖從組織得知流量不應來自的國家/地區的存取。

## <a name="define-locations"></a>定義位置

1. 以全域管理員、安全性系統管理員或條件式存取系統管理員的身分登入**Azure 入口網站**。
1. 流覽至**Azure Active Directory** > **安全性** > [**條件式存取**] > **名為 [位置**]。
1. 選擇 [**新增位置**]。
1. 為您的位置命名。
1. 如果您知道組成該位置或**國家/地區**的特定外部可存取 IPv4 位址範圍，請選擇 [ **IP 範圍**]。
   1. 提供**IP 範圍**，或選取您要指定之位置的**國家/地區**。
      * 如果您選擇 [國家/地區]，可以選擇是否要包含未知的區域。
1. 選擇 [儲存]

如需有關條件式存取中位置條件的詳細資訊，請參閱 [條件式存取中的位置條件 Azure Active Directory](location-condition.md)

## <a name="create-a-conditional-access-policy"></a>建立條件式存取原則

1. 以全域管理員、安全性系統管理員或條件式存取系統管理員的身分登入**Azure 入口網站**。
1. 流覽至**Azure Active Directory** > **安全性** > **條件式存取**。
1. 選取 [新增原則]。
1. 提供您的原則名稱。 我們建議組織針對其原則的名稱建立有意義的標準。
1. 在 [**指派**] 底下，選取 [**使用者和群組**]
   1. 在 [**包含**] 底下，選取 [**所有使用者**]。
   1. 選取 [完成] 。
1. 在 [**雲端應用程式] 或 [動作** > **包括**] 底下，選取 [**所有雲端應用程式**] 並選取 [**完成**]
1. 在 [**條件**] > **位置**] 底下。
   1. 將 **[** 設定] 設為 **[是]**
   1. **包含**選取選取的**位置**
   1. 選取您為組織建立的已封鎖位置。
   1. 按一下 [**選取** > 完成** > 完成**]。 
1. 在 [**存取控制**] 底下 > [**封鎖**]，然後選取 [**選取**]。
1. 確認您的設定，並將 [**啟用原則**] 設為 [**開啟**]。
1. 選取 [**建立**] 以建立以啟用您的原則。

## <a name="next-steps"></a>後續步驟

[條件式存取的一般原則](concept-conditional-access-policy-common.md)

[使用條件式存取僅限報告模式判斷影響](howto-conditional-access-report-only.md)

[使用條件式存取 What If 工具模擬登入行為](troubleshoot-conditional-access-what-if.md)
