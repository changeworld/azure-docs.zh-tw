---
title: Azure 監視器活頁簿存取控制
description: 使用以角色為基礎的存取控制，透過預先建立和自訂的參數化活頁簿簡化複雜
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 20116ab105e4eb12875ba3cb279fb261eb5c70e4
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658416"
---
# <a name="access-control"></a>存取控制

活頁簿中的存取控制指的是兩件事：

* 讀取活頁簿中的資料時所需的存取權。 此存取權是由活頁簿中所使用資源的標準[Azure 角色](https://docs.microsoft.com/azure/role-based-access-control/overview)所控制。 活頁簿不會指定或設定對這些資源的存取權。 使用者通常會使用這些資源上的 [[監視讀取](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader)者] 角色，取得這些資源的存取權。

* 儲存活頁簿所需的存取權

    - 儲存私用 `("My")` 活頁簿不需要額外的許可權。 所有使用者都可以儲存私人活頁簿，而且只有他們可以看到這些活頁簿。
    - 儲存共用活頁簿需要資源群組中的寫入權限，才能儲存活頁簿。 這些許可權通常是由「[監視參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)」角色所指定，但也可以透過「活頁*簿參與者*」角色來設定。
    
## <a name="standard-roles-with-workbook-related-privileges"></a>具有活頁簿相關許可權的標準角色

[監視讀取器](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader)包含標準的/read 許可權，可由監視工具（包括活頁簿）用來讀取資源的資料。

[監視參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)包括用來儲存專案之各種監視工具所使用的一般 `/write` 許可權（包括儲存共用活頁簿的 `workbooks/write` 許可權）。
「活頁簿參與者」會將「活頁簿/寫入」許可權新增至物件，以儲存共用的活頁簿。
使用者不需要特殊許可權，就能儲存只能看到的私人活頁簿。

針對自訂角色型存取控制：

新增 `microsoft.insights/workbooks/write` 以儲存共用的活頁簿。 如需詳細資訊，請參閱活頁[簿參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)角色。

## <a name="next-steps"></a>後續步驟

* [開始深入](workbooks-visualizations.md)瞭解活頁簿許多豐富的視覺效果選項。
