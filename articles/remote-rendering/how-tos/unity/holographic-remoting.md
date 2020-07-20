---
title: 在 Unity 中使用全像攝影遠端處理和遠端轉譯
description: 全像攝影遠端預覽如何搭配 Azure 遠端呈現使用
author: christophermanthei
ms.author: chmant
ms.date: 03/23/2020
ms.topic: how-to
ms.openlocfilehash: ac47a2922e92233f0acabf75817a712671306bc1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80681203"
---
# <a name="use-holographic-remoting-and-remote-rendering-in-unity"></a>在 Unity 中使用全像攝影遠端處理和遠端轉譯

全像攝影[遠端](https://docs.microsoft.com/windows/mixed-reality/holographic-remoting-player)和 Azure 遠端轉譯在一個應用程式內是互斥的。 因此， [Unity 播放模式](https://docs.microsoft.com/windows/mixed-reality/unity-play-mode)也無法使用。

每次執行 Unity editor 時，都只能使用其中一種。 若要使用另一個，請先重新開機 Unity。

## <a name="use-unity-play-mode-to-preview-on-hololens-2"></a>在 Hololens 2 上使用 Unity 播放模式預覽

 您仍然可以使用 Unity 播放模式，例如測試應用程式的 UI。 不過，ARR 絕對不會初始化。 否則會損毀。

## <a name="use-a-wmr-vr-headset-to-preview-on-desktop"></a>使用 WMR VR 頭戴式裝置在桌面上預覽

如果有 Windows Mixed Reality VR 頭戴式裝置，它可以用來在 Unity 內部進行預覽。 在此情況下，您可以初始化 ARR，不過使用 WMR 頭戴式裝置時，將無法連接到會話。
