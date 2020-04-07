---
title: 統一使用全息調整和遠端渲染
description: 如何將全息遠端渲染預覽與 Azure 遠端渲染結合使用
author: christophermanthei
ms.author: chmant
ms.date: 03/23/2020
ms.topic: how-to
ms.openlocfilehash: ac47a2922e92233f0acabf75817a712671306bc1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681203"
---
# <a name="use-holographic-remoting-and-remote-rendering-in-unity"></a>統一使用全息調整和遠端渲染

[全息遠端渲染](https://docs.microsoft.com/windows/mixed-reality/holographic-remoting-player)和 Azure 遠端渲染在一個應用程式中是互斥的。 因此[,Unity 播放模式](https://docs.microsoft.com/windows/mixed-reality/unity-play-mode)也不可用。

對於 Unity 編輯器的每個運行,只能使用兩個編輯器中的一個。 要使用另一個,請首先重新啟動 Unity。

## <a name="use-unity-play-mode-to-preview-on-hololens-2"></a>使用 Unity 播放模式在 Hololens 2 上預覽

 Unity 播放模式仍可以使用,例如用於測試應用程式的 UI。 但是,始終不初始化 ARR 至關重要。 否則,它會崩潰。

## <a name="use-a-wmr-vr-headset-to-preview-on-desktop"></a>使用 WMR VR 耳機在桌面上預覽

如果存在 Windows 混合實境 VR 耳機,則可用於在 Unity 中預覽。 在這種情況下,可以初始化 ARR,但在使用 WMR 耳機時無法連接到會話。
