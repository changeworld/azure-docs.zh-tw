---
title: 與 Azure 策略集成 - Azure 批次處理 |微軟文檔
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77618369"
---
# <a name="integration-with-azure-policy"></a>與 Azure 策略集成

Azure 策略是 Azure 中用於創建、分配和管理原則的服務，用於在資源上強制實施規則，以確保這些資源始終符合公司標準和服務等級協定。 Azure 策略評估資源是否不符合您分配的策略。 

Azure Batch 具有兩個內置擴展，可説明您管理原則合規性。 

|**名稱**...|   **描述**|**效果**|  **版本**|    **來源**
|----------------|----------|----------|----------------|---------------|
|應啟用 Batch 帳戶中的診斷記錄|   稽核診斷記錄的啟用情形。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索以供調查之用|AuditIfNotExists, Disabled|  2.0.0|  GitHub|
|Batch 帳戶上應設定計量警示規則| 稽核 Batch 帳戶為啟用必要計量而進行的計量警示設定|   AuditIfNotExists, Disabled| 1.0.0|  GitHub|

策略定義描述需要滿足的條件。 條件將資源屬性與所需值進行比較。 使用預定義的別名訪問資源屬性欄位。使用屬性別名訪問資源類型的特定屬性。 別名可讓您針對資源上的屬性限制所允許的值或條件。 每個別名會對應至指定資源類型之不同 API 版本中的路徑。 在原則評估期間，原則引擎會取得該 API 版本的屬性路徑。

Batch 所需的資源包括：帳戶、計算節點、池、作業和任務。 因此，您可以使用屬性別名訪問這些資源的特定屬性。 瞭解有關[別名](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)的更多

為了確保知道當前別名並查看資源和策略，請使用 Visual Studio 代碼的 Azure 策略擴展。 它可以安裝在視覺工作室代碼支援的所有平臺上。 此支援包括 Windows、Linux 和 macOS。 請參閱[安裝指南](https://docs.microsoft.com/azure/governance/policy/how-to/extension-for-vscode)。



