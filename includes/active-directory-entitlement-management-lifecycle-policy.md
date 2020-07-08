---
title: 包含檔案
description: 包含檔案
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: e971af5d415ea788f97ab11b58541e629c7eb0ed
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76694295"
---
## <a name="lifecycle"></a>生命週期

在 [**生命週期**] 索引標籤上，您可以指定使用者對存取套件的指派何時到期。 您也可以指定使用者是否可以擴充其指派。

1. 在 [**到期**] 區段中，將 [**存取套件指派**]**設定為 [到期日]**、[**天數**] 或 [**永不**]。

    針對 [**日期**]，選取未來的到期日。

    在 [**天數**] 中，指定介於0到3660天的數位。

    根據您的選擇，使用者對存取套件的指派會在特定日期到期、核准後的特定天數，或永不過期。

1. 按一下 [**顯示高階到期設定**] 以顯示其他設定。

    ![存取套件-生命週期到期設定](./media/active-directory-entitlement-management-lifecycle-policy/expiration.png)

1. 若要允許使用者擴充其指派，請將 [**允許使用者擴充存取**] 設定為 **[是]**。

    如果原則中允許延伸模組，則使用者會收到一封電子郵件14天，另外也會在1天后將其存取套件指派設定為到期，提示他們擴充指派。 如果使用者提交擴充存取權的要求，則此延伸模組的日期必須等於或早于指派的到期時間，如用來授與使用者存取封裝存取權限的原則中所定義。 例如，如果原則指出指派已設定為在6月30日到期，則使用者可以要求的擴充功能上限為6月30日。

    如果使用者的存取權已延長，他們將無法在指定的延伸日期（建立原則之使用者的時區日期）之後要求存取套件。

1. 若要要求核准以授與延伸模組，請將 [**需要核准以授與延伸**模組為 **]**。

    將會使用 [**要求**] 索引標籤上所指定的相同核准設定。

1. 按 **[下一步] 或 [** **更新**]。
