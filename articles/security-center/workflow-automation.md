---
title: Azure 安全中心的工作流自動化 |微軟文件
description: 瞭解如何在 Azure 安全中心建立和自動化工作流
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 5d947cf41e13abdea9a2fd29f8a740d0c101dc6f
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397901"
---
# <a name="workflow-automation"></a>工作流程自動化

每個安全程式都包含多個用於事件回應的工作流。 這些流程可能包括通知相關利益幹系人、啟動變更管理流程以及應用特定的補救步驟。 安全專家建議您盡可能自動執行這些過程的許多步驟。 自動化減少了開銷。 它還可以通過確保流程步驟快速、一致、並根據您的預定義要求完成來提高安全性。

本文介紹了 Azure 安全中心的工作流自動化功能。 此功能可以觸發安全警報和建議的邏輯應用。 例如,您可能希望安全中心在發生警報時向特定使用者發送電子郵件。 您還將瞭解如何使用[Azure 邏輯應用建立邏輯應用](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)。

> [!NOTE]
> 如果您以前在側邊欄上使用 Playbook(預覽)檢視,您將在新的工作流自動化頁面中找到相同的功能以及展開的功能。


## <a name="requirements"></a>需求

* 要使用 Azure 邏輯應用工作流,必須具有以下邏輯應用角色/許可權:

    * [邏輯應用操作員](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-operator)許可權是必需的或邏輯應用讀取/觸發存取(此角色無法建立或編輯邏輯應用;僅*運行*現有應用)

    * [邏輯應用程式的權](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-contributor)限為邏輯應用建立與修改所必需的

* 如果要使用邏輯應用連接器,可能需要其他憑據才能登錄到其各自的服務(例如,Outlook/Teams/Slack 實例)


## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>建立邏輯應用程式並定義何時自動執行 

1. 從安全中心的側邊欄中,選擇**工作流自動化**。

    [![工作流自動化清單](media/workflow-automation/list-of-workflow-automations.png)](media/workflow-automation/list-of-workflow-automations.png#lightbox)

    在此頁面中,您可以創建新的自動化規則,以及啟用、禁用或刪除現有規則。  
1. 要定義新工作流,請按一下「**添加工作流自動化**」。。 

    將顯示一個窗格,其中帶有新自動化的選項。 您可以輸入:
    1. 自動化的名稱和說明。
    1. 將啟動此自動工作流的觸發器。 例如,您可能希望邏輯應用在生成包含「SQL」的安全警報時執行。
    1. 滿足觸發器條件時將運行的邏輯應用。 

        [![工作流自動化清單](media/workflow-automation/add-workflow.png)](media/workflow-automation/add-workflow.png#lightbox)

1. 在「操作」部分中,按一下「**創建新操作**」以開始邏輯應用創建過程。

    您將被帶往 Azure 邏輯應用。

    [![建立新的邏輯應用程式](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. 輸入名稱、資源組和位置,然後按一下"**創建**"。

1. 在新的邏輯應用中,您可以從安全類別中的內置預定義範本中進行選擇。 或者,您可以定義觸發此過程時要發生的事件的自定義流。

    在邏輯應用設計器中,支援安全中心連接器中的以下觸發器:

    * **建立或觸發 Azure 安全中心建議時**
    * **建立或觸發 Azure 安全中心警報時** 
    
    > [!TIP]
    > 您可以自定義觸發器,使其僅與具有您感興趣的嚴重性級別的警報相關聯。
    
    > [!NOTE]
    > 如果使用舊觸發器「觸發對 Azure 安全中心警報的回應」,則工作流自動化功能不會啟動邏輯應用。 而是使用上述任一觸發器。 

    [![範例邏輯應用](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. 定義邏輯應用後,返回到工作流自動化定義窗格("添加工作流自動化")。 按下 **「刷新**」以確保新的邏輯應用可供選擇。

    ![Refresh](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. 選擇邏輯應用並保存自動化。 請注意,邏輯應用下拉清單僅顯示具有上述支援安全中心連接器的邏輯應用。


## <a name="manually-trigger-a-logic-app"></a>手動觸發邏輯應用

您還可以在查看安全警報或任何提供[快速修復修正](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#quick-fix-remediation)的建議時手動運行邏輯應用。

要手動執行邏輯應用,請打開警報或支援快速修復修正的建議,然後按**下 「觸發邏輯應用**」 :

[![手動觸發邏輯應用](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)

## <a name="data-types-schemas"></a>資料類型架構

要檢視傳遞給邏輯應用程式的安全警報或建議事件的原始事件架構,請存[取工作流程自動化資料類型架構](https://aka.ms/ASCAutomationSchemas)。 如果您不使用安全中心的內建邏輯應用連接器上述,而是使用邏輯應用的通用 HTTP 連接器,則此功能非常有用 - 您可以使用事件 JSON 架構在您認為合適的情況下手動分析它。

## <a name="next-steps"></a>後續步驟

在本文中,您瞭解了創建邏輯應用、在安全中心中自動執行它們以及手動運行它們。 

有關其他相關材料,請參閱: 

- [關於如何使用工作流自動化自動執行安全回應的 Microsoft 學習模組](https://docs.microsoft.com/learn/modules/resolve-threats-with-azure-security-center/)
- [Azure 資訊安全中心的安全性建議](security-center-recommendations.md)
- [Azure 資訊安全中心的安全性警示](security-center-alerts-overview.md)
- [關於 Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)
- [Logic Apps 連接器](https://docs.microsoft.com/connectors/)
- [工作流程自動化資料類型架構](https://aka.ms/ASCAutomationSchemas)
