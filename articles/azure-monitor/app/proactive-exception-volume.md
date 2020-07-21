---
title: 例外狀況磁片區中異常上升-Azure 應用程式深入解析
description: 針對例外狀況磁片區中不尋常的模式，使用 Azure 應用程式 Insights 中的智慧型偵測來監視應用程式例外狀況。
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: 00b7a28a51f91c969b41d2ab85b611f6dde51396
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86499421"
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>磁碟區例外狀況異常升高 (預覽)

Application Insights 會自動分析應用程式中擲回的例外狀況，並且能夠針對例外狀況搖測中的異常模式向您提出警告。

除了為您的應用程式[設定例外狀況報告](./asp-net-exceptions.md#set-up-exception-reporting)以外，這項功能不需要任何特殊設定。 當您的應用程式產生足夠的例外狀況遙測時，它就會是在作用中狀態。

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>何時會取得這個類型的智慧偵測通知？
如果您的應用程式在某一天 (相較於過去七天的計算基準) 發生特定類型例外狀況的次數異常升高，您就可能會收到此類通知。
系統會使用機器學習演算法來偵測例外狀況計數升高的狀況，同時將應用程式使用量自然成長的情況納入考量。

## <a name="does-my-app-definitely-have-a-problem"></a>我的應用程式絕對有問題嗎？
否，通知並不表示您的應用程式一定有問題。 雖然過多的例外狀況數目通常代表應用程式出現問題，但這些例外狀況可能是良性的，並且應用程式也能正確處理。

## <a name="how-do-i-fix-it"></a>如何修正問題？
通知包括要在診斷程序中支援的診斷資訊：
1. **選.** 通知會顯示受影響的使用者人數或要求數。 這可協助您將優先順序指派給此問題。
2. **範圍.** 此問題是否會影響所有流量，還是只會影響某些作業？ 可以從通知取得這項資訊。
3. **診斷.** 偵測包含了擲回例外狀況的方法及例外狀況類型的相關資訊。 您也可以使用連結到支援資訊的相關項目和報表，協助您進一步診斷問題。
