---
title: Azure 安全中心的工作流自動化 |微軟文檔
description: 瞭解如何在 Azure 安全中心創建和自動化工作流
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 972b5415b85e82a5afdaf7d85d3a3bcb9e144d4d
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384937"
---
# <a name="workflow-automation"></a>工作流程自動化

每個安全程式都包含多個用於事件回應的工作流。 這些流程可能包括通知相關專案關係人、啟動變更管理流程以及應用特定的補救步驟。 安全專家建議您盡可能自動執行這些過程的許多步驟。 自動化減少了開銷。 它還可以通過確保流程步驟快速、一致、並根據您的預定義要求完成來提高安全性。

本文介紹了 Azure 安全中心的工作流自動化功能。 此功能可以觸發安全警報和建議的邏輯應用。 例如，您可能希望安全中心在發生警報時向特定使用者發送電子郵件。 您還將瞭解如何使用[Azure 邏輯應用創建邏輯應用](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)。

> [!NOTE]
> 如果您以前在側邊欄上使用 Playbook（預覽）視圖，您將在新的工作流自動化頁面中找到相同的功能以及展開的功能。


## <a name="requirements"></a>需求

* 要使用 Azure 邏輯應用工作流，必須具有以下邏輯應用角色/許可權：

    * [邏輯應用操作員](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-operator)許可權是必需的或邏輯應用讀取/觸發訪問（此角色無法創建或編輯邏輯應用;僅*運行*現有應用）

    * [邏輯應用參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-contributor)許可權是邏輯應用創建和修改所必需的

* 如果要使用邏輯應用連接器，可能需要其他憑據才能登錄到其各自的服務（例如，Outlook/Teams/Slack 實例）


## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>創建邏輯應用並定義何時自動運行 

1. 從安全中心的側邊欄中，選擇**工作流自動化**。

    [![工作流自動化清單](media/workflow-automation/list-of-workflow-automations.png)](media/workflow-automation/list-of-workflow-automations.png#lightbox)

    在此頁面中，您可以創建新的自動化規則，以及啟用、禁用或刪除現有規則。  
1. 要定義新工作流，請按一下"**添加工作流自動化**"。 

    將顯示一個窗格，其中帶有新自動化的選項。 您可以在此處輸入：
    1. 自動化的名稱和說明。
    1. 將啟動此自動工作流的觸發器。 例如，您可能希望邏輯應用在生成包含"SQL"的安全警報時運行。
    1. 滿足觸發器條件時將運行的邏輯應用。 

        [![工作流自動化清單](media/workflow-automation/add-workflow.png)](media/workflow-automation/add-workflow.png#lightbox)

1. 在"操作"部分中，按一下"**創建新操作**"以開始邏輯應用創建過程。

    您將被帶往 Azure 邏輯應用。

    [![創建新的邏輯應用](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. 輸入名稱、資源組和位置，然後按一下"**創建**"。

1. 在新的邏輯應用中，您可以從安全類別中的內置預定義範本中進行選擇。 或者，您可以定義觸發此過程時要發生的事件的自訂流。

    在邏輯應用設計器中，支援安全中心連接器中的以下觸發器：

    * **創建或觸發 Azure 安全中心建議時**
    * **創建或觸發 Azure 安全中心警報時** 
    
    > [!TIP]
    > 您可以自訂觸發器，使其僅與具有您感興趣的嚴重性級別的警報相關聯。
    
    > [!NOTE]
    > 如果使用舊觸發器"觸發對 Azure 安全中心警報的回應"，則工作流自動化功能不會啟動邏輯應用。 而是使用上述任一觸發器。 

    [![示例邏輯應用](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. 定義邏輯應用後，返回到工作流自動化定義窗格（"添加工作流自動化"）。 按一下 **"刷新**"以確保新的邏輯應用可供選擇。

    ![Refresh](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. 選擇邏輯應用並保存自動化。 請注意，邏輯應用下拉清單僅顯示具有上述支援安全中心連接器的邏輯應用。


## <a name="manually-trigger-a-logic-app"></a>手動觸發邏輯應用

您還可以在查看安全警報或任何提供[快速修復修正](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#quick-fix-remediation)的建議時手動運行邏輯應用。

要手動運行邏輯應用，請打開警報或支援快速修復修正的建議，然後按一下 **"觸發邏輯應用**" ：

[![手動觸發邏輯應用](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)

## <a name="data-types-schemas"></a>資料類型架構

要查看傳遞給邏輯應用實例的安全警報或建議事件的原始事件架構，請訪問[工作流自動化資料類型架構](https://aka.ms/ASCAutomationSchemas)。 如果您不使用安全中心的內置邏輯應用連接器上述，而是使用邏輯應用的通用 HTTP 連接器，則此功能非常有用 - 您可以使用事件 JSON 架構在您認為合適的情況下手動分析它。

## <a name="next-steps"></a>後續步驟

在本文中，您瞭解了創建邏輯應用、在安全中心中手動運行它們以及自動化其執行。 

有關其他相關材料，請參閱以下文章： 

- [Azure 資訊安全中心的安全性建議](security-center-recommendations.md)
- [Azure 資訊安全中心的安全性警示](security-center-alerts-overview.md)
- [關於 Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)
- [Logic Apps 連接器](https://docs.microsoft.com/connectors/)
- [工作流自動化資料類型架構](https://aka.ms/ASCAutomationSchemas)
