---
title: 滲透測試 | Microsoft Docs
description: 本文提供滲透測試 (pentest) 程序和如何對 Azure 基礎結構中執行的應用程式執行滲透測試的概觀。
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
ms.date: 08/13/2018
ms.author: terrylan
ms.openlocfilehash: 413ec8b121838a4ffac4119421ec3266e141618b
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549260"
---
# <a name="penetration-testing"></a>滲透測試
使用 Azure 進行應用程式測試和部署的其中一個優點是您可以快速建立環境。 您無需購置、取得和組裝自己的內部部署硬體。

這很好 - 但您仍然需要確定您審慎執行一般的安全性作業。 您可能想要做的一件事是滲透測試在 Azure 中部署的應用程式。

您可能已經知道 Microsoft 會執行 [我們的 Azure 環境的滲透測試](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)。 這有助於促進 Azure 改進。

我們不會為您滲透測試您的應用程式,但我們確實瞭解您需要並且需要在您自己的應用程式上執行測試。 這是一件好事,因為當您增強應用程式的安全性時,有助於確保整個 Azure 生態系統的安全。

自 2017 年 6 月 15 日起,Microsoft 不再需要預先批准才能對 Azure 資源執行滲透測試。 要對 Microsoft Azure 正式提出近期滲透測試合作申請的客戶，建議填寫 [Azure 服務滲透測試通知單](https://portal.msrc.microsoft.com/en-us/engage/pentest)。 此程序僅與 Microsoft Azure 相關，並不適用任何其他 Microsoft 雲端服務。

>[!IMPORTANT]
>雖然通知 Microsoft 相關的滲透測試活動已不再需要，客戶仍須遵守 [Microsoft 雲端整合滲透測試的參與規則](https://technet.microsoft.com/mt784683)。

您可以執行的標準測試包括：

* 對端點進行測試，以發掘 [開放式 Web 應用程式安全性專案 (OWASP) 的前 10 大弱點](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [模糊測試](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)
* [連接埠掃描](https://en.wikipedia.org/wiki/Port_scanner)

您不能執行的一種測試是任何種類的 [拒絕服務 (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) 攻擊。 這包括起始 DoS 攻擊本身，或是執行可能會決定、示範或模擬任何類型的 DoS 攻擊的相關測試。

## <a name="next-steps"></a>後續步驟

- 如果要正式記錄針對 Microsoft Azure 中託管的應用程式即將進行的滲透測試,請繼續執行[滲透測試參與規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2)並填寫測試通知表。
