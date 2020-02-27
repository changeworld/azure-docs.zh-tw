---
title: 與 Azure 原則整合-Azure Batch |Microsoft Docs
description: ''
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/24/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 9a306457f838fc79d12be3217d96cc8fb25c9c1b
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77618369"
---
# <a name="integration-with-azure-policy"></a>與 Azure 原則整合

Azure 原則是 Azure 中的一項服務，可讓您用來建立、指派和管理原則，以對您的資源強制執行規則，以確保這些資源仍符合您的公司標準和服務等級協定。 Azure 原則會針對您所指派的原則，評估您的資源是否符合規範。 

Azure Batch 有兩個內建的延伸模組，可協助您管理原則合規性。 

|**名稱**。|   **說明**|**效果（秒）**|  **版本**|    **Source**
|----------------|----------|----------|----------------|---------------|
|應啟用 Batch 帳戶中的診斷記錄|   稽核診斷記錄的啟用情形。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索以供調查之用|AuditIfNotExists, Disabled|  2.0.0|  GitHub|
|應設定 Batch 帳戶上的計量警示規則| 稽核 Batch 帳戶為啟用必要計量而進行的計量警示設定|   AuditIfNotExists, Disabled| 1.0.0|  GitHub|

原則定義會描述需要符合的條件。 條件會將資源屬性與所需的值進行比較。 資源屬性欄位是使用預先定義的別名來存取。您可以使用屬性別名來存取資源類型的特定屬性。 別名可讓您針對資源上的屬性限制所允許的值或條件。 每個別名會對應至指定資源類型之不同 API 版本中的路徑。 在原則評估期間，原則引擎會取得該 API 版本的屬性路徑。

Batch 所需的資源包括：帳戶、計算節點、集區、作業和工作。 因此，您會使用屬性別名來存取這些資源的特定屬性。 深入瞭解[別名](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

為確保您知道目前的別名，並檢查您的資源和原則，請使用適用于 Visual Studio Code 的 Azure 原則延伸模組。 它可以安裝在 Visual Studio Code 支援的所有平臺上。 這種支援包括 Windows、Linux 和 macOS。 請參閱[安裝指導方針](https://docs.microsoft.com/azure/governance/policy/how-to/extension-for-vscode)。



