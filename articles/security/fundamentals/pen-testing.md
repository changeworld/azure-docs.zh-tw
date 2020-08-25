---
title: 滲透測試 |Microsoft Docs
description: 本文概述滲透測試程式，以及如何針對在 Azure 基礎結構中執行的應用程式執行畫筆測試。
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2020
ms.author: terrylan
ms.openlocfilehash: dfacf124f8db0e5323c9abff56c4a78f85f6f014
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816128"
---
# <a name="penetration-testing"></a>滲透測試

使用 Azure 進行應用程式測試和部署的其中一個優點是您可以快速建立環境。 您無需購置、取得和組裝自己的內部部署硬體。

快速建立環境非常好用，但您仍然需要確定您已執行正常的安全性檢查。 您可能想要做的其中一件事，就是對您在 Azure 中部署的應用程式進行滲透測試。

您可能已經知道 Microsoft 會執行 [我們的 Azure 環境的滲透測試](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)。 這項測試有助於推動 Azure 的改進。

我們不會為您執行應用程式的滲透測試，但我們瞭解您想要且需要在您自己的應用程式上執行測試。 這是很好的事，因為當您增強應用程式的安全性時，可協助讓整個 Azure 生態系統更安全。

自2017年6月15日起，Microsoft 不再需要預先核准，就能對 Azure 資源進行滲透測試。 此程序僅與 Microsoft Azure 相關，並不適用任何其他 Microsoft 雲端服務。

>[!IMPORTANT]
>雖然通知 Microsoft 相關的滲透測試活動已不再需要，客戶仍須遵守 [Microsoft 雲端整合滲透測試的參與規則](https://technet.microsoft.com/mt784683)。

您可以執行的標準測試包括：

* 對端點進行測試，以發掘 [開放式 Web 應用程式安全性專案 (OWASP) 的前 10 大弱點](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [模糊測試](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)
* [連接埠掃描](https://en.wikipedia.org/wiki/Port_scanner)

您無法執行的一種類型的手寫筆測試，就是任何一種 [拒絕服務 (DoS) ](https://en.wikipedia.org/wiki/Denial-of-service_attack) 攻擊。 這項測試包括起始 DoS 攻擊本身，或執行可能決定、示範或模擬任何類型之 DoS 攻擊的相關測試。

>[!Note]
>Microsoft 已與 BreakingPoint Cloud 合作建立一個介面，您可以針對啟用 DDoS 保護的公用 IP 位址產生流量，以進行模擬。 若要深入瞭解雲端模擬的中斷點，請參閱 [驗證 DDoS 偵測](../../virtual-network/manage-ddos-protection.md#validate-ddos-detection)。

## <a name="next-steps"></a>後續步驟

* 深入瞭解 Engagement 的 [滲透測試規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2)。
