---
title: Azure 資訊安全中心威脅情報報告 | Microsoft Docs
description: 本文件可協助您在調查期間使用 Azure 資訊安全中心威脅情報報告，找出更多關於安全性警示的資訊。
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
ms.date: 11/28/2018
ms.author: memildin
ms.openlocfilehash: f8b4063d87fa9a89dccd42eddea644609bd6ff27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921244"
---
# <a name="azure-security-center-threat-intelligence-report"></a>Azure 資訊安全中心威脅情報報告
本文件說明 Azure 資訊安全中心威脅情報報告可如何協助您深入了解產生安全性警示的威脅。

## <a name="what-is-a-threat-intelligence-report"></a>何謂威脅情報報告？
安全中心威脅保護的工作原理是監視來自 Azure 資源、網路和連接的合作夥伴解決方案的安全資訊。 它會分析這項資訊 (通常是來自多個來源的相互關聯資訊) 以識別威脅。 有關詳細資訊，請參閱[Azure 安全中心如何檢測和回應威脅](security-center-alerts-overview.md#detect-threats)。

當資訊安全中心識別到威脅時，便會觸發[安全性警示](security-center-managing-and-responding-alerts.md)，其內含關於特定事件的詳細資訊，包括修復方面的建議。 為了協助事件回應小組調查和補救威脅，資訊安全中心包含威脅情報報告，其中包含所偵測到威脅的相關資訊，包括如下資訊：

* 攻擊者的身分識別或關聯 (如果有提供這項資訊)
* 攻擊者的目標
* 目前和過往的攻擊活動 (如果有提供這項資訊)
* 攻擊者的策略、工具和程序
* 關聯的入侵指標 (IoC)，例如 URL 和檔案雜湊
* 受害者研究 (Victimology)，亦即在業界和地理區域方面的盛行情況，可協助您判斷 Azure 資源是否有風險
* 緩和與修復資訊

> [!NOTE]
> 任何特定報告中所含的資訊數量各有不同；詳細程度視惡意程式碼的活動和盛行情況而定。
>
>

資訊安全中心有三種威脅報告，不同攻擊會提供不同的報告。 所提供的報告如下︰

* **活動組報告**：提供深入深入攻擊者，他們的目標和戰術。
* **活動報告**︰著重說明特定攻擊活動的詳細資料。
* **威脅摘要報告**︰涵蓋先前兩種報告的所有項目。

這類資訊在事件回應處理期間非常有用，因為該期間會持續進行調查，以了解攻擊來源、攻擊者的動機，以及日後該如何減少此問題。

## <a name="how-to-access-the-threat-intelligence-report"></a>如何存取威脅情報報告？
您可以查看 [安全性警示] **** 圖格來檢視目前的警示。 開啟 Azure 入口網站並遵循下列步驟來查看有關每個警示的詳細資料：

1. 您會在 [資訊安全中心] 儀表板看到 [安全性警示] **** 圖格。
2. 按一下圖格便可開啟 [安全性警示]**** 刀鋒視窗，其中包含有關警示的更多詳細資料，按一下您想了解的安全性警示即可取得更多相關資訊。

    ![安全性警示](./media/security-center-threat-report/security-center-threat-report-fig1.png)
3. 在此案例中，[已執行的可疑處理序]**** 刀鋒視窗會顯示警示的詳細資料，如下圖所示︰

    ![安全性警示詳細資料](./media/security-center-threat-report/security-center-threat-report-fig2.png)
4. 根據警示類型而定，針對每個安全性警示所提供的資訊數量會有所不同。 [報告]**** 欄位中有威脅情報報告的連結。 按一下連結，便會出現另一個含有 PDF 檔案的瀏覽器視窗。

   ![儲存體選擇](./media/security-center-threat-report/security-center-threat-report-fig3.png)

您可以從這裡下載此報告的 PDF，深入了解偵測到的安全性問題，並根據所提供的資訊採取動作。

## <a name="see-also"></a>另請參閱
在本文件中，您已了解 Azure 資訊安全中心威脅情報報告如何在安全性警示調查期間幫上忙。 若要深入了解「Azure 資訊安全中心」，請參閱下列主題：

* [Azure 資訊安全中心規劃和操作指南](security-center-planning-and-operations-guide.md)。 了解如何規劃及了解採用 Azure 資訊安全中心的設計考量。
* [管理和回應 Azure 安全中心中的安全警報](security-center-managing-and-responding-alerts.md)。 了解如何管理和回應安全性警示。
* [在 Azure 資訊安全中心處理安全性事件](security-center-incident.md)