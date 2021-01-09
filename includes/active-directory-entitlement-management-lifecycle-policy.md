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
ms.openlocfilehash: db13884e3deed8e990d21d82e215a1e837371275
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052753"
---
## <a name="lifecycle"></a>生命週期

在 [ **生命週期** ] 索引標籤上，您可以指定使用者對存取套件的指派何時到期。 您也可以指定使用者是否可以擴充其指派。

1. 在 [ **到期** ] 區段中，將 [ **存取套件指派** ] **設為 [到期日]**、[ **天數**] 或 [ **永不**]。

    針對 [ **日期**]，選取未來的到期日。

    在 [ **天數**] 中，指定介於0和3660天之間的數位。

    根據您的選擇，使用者對存取套件的指派會在特定日期到期、在核准後的特定天數內到期，或永不到期。

1. 按一下 [ **顯示 advanced 逾期設定** ] 以顯示其他設定。

    ![存取套件-生命週期到期設定](./media/active-directory-entitlement-management-lifecycle-policy/expiration.png)

1. 若要允許使用者擴充其指派，請設定 [ **允許使用者將存取權延伸** 至 **[是]**。

    如果原則中允許延伸模組，使用者將會收到一封電子郵件，也會在其存取套件指派設定為過期前一天，提示他們延長指派。 使用者在要求延伸時，仍然必須在原則的範圍內。 此外，如果原則具有指派的明確結束日期，而且使用者提交要求以延伸存取權，則要求中的延伸模組日期必須在指派到期時或之前（如同用來授與使用者存取套件存取權的原則中所定義）。 例如，如果原則指出將指派設定為在6月30日過期，則使用者可以要求的最大擴充為6月30日。

    如果使用者的存取權已延長，則在建立原則) 的使用者時區中，指定的延伸模組日期 (日期之後，他們將無法再要求存取套件。

1. 若要要求核准以授與擴充功能，請設定 [ **需要核准] 以將延伸模組授** 與 **[是]**。

    將會使用在 [ **要求** ] 索引標籤上指定的相同核准設定。

1. 按一下 **[下一步] 或 [** **更新**]。
