---
title: Azure 安全中心事件 - 警報的智慧關聯
description: 本主題介紹融合如何使用雲智慧警報關聯在 Azure 安全中心生成安全事件。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73686485"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Azure 安全中心中的雲智慧警報關聯（事件）

Azure 安全中心使用高級分析和威脅情報不斷分析混合雲工作負載，以提醒您有關惡意活動。

威脅覆蓋的廣度正在擴大。 需要檢測哪怕是絲毫的妥協也非常重要，安全分析人員對不同的警報進行會審並識別實際攻擊可能具有挑戰性。 安全中心可説明分析師應對這種警報疲勞。 通過將不同的警報和低傳真度信號關聯到安全事件中，它有助於診斷攻擊發生時的攻擊。

Fusion 分析是為安全中心事件提供支援的技術和分析後端，使其能夠將不同的警報和上下文信號關聯在一起。 Fusion 查看跨資源訂閱上報告的不同信號。 Fusion 發現具有共用上下文資訊的攻擊進度或信號的模式，指示您應該對它們使用統一的回應過程。

Fusion 分析將安全域知識與 AI 相結合，以分析警報，發現新的攻擊模式。 

安全中心利用 MITRE 攻擊矩陣將警報與其感知意圖相關聯，説明正式化安全域知識。 此外，通過使用為攻擊的每個步驟收集的資訊，安全中心可以排除看似攻擊步驟但實際上不是的活動。

由於攻擊通常發生在不同的租戶之間，因此安全中心可以組合 AI 演算法來分析每個訂閱上報告的攻擊序列。 此技術將攻擊序列標識為流行的警報模式，而不是只是偶然地相互關聯。

在調查事件期間，分析人員通常需要額外的上下文才能就威脅的性質以及如何減輕威脅做出判斷。 例如，即使檢測到網路異常，也不了解網路上或目標資源上還發生了什麼，也很難理解接下來要執行哪些操作。 為了提供説明，安全事件可以包括工件、相關事件和資訊。 可用於安全事件的其他資訊因檢測到的威脅類型和環境配置而異。 

![檢測到的安全附隨報告的螢幕截圖](./media/security-center-alerts-cloud-smart/security-incident.png)

要更好地瞭解安全事件，請參閱[如何在 Azure 安全中心中處理安全事件](https://docs.microsoft.com/azure/security-center/security-center-incident)。

