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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "76694295"
---
## <a name="lifecycle"></a>生命週期

在 **"生命週期"** 選項卡上，您可以指定使用者對訪問包的分配何時過期。 您還可以指定使用者是否可以擴展其分配。

1. 在 **"過期"** 部分中 **，"訪問包分配"將過期**為 **"打開日期**"、**天數**或 **"從不**"。

    對於 **"日期"，** 選擇將來的到期日期。

    對於**天數**，指定介於 0 和 3660 天之間的數位。

    根據您的選擇，使用者對訪問包的分配將在特定日期、批准後的某一天數或從未過期。

1. 按一下"**顯示高級過期設置**"以顯示其他設置。

    ![訪問包 - 生命週期過期設置](./media/active-directory-entitlement-management-lifecycle-policy/expiration.png)

1. 要允許使用者擴展其分配，請設置 **"允許使用者擴展**對**是**"的訪問。

    如果策略中允許擴展，使用者將收到一封電子郵件 14 天，也將收到一封電子郵件，其訪問包分配設置為過期，提示他們延長分配。 如果使用者提交擴展存取權限的請求，則擴展日期必須位於或之前分配過期，如用於授予使用者訪問包的策略中定義的。 例如，如果策略指示分配設置為在 6 月 30 日過期，則使用者可以請求的最大擴展是 6 月 30 日。

    如果使用者的存取權限被擴展，他們將無法在指定的擴展日期（在創建策略的使用者的時區中設置的日期）之後請求訪問包。

1. 要需要批准才能授予擴展，需要**批准才能授予****"是**"。

    將使用"**請求"** 選項卡上指定的相同審批設置。

1. 按一下 **"下一步**"或 **"更新**"。
