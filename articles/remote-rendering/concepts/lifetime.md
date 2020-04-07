---
title: 物件和資源存留期
description: 解釋不同類型的存留期管理
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: conceptual
ms.openlocfilehash: d031ff4a6ee86da2843f0f18ac428c50f7cfc121
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681866"
---
# <a name="object-and-resource-lifetime"></a>物件和資源存留期

Azure 遠端呈現區分了兩種**objects**類型 :物件**和資源**。

## <a name="object-lifetime"></a>物件存留期

*對象*被視為使用者可以自行決定創建、修改和銷毀的物件。 物件可以自由複製,每個實例可以隨時間而發生變異。 因此[,實體](entities.md)和[元件](components.md)是物件。

物件的存留期完全由使用者控制。 但是,它與用戶端表示的存留期無關。 類喜歡`Entity`,`Component`並且`Destroy`具有 必須調用的函數才能在遠端呈現主機上解分配物件。 此外,`Entity.Destroy()`將銷毀該層次結構中的實體、其子級和所有元件。

## <a name="resource-lifetime"></a>資源存留期

*資源*是其生存期完全由遠程呈現主機管理的東西。 資源在內部計數引用。 當沒人再引用他們時,他們會被交易。

大多數資源只能間接創建,通常是通過從檔載入這些資源。 多次載入同一檔時,Azure 遠端呈現將返回同一引用,不再載入資料。

許多資源是不變的,例如[等雜](meshes.md)種和[紋理](textures.md)。 但是,某些資源是可變的,例如[材料](materials.md)。 由於資源通常是共用的,因此修改資源可能會影響多個物件。 例如,更改材質的顏色將更改使用等件的所有物件的顏色,而這些物件又引用該材質。

### <a name="built-in-resources"></a>內建資源

Azure 遠端呈現包含一些內置資源,這些資源可以通過在`builtin://``AzureSession.Actions.LoadXYZAsync()`調用 期間預先等待其各自的標識符來載入這些資源。 每個功能的文檔中列出了可用的內置資源。 例如,[天空章節](../overview/features/sky.md)列出了內置的天空紋理。

## <a name="general-lifetime"></a>一般壽命

所有對象和資源的存留期綁定到連接。 斷開連接時,一切都會被丟棄。 重新連接到同一會話時,場景圖將為空,並且清除所有資源。

實際上,在斷開連接后將同一資源載入到會話中的速度通常比第一次快。 這是因為大多數資源必須在第一次從 Azure 存儲下載,這不需要第二次,從而節省了大量時間。

## <a name="next-steps"></a>後續步驟

* [實體](entities.md)
* [元件](components.md)
* [模型](models.md)
