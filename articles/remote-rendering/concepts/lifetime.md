---
title: 物件和資源存留期
description: 說明不同類型的存留期管理
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: conceptual
ms.openlocfilehash: d031ff4a6ee86da2843f0f18ac428c50f7cfc121
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80681866"
---
# <a name="object-and-resource-lifetime"></a>物件和資源存留期

Azure 遠端轉譯區分兩種類型： **物件** 和 **資源**。

## <a name="object-lifetime"></a>物件存留期

*物件* 被視為使用者可以自行建立、修改和終結的專案。 物件可以自由複製，而且每個實例都可以隨著時間改變。 因此， [實體](entities.md) 和 [元件](components.md) 是物件。

物件的存留期完全在使用者控制項之下。 不過，它與用戶端表示的存留期無關。 和等 `Entity` 類別 `Component` 都具有 `Destroy` 必須呼叫的函式，才能解除配置遠端轉譯主機上的物件。 此外， `Entity.Destroy()` 將會摧毀實體、其子系和該階層中的所有元件。

## <a name="resource-lifetime"></a>資源存留期

*資源* 是存留期完全由遠端轉譯主機管理的事物。 資源是在內部計數的參考。 當不再有任何人參考它們時，就會將它們解除配置。

大部分資源只能間接建立，通常是從檔案載入。 當相同的檔案載入多次時，Azure 遠端轉譯會傳回相同的參考，而不會再次載入資料。

許多資源都是不變的，例如 [網格](meshes.md) 和 [紋理](textures.md)。 不過，某些資源是可變動的，例如 [材質](materials.md)。 因為資源通常是共用的，所以修改資源可能會影響多個物件。 例如，變更材質的色彩將會變更使用網格的所有物件色彩，進而參考該材質。

### <a name="built-in-resources"></a>內建資源

Azure 遠端轉譯包含一些內建的資源，可透過在呼叫期間于其各自的識別碼前面加上載入 `builtin://` `AzureSession.Actions.LoadXYZAsync()` 。 可用的內建資源會列在每個個別功能的檔中。 例如， [天空章節](../overview/features/sky.md) 會列出內建的天空紋理。

## <a name="general-lifetime"></a>一般存留期

所有物件和資源的存留期都會系結至連接。 中斷連線時，所有專案都會被捨棄。 重新連接到相同的會話時，場景圖形將會是空的，而且會清除所有資源。

在實務上，在中斷連線之後，將相同的資源載入會話中，通常會比第一次快很多。 這是因為大部分的資源都必須在第一次從 Azure 儲存體下載，而這不是第二次所需的時間，因而節省相當長的時間。

## <a name="next-steps"></a>後續步驟

* [實體](entities.md)
* [Components](components.md)
* [模型](models.md)
