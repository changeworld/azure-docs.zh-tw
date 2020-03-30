---
title: Windows 虛擬桌面主機池負載平衡 - Azure
description: 適用于 Windows 虛擬桌面環境的主機池負載平衡方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 193821ed0df09b87f19e45a82ca42026405a0dc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127865"
---
# <a name="host-pool-load-balancing-methods"></a>主機集區負載平衡方法

Windows 虛擬桌面支援兩種負載平衡方法。 每種方法確定在使用者連接到主機池中的資源時，哪個工作階段主機將承載使用者的會話。

以下負載平衡方法在 Windows 虛擬桌面中可用：

- 通過"廣度優先"負載平衡，您可以在主機池中的工作階段主機之間均勻分配使用者會話。
- 深度優先負載平衡允許您在主機池中使工作階段主機與使用者會話飽和。 第一個會話達到其會話限制閾值後，負載等化器將引導任何新使用者連接到主機池中的下一個工作階段主機，直到其達到其限制，等等。

每個主機池只能配置特定于它的一種類型的負載平衡。 但是，無論它們位於哪個主機池中，兩種負載平衡方法都具有以下行為：

- 如果使用者已在主機池中具有會話並重新連接到該會話，負載等化器將成功地將其現有會話重定向到工作階段主機。 即使工作階段主機的"允許新連接"屬性設置為 False，此行為也會適用。
- 如果使用者在主機池中尚未具有會話，則負載等化器不會考慮其 AllowNewConnections 屬性在負載平衡期間設置為 False 的工作階段主機。

## <a name="breadth-first-load-balancing-method"></a>廣度優先負載平衡方法

廣度優先負載平衡方法允許您分發使用者連接以針對此方案進行優化。 此方法非常適合希望為連接到其池虛擬桌面環境的使用者提供最佳體驗的組織。

廣度優先方法首先查詢允許新連接的工作階段主機。 然後，該方法選擇會話數最少的工作階段主機。 如果存在連接，該方法將選取查詢中的第一個工作階段主機。

## <a name="depth-first-load-balancing-method"></a>深度優先負載平衡方法

深度優先負載平衡方法允許您一次使一個工作階段主機飽和，以便針對此方案進行優化。 此方法非常適合注重成本的組織，這些組織希望更精細地控制他們為主機池分配的虛擬機器數。

深度優先方法首先查詢允許新連接且未超過其最大會話限制的工作階段主機。 然後，該方法選擇會話數最多的工作階段主機。 如果有領帶，該方法將選取查詢中的第一個工作階段主機。