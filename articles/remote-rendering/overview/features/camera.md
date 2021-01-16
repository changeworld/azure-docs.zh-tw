---
title: 相機
description: 描述攝影機設定和使用案例
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.openlocfilehash: 76bb9d289e984dd8c229bdaaab09e679e11283fe
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2021
ms.locfileid: "98246276"
---
# <a name="camera"></a>相機

為了確保本機和遠端轉譯的內容可以順暢地合成，在伺服器與用戶端之間必須保持同步各種不同的相機屬性。 最值得注意的是相機的轉換和投射。 例如，在本機轉譯的內容必須使用相同的相機轉換和投影，來呈現最新的遠端框架。

![本機和遠端攝影機](./media/camera.png)

在上圖中，相較于伺服器傳送的遠端框架，本機相機已移動。 如此一來，就必須從與遠端框架相同的觀點來轉譯本機內容，最後產生的影像會 reprojected 到本機相機空間中，這在 [晚期階段 reprojection](late-stage-reprojection.md)中有詳細的說明。

遠端和本機轉譯之間的延遲，表示這些設定的任何變更都會套用延遲。 因此，任何新的值都不能立即用在相同的框架中。

您可以在相機設定上設定近和遠的平面距離。 在 HoloLens 2 上，其餘的轉換和投影資料是由硬體所定義。 使用 [桌面模擬](../../concepts/graphics-bindings.md)時，會透過其功能的輸入來設定 `Update` 。

變更的值可以在本機使用變更後的第一個遠端框架抵達時使用。 在 HoloLens 2 上，要用於轉譯的資料是由 *HolographicFrame* 所提供，就像在任何其他的全像任何應用程式一樣。 在 [桌面模擬](../../concepts/graphics-bindings.md)中，它們是透過函式的輸出來抓取 `Update` 。

## <a name="camera-settings"></a>攝影機設定

下列屬性可以在相機設定上變更：

**近和遠的平面：**

為確保無法設定不正確範圍， **NearPlane** 和 **FarPlane** 屬性是唯讀的，而且有個別的函式 **SetNearAndFarPlane** 可變更範圍。 此資料將會傳送至框架結尾的伺服器。 設定這些值時， **NearPlane** 必須小於 **FarPlane**。 否則將會發生錯誤。

> [!IMPORTANT]
> 在 Unity 中，這會在變更主要相機接近或遠的平面時自動處理。

**EnableDepth**：

有時，停用遠端影像的深度緩衝區寫入以進行偵測是很有説明的。 停用深度也會阻止伺服器傳送深度資料，進而減少頻寬。

> [!TIP]
> 在 Unity 中，提供稱為 **EnableDepthComponent** 的 debug 元件，可用來在編輯器 UI 中切換這項功能。

**InverseDepth**：

> [!NOTE]
> 只有當設定為時，此設定才會很重要 `EnableDepth` `true` 。 否則，此設定不會有任何影響。

深度緩衝區通常會在 [0; 1] 的浮點範圍中記錄 z 值，0表示近平面深度，1表示最遠的深度。 您也可以反轉此範圍，並在範圍 [1; 0] 中記錄深度值，也就是說，接近平面的深度會變成1，而最遠的深度則會變成0。 一般來說，後者會改善跨非線性 z 範圍的浮點精確度分佈。

> [!WARNING]
> 常見的方法是將相機物件上的接近平面和遠平面值反轉。 在上嘗試這種情況時，Azure 遠端轉譯發生錯誤，這將會失敗 `CameraSettings` 。

Azure 遠端轉譯 API 必須知道本機轉譯器的深度緩衝區慣例，才能正確地將遠端深度組成本機深度緩衝區。 如果您的深度緩衝區範圍是 [0，1]，請將此旗標保留為 `false` 。 如果您使用具有 [1; 0] 範圍的反向深度緩衝區，請將旗標設定 `InverseDepth` 為 `true` 。

> [!NOTE]
> 針對 Unity，已套用正確的設定，因此不 `RemoteManager` 需要手動介入。

您可以依照下列方式來變更相機設定：

```cs
void ChangeCameraSetting(AzureSession session)
{
    CameraSettings settings = session.Actions.CameraSettings;

    settings.SetNearAndFarPlane(0.1f, 20.0f);
    settings.EnableDepth = false;
    settings.InverseDepth = false;
}
```

```cpp
void ChangeStageSpace(ApiHandle<AzureSession> session)
{
    ApiHandle<CameraSettings> settings = session->Actions()->GetCameraSettings();

    settings->SetNearAndFarPlane(0.1f, 20.0f);
    settings->SetEnableDepth(false);
    settings->SetInverseDepth(false);
}
```

## <a name="api-documentation"></a>API 文件

* [C # CameraSettings](/dotnet/api/microsoft.azure.remoterendering.camerasettings)
* [C + + CameraSettings](/cpp/api/remote-rendering/camerasettings)
* [C # GraphicsBindingSimD3d11. Update 函數](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11.update)
* [C + + GraphicsBindingSimD3d11：： Update 函數](/cpp/api/remote-rendering/graphicsbindingsimd3d11#update)

## <a name="next-steps"></a>後續步驟

* [圖形繫結](../../concepts/graphics-bindings.md)
* [延遲階段重新投影](late-stage-reprojection.md)