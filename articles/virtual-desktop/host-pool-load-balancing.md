---
title: Windows 虛擬桌面主機集區負載平衡-Azure
description: 瞭解適用于 Windows 虛擬桌面環境的主機集區負載平衡方法。
author: Heidilohr
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2b977d64dea1cef3b8142758e57d91e92e5bcc02
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2020
ms.locfileid: "89461114"
---
# <a name="host-pool-load-balancing-methods"></a>主機集區負載平衡方法

>[!IMPORTANT]
>此內容適用於具有 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面。 如果您使用不含 Azure Resource Manager 物件的 Windows 虛擬桌面 (傳統版)，請參閱[這篇文章](./virtual-desktop-fall-2019/host-pool-load-balancing-2019.md)。

Windows 虛擬桌面支援兩種負載平衡方法。 每個方法會決定當工作階段主機連線到主機集區中的資源時，會裝載使用者的會話。

以下是 Windows 虛擬桌面提供的負載平衡方法：

- 廣度優先的負載平衡可讓您在主機集區的工作階段主機之間平均分配使用者會話。
- 深度優先的負載平衡可讓您將工作階段主機與主機集區中的使用者會話飽和。 第一個會話達到其會話限制閾值之後，負載平衡器會將任何新的使用者連線導向至主機集區中的下一個工作階段主機，直到達到其限制為止等等。

每個主機集區只能設定一個特定的負載平衡類型。 不過，無論是哪一個主機集區，這兩種負載平衡方法都會共用下列行為：

- 如果使用者在主機集區中已經有會話，且正在重新連線到該會話，負載平衡器就會使用現有的會話成功將它們重新導向至工作階段主機。 即使該工作階段主機的 AllowNewConnections 屬性設定為 False，還是會套用此行為。
- 如果使用者在主機集區中還沒有會話，負載平衡器就不會將其 AllowNewConnections 屬性設定為 False 的工作階段主機視為負載平衡。

## <a name="breadth-first-load-balancing-method"></a>廣度優先的負載平衡方法

廣度優先的負載平衡方法可讓您散發使用者連接，以針對此案例進行優化。 如果組織想要為連線到其集區虛擬桌面環境的使用者提供最佳體驗，這種方法就很適合。

廣度優先的方法會先查詢允許新連接的工作階段主機。 然後，方法會從工作階段主機集合的最少數目的一半，隨機選取工作階段主機。 例如，如果有九部電腦具有11、12、13、14、15、16、17、18和19個會話，您所建立的新會話將不會自動移至第一部電腦。 相反地，它可以移至會話數目最少的前五部機器 (11、12、13、14、15) 。

## <a name="depth-first-load-balancing-method"></a>深度優先的負載平衡方法

深度優先的負載平衡方法可讓您一次讓一個工作階段主機飽和，以針對此案例進行優化。 這種方法適用于符合成本的組織，而這些組織想要更精確地控制它們為主機集區配置的虛擬機器數目。

深度優先方法會先查詢允許新連線的工作階段主機，而不會超出其最大會話限制。 然後，方法會選取會話數目最高的工作階段主機。 如果有系結，方法會選取查詢中的第一個工作階段主機。