---
title: Azure 監視器活頁簿存取控制
description: 使用以角色為基礎的存取控制，以預建和自訂參數化活頁簿簡化複雜報表
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 9c0b5dd72cf82649e7f659d23606d23aea62ac82
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91932544"
---
# <a name="access-control"></a>存取控制

活頁簿中的存取控制指的是兩件事：

* 讀取活頁簿中資料所需的存取權。 這項存取是由活頁簿中所使用資源的標準 [Azure 角色](../../role-based-access-control/overview.md) 所控制。 活頁簿不會指定或設定這些資源的存取權。 使用者通常會使用這些資源上的「 [監視讀者](../../role-based-access-control/built-in-roles.md#monitoring-reader) 」角色來取得這些資源的存取權。

* 儲存活頁簿所需的存取權

    - 儲存私人活頁 `("My")` 簿不需要額外的許可權。 所有使用者都可以儲存私用活頁簿，而且只有它們才能看到這些活頁簿。
    - 儲存共用活頁簿需要資源群組中的寫入權限，才能儲存活頁簿。 這些許可權通常是由「 [監視參與者](../../role-based-access-control/built-in-roles.md#monitoring-contributor) 」角色所指定，但是也可以透過「活頁 *簿參與者* 」角色來設定。
    
## <a name="standard-roles-with-workbook-related-privileges"></a>具有活頁簿相關許可權的標準角色

[監視讀取器](../../role-based-access-control/built-in-roles.md#monitoring-reader) 包含/read 監視 (工具所使用的標準許可權，包括從資源讀取資料) 的活頁簿。

[監視參與者](../../role-based-access-control/built-in-roles.md#monitoring-contributor) 包含 `/write` 用來儲存專案的各種監視工具所使用的一般許可權 (包括 `workbooks/write`) 儲存共用活頁簿的許可權。
「活頁簿參與者」會將「活頁簿/寫入」許可權加入至物件，以儲存共用的活頁簿。
使用者不需要特殊許可權，就能儲存只能看見的私人活頁簿。

針對自訂角色型存取控制：

加入 `microsoft.insights/workbooks/write` 以儲存共用的活頁簿。 如需詳細資訊，請參閱活頁 [簿參與者](../../role-based-access-control/built-in-roles.md#monitoring-contributor) 角色。

## <a name="next-steps"></a>後續步驟

* [開始深入](workbooks-visualizations.md) 瞭解活頁簿許多豐富的視覺效果選項。
