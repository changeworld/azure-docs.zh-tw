---
title: Azure 資訊安全中心威脅情報報告 | Microsoft Docs
description: 此頁面可協助您在調查期間使用 Azure 資訊安全中心威脅情報報告，以尋找有關安全性警示的詳細資訊
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5662e312-e8c2-4736-974e-576eeb333484
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2020
ms.author: memildin
ms.openlocfilehash: a4fdbab4a69fac1376779f37d5fa69fef587bf52
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84888251"
---
# <a name="azure-security-center-threat-intelligence-report"></a>Azure 資訊安全中心威脅情報報告

此頁面會說明 Azure 資訊安全中心的威脅情報報告如何協助您深入瞭解觸發安全性警示的威脅。


## <a name="what-is-a-threat-intelligence-report"></a>何謂威脅情報報告？

資訊安全中心威脅防護的運作方式是從您的 Azure 資源、網路和已連線的合作夥伴解決方案監視安全性資訊。 它會分析這項資訊 (通常是來自多個來源的相互關聯資訊) 以識別威脅。 如需詳細資訊，請參閱[Azure 資訊安全中心偵測和回應威脅的方式](security-center-alerts-overview.md#detect-threats)。

當資訊安全中心識別威脅時，它會觸發[安全性警示](security-center-managing-and-responding-alerts.md)，其中包含關於事件的詳細資訊，包括補救的建議。 為了協助事件回應小組調查和修復威脅，資訊安全中心提供威脅情報報告，其中包含偵測到之威脅的相關資訊。 此報表包含下列資訊：

* 攻擊者的身分識別或關聯 (如果有提供這項資訊)
* 攻擊者的目標
* 目前和過往的攻擊活動 (如果有提供這項資訊)
* 攻擊者的策略、工具和程序
* 關聯的入侵指標 (IoC)，例如 URL 和檔案雜湊
* 受害者研究 (Victimology)，亦即在業界和地理區域方面的盛行情況，可協助您判斷 Azure 資源是否有風險
* 緩和與修復資訊

> [!NOTE]
> 任何特定報告中所含的資訊數量各有不同；詳細程度視惡意程式碼的活動和盛行情況而定。

資訊安全中心有三種威脅報告，不同攻擊會提供不同的報告。 所提供的報告如下︰

* **活動群組報告**：提供攻擊者、其目標和策略的深入探討。
* **活動報告**︰著重說明特定攻擊活動的詳細資料。
* **威脅摘要報告**︰涵蓋先前兩種報告的所有項目。

這種類型的資訊在事件回應程式期間非常有用，因為它會持續進行調查，以瞭解攻擊來源、攻擊者的動機，以及未來應如何解決此問題。



## <a name="how-to-access-the-threat-intelligence-report"></a>如何存取威脅情報報告？

1. 從資訊安全中心的提要欄位中，開啟 [**安全性警示**] 頁面。
1. 選取警示。 
    [警示詳細資料] 頁面隨即開啟，其中包含有關警示的更多詳細資料。 以下是偵測**到的勒索軟體指示器**警示詳細資料頁面。

    [![偵測到的勒索軟體指示器警示詳細資料頁面](media/security-center-threat-report/ransomware-indicators-detected-link-to-threat-intel-report.png)](media/security-center-threat-report/ransomware-indicators-detected-link-to-threat-intel-report.png#lightbox)

1. 選取報表的連結，PDF 就會在您的預設瀏覽器中開啟。

    [![可能不安全的動作警示詳細資料頁面](media/security-center-threat-report/threat-intelligence-report.png)](media/security-center-threat-report/threat-intelligence-report.png#lightbox)

    您可以選擇下載 PDF 報告。 

    >[!TIP]
    > 根據警示類型而定，針對每個安全性警示所提供的資訊數量會有所不同。



## <a name="next-steps"></a>後續步驟

此頁面說明如何在調查安全性警示時開啟威脅情報報告。 如需相關資訊，請參閱下列頁面：

* [管理和回應 Azure 資訊安全中心中的安全性警示](security-center-managing-and-responding-alerts.md)。 了解如何管理和回應安全性警示。
* [處理 Azure 資訊安全中心中的安全性事件](security-center-incident.md)