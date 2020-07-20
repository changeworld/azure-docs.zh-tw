---
title: 階段空間
description: 說明階段空間設定和使用案例
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.openlocfilehash: b67294c503e513290b474e0059771a73ad526a6a
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86189393"
---
# <a name="stage-space"></a>階段空間

在提供前端追蹤資料（如 HoloLens 2）的裝置上執行 ARR 時，head 姿勢會同時傳送至使用者應用程式和伺服器。 在中定義 head 轉換的空間稱為「*階段空間*」。

為了對齊本機和遠端內容，會假設用戶端和伺服器上的階段空間和世界空間都相同。 如果使用者決定在相機頂端新增額外的轉換，則必須將它傳送至伺服器，並正確地對齊本機和遠端內容。

移動階段空間的一般原因包括：[世界鎖定工具](https://microsoft.github.io/MixedReality-WorldLockingTools-Unity/README.html)或其他真實世界錨定的技術，以及在 VR 中移動虛擬字元。

## <a name="stage-space-settings"></a>階段空間設定

> [!CAUTION]
> 實驗性：這項功能是實驗性，會隨著時間而改變。 因此，更新至較新的用戶端 SDK 版本可能需要額外的工作才能升級程式碼。 在變更階段空間原點時，目前的執行會中斷本機/遠端內容的對齊。
因此，它目前僅適用于全球鎖定用途，例如僅呈現一段時間內非常小變更的錨點。

若要通知伺服器已將額外的轉換套用至階段空間，其來源定義于位置，而在世界空間中的旋轉必須透過傳送。 此設定可透過*階段空間設定*來存取。

> [!IMPORTANT]
> 在[桌面模擬](../../concepts/graphics-bindings.md)中，相機的世界空間位置是由使用者應用程式提供。 在此情況下，必須略過設定階段空間的來源，因為它已經乘以相機轉換。

```cs
void ChangeStageSpace(AzureSession session)
{
    StageSpaceSettings settings = session.Actions.StageSpaceSettings;

    // Set position and rotation to the world-space transform of the stage space.
    settings.Position = new Double3(0, 0, 0);
    settings.Rotation = new Quaternion(0, 0, 0, 1);
}
```

```cpp
void ChangeStageSpace(ApiHandle<AzureSession> session)
{
    ApiHandle<StageSpaceSettings> settings = *session->Actions()->StageSpaceSettings();

    // Set position and rotation to the world-space transform of the stage space.
    settings->Position({0, 0, 0});
    settings->Rotation({0, 0, 0, 1});
}
```

## <a name="unity-stage-space-script"></a>Unity 階段空間腳本

Unity 整合提供名為**StageSpace**的腳本，可新增至相機的父 GameObject。 一出現之後，此腳本會負責設定階段空間的來源。

## <a name="next-steps"></a>後續步驟

* [圖形繫結](../../concepts/graphics-bindings.md)
* [延遲階段重新投影](late-stage-reprojection.md)
