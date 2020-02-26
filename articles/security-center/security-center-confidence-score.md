---
title: Azure 資訊安全中心的信賴分數 | Microsoft Docs
description: 資訊安全中心會產生信賴分數，以協助您的小組判斷威脅是否合法，以及如何分級和設定警示的優先順序。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e88198f8-2e16-409d-a0b0-a62e68c2f999
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: memildin
ms.openlocfilehash: 8aa17f473c550c99d91862754e6a746575aca202
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604377"
---
# <a name="alert-confidence-score-preview"></a>警示信賴分數（預覽）

Azure 資訊安全中心可讓您檢視在 Azure 中執行的各項資源，並在它偵測到潛在的問題時警示您。 安全性作業小組要個別處理為數眾多的警示可能會十分困難，因此終究必須要排定調查警示的優先順序。 調查警示可能既複雜又耗時，因此會忽略某些警示。

資訊安全中心中的信賴分數（目前處於預覽狀態）可協助您的小組分級和排定警示的優先順序。 資訊安全中心會自動套用業界最佳做法、智慧型演算法和分析人員所使用的程序，以判斷某項威脅是否合法，並以信賴分數的形式為您提供有意義的深入解析。

## <a name="how-the-confidence-score-is-triggered"></a>信賴分數的觸發方式

在偵測到有可疑的程序在您的虛擬機器上執行時，即會產生警示。 資訊安全中心會檢閱並分析這些針對執行於 Azure 中的 Windows 虛擬機器所發出的警示。 它會使用進階演算法，對整個組織的多個實體和資料來源以及您所有的 Azure 資源執行自動檢查和相互關聯，並提供信賴分數，用以評量資訊安全中心認定警示屬實而需受到調查的把握度。

## <a name="understanding-the-confidence-score"></a>了解信賴分數

信賴分數介於 1 到 100 的範圍間，代表代表資訊安全中心認定警示需受到調查的把握度。 分數愈高，資訊安全中心認定警示確實表示惡意活動的把握度就愈高。 信賴分數會有一份主要原因清單，用以說明警示獲得其信賴分數的原因。 信賴分數有助於安全性分析人員排定其處理警示的優先順序，並優先解決最迫切的攻擊，最終縮短因應攻擊和漏洞所需的時間。

若要檢視信賴分數：
- 在資訊安全中心入口網站中，開啟 [安全性警示] 刀鋒視窗。
-  警示和事件會由高至低排列，這表示資訊安全中心愈有把握認定為威脅的警示，會愈靠近頁面頂端。 


 ![信賴分數][1]

若要檢視警示中構成資訊安全中心信賴度的資料：
- 在 [安全性警示] 刀鋒視窗中的 [信賴度] 下方，檢視構成信賴分數的觀察值，並取得該警示的相關深入解析。 這可讓您深入了解造成警示的活動所屬的性質。

  ![可疑信賴分數][2]

使用資訊安全中心的信賴分數，在您的環境中排定警示分級的優先順序。 信賴分數可節省您的時間和心力，因為它會自動調查警示、套用業界最佳做法和智慧型演算法，並化身為虛擬分析人員來判斷哪些是真正的威脅，以及您需要注意哪些部分。


<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
