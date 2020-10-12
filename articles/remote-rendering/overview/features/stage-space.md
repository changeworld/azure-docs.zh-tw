---
title: 階段空間
description: 描述階段空間設定和使用案例
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 5809494fb8b619569316a24816a2e5d943dee6b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89013124"
---
# <a name="stage-space"></a>階段空間

在提供前端追蹤資料（例如 HoloLens 2）的裝置上執行 ARR 時，會將 head 姿勢傳送給使用者應用程式和伺服器。 在其中定義 head 轉換的空間稱為「 *階段空間*」。

為了對齊本機和遠端內容，假設在用戶端和伺服器上的階段空間和世界空間相同。 如果使用者決定在相機頂端新增額外的轉換，則必須將它傳送到伺服器，以正確地對齊本機和遠端內容。

移動階段空間的一般原因是 [世界鎖定工具](https://microsoft.github.io/MixedReality-WorldLockingTools-Unity/README.html) 或其他真實世界錨定技術，以及在 VR 中移動虛擬字元。

## <a name="stage-space-settings"></a>階段空間設定

> [!CAUTION]
> 實驗性：這項功能是實驗性的，會隨著時間而改變。 因此，更新至較新的用戶端 SDK 版本可能需要額外的工作才能升級程式碼。 目前的實作為變更階段空間原點時，會中斷本機/遠端內容的對齊。
因此，它目前僅適用于世界上的鎖定用途，例如僅顯示一段時間內只有極小變更的錨點。

若要通知伺服器將額外的轉換套用至階段空間，其來源是由位置所定義，而在世界空間中的旋轉則需要傳送。 您可以透過 *階段空間設定*來存取這種設定。

> [!IMPORTANT]
> 在 [桌上型電腦模擬](../../concepts/graphics-bindings.md) 中，相機的世界空間位置是由使用者應用程式所提供。 在此情況下，必須略過設定階段空間原點，因為它已經乘以相機轉換。

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
    ApiHandle<StageSpaceSettings> settings = session->Actions()->GetStageSpaceSettings();

    // Set position and rotation to the world-space transform of the stage space.
    settings->SetPosition({0, 0, 0});
    settings->SetRotation({0, 0, 0, 1});
}
```

## <a name="unity-stage-space-script"></a>Unity 階段空間腳本

Unity 整合提供一個稱為 **StageSpace** 的腳本，可新增至相機的父 GameObject。 一旦出現，此腳本將會負責設定階段空間原點。

## <a name="next-steps"></a>後續步驟

* [圖形繫結](../../concepts/graphics-bindings.md)
* [延遲階段重新投影](late-stage-reprojection.md)
