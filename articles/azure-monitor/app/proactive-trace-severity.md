---
title: 追蹤嚴重性比率降低-Azure 應用程式深入解析
description: 使用 Azure 應用程式 Insights 監視應用程式追蹤，以瞭解使用智慧型偵測的追蹤遙測中不尋常的模式。
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: 30bdd30ac9c49bb79a3c48bae8149ec761756dd4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77671676"
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>追蹤嚴重性比率降低 (預覽)

應用程式廣泛使用追蹤，因為它們可以協助說明在幕後發生的事情。 當錯誤發生時，追蹤可提供導致不良狀態事件的順序很重要的可見性。 雖然追蹤通常非結構化，但可以從中具體學到一件事 - 其嚴重性層級。 在應用程式的穩定狀態中，我們會預期「良好」追蹤 (*資訊*和*詳細資訊*) 與「不良」追蹤 (*警告*、*錯誤*和*重大*) 之間的比率會保持穩定。 此處的假設是「不良」追蹤可能會因為許多原因 (例如暫時性網路問題) 定期發生在某個程度。 但是，當實際的問題開始成長時，通常會以「不良」追蹤與「良好」追蹤的相對比例增加來顯露。 Application Insights 智慧偵測會自動分析您的應用程式所記錄的追蹤，並警告您有關追蹤遙測嚴重性中的異常模式。

此功能不需任何特殊的設定，只需要為您的應用程式設定追蹤記錄 (請參閱如何為 [.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-trace-logs) 或 [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-trace-logs) 設定追蹤記錄檔接聽程式)。 當您的應用程式產生足夠的例外狀況遙測時，它就會是在作用中狀態。

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>何時會取得這個類型的智慧偵測通知？
相較于過去七天所計算的基準，如果「良好」追蹤（以*資訊*層級或*詳細*資訊記錄的追蹤）和「不良」追蹤（以*警告*、*錯誤*或*嚴重*程度記錄的追蹤）之間的比率降低，您可能會收到這種類型的通知。

## <a name="does-my-app-definitely-have-a-problem"></a>我的應用程式絕對有問題嗎？
否，通知並不表示您的應用程式一定有問題。 雖然「良好」與「不良」追蹤之間的比率降低可能表示應用程式問題，這項比例的變更可能會是良性。 例如，增加可能是因為應用程式由於新流程而發出較現有流程更多個「不良」追蹤)。

## <a name="how-do-i-fix-it"></a>如何修正問題？
通知包括要在診斷程序中支援的診斷資訊：
1. **選.** 通知會顯示受影響的作業數。 這可協助您將優先順序指派給此問題。
2. **範圍.** 此問題是否會影響所有流量，還是只會影響某些作業？ 可以從通知取得這項資訊。
3. **診斷.** 您可以使用連結到支援資訊的相關項目和報表，協助您進一步診斷問題。


