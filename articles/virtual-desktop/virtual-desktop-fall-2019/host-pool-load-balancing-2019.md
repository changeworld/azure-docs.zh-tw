---
title: Windows 虛擬桌面 (傳統) 主機集區負載平衡-Azure
description: 適用于 Windows 虛擬桌面環境的主機集區負載平衡方法。
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 940863b983b00dbb3c4af590d75868665372f818
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88002311"
---
# <a name="host-pool-load-balancing-methods-in-windows-virtual-desktop-classic"></a>Windows 虛擬桌面 (傳統) 中的主機集區負載平衡方法

>[!IMPORTANT]
>此內容適用於不支援 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面 (傳統)。 如果您嘗試管理 Azure Resource Manager Windows 虛擬桌面物件，請參閱[這篇文章](../host-pool-load-balancing.md)。

Windows 虛擬桌面支援兩種負載平衡方法。 每個方法會決定當工作階段主機連線到主機集區中的資源時，會裝載使用者的會話。

以下是 Windows 虛擬桌面提供的負載平衡方法：

- 廣度優先的負載平衡可讓您在主機集區的工作階段主機之間平均分配使用者會話。
- 深度優先的負載平衡可讓您將工作階段主機與主機集區中的使用者會話飽和。 第一個會話達到其會話限制閾值之後，負載平衡器會將任何新的使用者連線導向至主機集區中的下一個工作階段主機，直到達到其限制為止等等。

每個主機集區只能設定一個特定的負載平衡類型。 不過，無論是哪一個主機集區，這兩種負載平衡方法都會共用下列行為：

- 如果使用者在主機集區中已經有會話，且正在重新連線到該會話，負載平衡器就會使用現有的會話成功將它們重新導向至工作階段主機。 即使該工作階段主機的 AllowNewConnections 屬性設定為 False，還是會套用此行為。
- 如果使用者在主機集區中還沒有會話，負載平衡器就不會將其 AllowNewConnections 屬性設定為 False 的工作階段主機視為負載平衡。

## <a name="breadth-first-load-balancing-method"></a>廣度優先的負載平衡方法

廣度優先的負載平衡方法可讓您散發使用者連接，以針對此案例進行優化。 如果組織想要為連線到其集區虛擬桌面環境的使用者提供最佳體驗，這種方法就很適合。

廣度優先的方法會先查詢允許新連接的工作階段主機。 然後，方法會選取會話數目最少的工作階段主機。 如果有系結，方法會選取查詢中的第一個工作階段主機。

## <a name="depth-first-load-balancing-method"></a>深度優先的負載平衡方法

深度優先的負載平衡方法可讓您一次讓一個工作階段主機飽和，以針對此案例進行優化。 這種方法適用于符合成本的組織，而這些組織想要更精確地控制它們為主機集區配置的虛擬機器數目。

深度優先方法會先查詢允許新連線的工作階段主機，而不會超出其最大會話限制。 然後，方法會選取會話數目最高的工作階段主機。 如果有系結，方法會選取查詢中的第一個工作階段主機。