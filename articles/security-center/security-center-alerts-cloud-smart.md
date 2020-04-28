---
title: Azure 資訊安全中心事件-警示的智慧型相互關聯
description: 本主題說明融合如何使用雲端智慧警示相互關聯來產生 Azure 資訊安全中心中的安全性事件。
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
ms.openlocfilehash: b26f0bab073ce248ca23bb8a815fa3e293ddba51
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "73686485"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Azure 資訊安全中心（事件）中的雲端智慧警示相互關聯

Azure 資訊安全中心持續分析混合式雲端工作負載，方法是使用 advanced analytics 和威脅情報來警示您有關惡意活動。

威脅涵蓋範圍的廣度日益增加。 偵測警鈴危害的需求也很重要，而且安全性分析師可能會很難將不同的警示分級，並識別實際的攻擊。 資訊安全中心可協助分析師解決此警示疲勞。 它藉由將不同的警示和低精確度信號相互關聯至安全性事件，協助診斷發生的攻擊。

融合分析是協助資訊安全中心事件的技術和分析後端，讓 it 能夠將不同的警示和內容相關的信號相互關聯。 融合會查看在各資源的訂用帳戶上報告的不同信號。 融合會尋找使用共用內容相關資訊來顯示攻擊進度或信號的模式，這表示您應該對它們使用統一的回應程式。

融合分析結合了安全性網域知識與 AI 來分析警示，並在發生新的攻擊模式時進行探索。 

資訊安全中心利用 MITRE 攻擊對照表，將警示與其認知意圖產生關聯，以協助將安全性網域知識正規化。 此外，藉由使用針對攻擊的每個步驟所收集的資訊，資訊安全中心可以排除似乎是攻擊步驟的活動，但實際上並不是。

因為攻擊通常會在不同的租使用者之間發生，資訊安全中心可以結合 AI 演算法來分析每個訂用帳戶上報告的攻擊順序。 這項技術會將攻擊序列識別為普遍的警示模式，而不是彼此相關聯。

在調查事件的過程中，分析師通常需要額外的內容，才能觸達關於威脅本質的結論，以及如何加以緩和。 例如，即使偵測到網路異常，而不了解網路上或目標資源的其他情況，還是很難以瞭解接下來要採取哪些動作。 為了提供協助，安全性事件可以包含構件、相關事件和資訊。 適用于安全性事件的其他資訊會因偵測到的威脅類型和環境設定而有所不同。 

![偵測到安全性附隨報告的螢幕擷取畫面](./media/security-center-alerts-cloud-smart/security-incident.png)

若要進一步瞭解安全性事件，請參閱[如何處理 Azure 資訊安全中心中的安全性事件](https://docs.microsoft.com/azure/security-center/security-center-incident)。

