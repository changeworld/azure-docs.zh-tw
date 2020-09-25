---
title: Azure 資訊安全中心事件-警示的智慧型相互關聯
description: 本主題說明融合如何使用 cloud smart alert 相互關聯來產生 Azure 資訊安全中心中的安全性事件。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e9d5a771-bfbe-458c-9a9b-a10ece895ec1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: b60798ad1fab0ed66f3d4a39b2fd7faf79fd515e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91268055"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Azure 資訊安全中心 (事件) 中的雲端智慧警示相互關聯

Azure 資訊安全中心持續分析混合式雲端工作負載，方法是使用先進的分析和威脅情報警示您有關惡意活動。

威脅涵蓋範圍的廣度不斷增加。 即使極小入侵的需求也很重要，但安全性分析師可能會很難將不同的警示分級，並找出實際的攻擊。 安全性中心可協助分析師解決此警示疲勞。 它藉由將不同的警示和低精確度信號關聯到安全性事件，協助診斷發生的攻擊。

融合分析是支援安全中心事件的技術和分析後端，可讓 it 將不同的警示和內容相關的信號相互關聯。 融合會查看跨資源在訂用帳戶上報告的不同信號。 融合可尋找使用共用內容資訊來顯示攻擊進展或信號的模式，表示您應該針對它們使用統一的回應程式。

融合分析結合了安全性領域知識與 AI，來分析警示，並在發生新的攻擊模式時進行探索。 

資訊安全中心會利用 MITRE 攻擊對照表來建立警示與其認知意圖的關聯，以協助將安全性領域的知識正規化。 此外，藉由使用針對攻擊的每個步驟所收集的資訊，資訊安全中心可以排除似乎是攻擊步驟的活動，但實際上並不是。

由於攻擊通常會發生在不同的租使用者之間，資訊安全中心可以結合 AI 演算法來分析每個訂用帳戶上報告的攻擊順序。 這項技術會將攻擊順序識別為常見的警示模式，而不是只是彼此相關的。

在調查事件的過程中，分析師通常需要額外的內容，以達成有關威脅本質的結論，以及如何緩和此問題。 例如，即使在偵測到網路異常的情況下，如果您不知道網路上有哪些其他狀況，或有關于目標資源的情況，就很難瞭解接下來要採取哪些動作。 為了提供協助，安全性事件可以包含構件、相關事件和資訊。 適用于安全性事件的其他資訊會因偵測到的威脅類型和環境的設定而有所不同。 

![偵測到的安全性附隨報告的螢幕擷取畫面](./media/security-center-alerts-cloud-smart/security-incident.png)

若要進一步瞭解安全性事件，請參閱 [如何處理 Azure 資訊安全中心中的安全性事件](security-center-incident.md)。

