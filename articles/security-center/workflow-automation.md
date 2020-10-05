---
title: Azure 資訊安全中心中的工作流程自動化 |Microsoft Docs
description: 瞭解如何在 Azure 資訊安全中心中建立和自動化工作流程
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: b713977d811411ea2ccd7dfa22c7757321ecd7aa
ms.sourcegitcommit: 5b69ba21787c07547edfbfd5254eaf34315cfadd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91712284"
---
# <a name="create-automatic-responses-to-alerts-and-recommendations-with-workflow-automation"></a>使用工作流程自動化來建立警示和建議的自動回應

每個安全性程式都包含事件回應的多個工作流程。 這些流程可能包括通知相關的專案利害關係人、啟動變更管理程式，以及套用特定的補救步驟。 安全性專家建議您盡可能自動化這些程式的許多步驟。 自動化可減少額外負荷。 它也可以確保程式步驟能以一致的方式執行，並根據預先定義的需求，來改善您的安全性。

本文描述 Azure 資訊安全中心的工作流程自動化功能。 這項功能可以觸發安全性警示和建議的 Logic Apps。 例如，您可能想要在警示發生時，讓安全性中心以電子郵件傳送特定的使用者。 您也將瞭解如何使用 [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)來建立 Logic Apps。

> [!NOTE]
> 如果您先前已在提要欄位上使用工作手冊 (預覽) 視圖，您將會在 [新工作流程自動化] 頁面中找到相同的功能以及展開的功能。



## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|版本狀態：|正式上市 (GA)|
|定價：|免費|
|必要的角色和權限：|資源群組的**安全性系統管理員角色**或**擁有**者<br>也必須具有目標資源的寫入權限<br><br>若要使用 Azure Logic Apps 工作流程，您也必須具有下列 Logic Apps 角色/許可權：<br> - [邏輯應用程式操作員](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-operator) 許可權是必要的或邏輯應用程式讀取/觸發存取 (此角色無法建立或編輯邏輯應用程式;只 *執行* 現有的) <br> - 邏輯應用程式建立和修改需要[邏輯應用程式參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-contributor)許可權<br>如果您想要使用邏輯應用程式連接器，您可能需要額外的認證來登入其各自的服務 (例如，您的 Outlook/團隊/時差實例) |
|雲端：|![是](./media/icons/yes-icon.png) 商業雲端<br>![是](./media/icons/yes-icon.png) 國家/地區/主權 (US Gov、中國 Gov、其他 Gov)|
|||



## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>建立邏輯應用程式，並定義應自動執行的時機 

1. 從 [安全性中心] 提要欄位，選取 [ **工作流程自動化**]。

    :::image type="content" source="./media/workflow-automation/list-of-workflow-automations.png" alt-text="工作流程自動化清單&quot;:::

    從這個頁面，您可以建立新的自動化規則，以及啟用、停用或刪除現有的規則。

1. 若要定義新的工作流程，請按一下 [ **新增工作流程自動化**]。 

    窗格隨即出現，並提供新自動化的選項。 您可以在這裡輸入：
    1. 自動化的名稱和描述。
    1. 將起始此自動工作流程的觸發程式。 例如，您可能想要在產生包含 &quot;SQL" 的安全性警示時執行邏輯應用程式。
    1. 符合觸發條件時將執行的邏輯應用程式。 

        :::image type="content" source="./media/workflow-automation/add-workflow.png" alt-text="工作流程自動化清單&quot;:::

    從這個頁面，您可以建立新的自動化規則，以及啟用、停用或刪除現有的規則。

1. 若要定義新的工作流程，請按一下 [ **新增工作流程自動化**]。 

    窗格隨即出現，並提供新自動化的選項。 您可以在這裡輸入：
    1. 自動化的名稱和描述。
    1. 將起始此自動工作流程的觸發程式。 例如，您可能想要在產生包含 &quot;SQL":::

1. 從 [動作] 區段中，按一下 [ **建立新** 的] 以開始建立邏輯應用程式。

    您將會進入 Azure Logic Apps。

    [![建立新的邏輯應用程式](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. 輸入名稱、資源群組和位置，然後按一下 [ **建立**]。

1. 在新的邏輯應用程式中，您可以從 [安全性] 類別中的內建預先定義範本中進行選擇。 或者，您可以定義觸發此進程時，所要發生的自訂事件流程。

    在邏輯應用程式設計工具中，支援下列來自安全性中心連接器的觸發程式：

    * **建立或觸發 Azure 資訊安全中心建議時** -如果您的邏輯應用程式依賴已淘汰或已取代的建議，則您的自動化將會停止運作，而您將需要更新觸發程式。 若要追蹤建議的變更，請參閱 [Azure 資訊安全中心版本](release-notes.md)資訊。

    * **當 Azure 資訊安全中心警示建立或觸發時** ，您可以自訂觸發程式，使其只與您感興趣的嚴重性層級的警示相關聯。
    
    > [!NOTE]
    > 如果您使用的是舊版觸發程式「當觸發 Azure 資訊安全中心警示的回應」時，您的邏輯應用程式將不會由工作流程自動化功能啟動。 相反地，請使用上述任一觸發程式。 

    [![範例邏輯應用程式](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. 定義邏輯應用程式之後，請返回工作流程自動化定義窗格 ( [新增工作流程自動化] ) 。 按一下 **[** 重新整理] 以確保您的新邏輯應用程式可供選取。

    ![重新整理](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. 選取您的邏輯應用程式，並儲存自動化。 請注意，[邏輯應用程式] 下拉式清單只會顯示 Logic Apps 上面所述的支援安全中心連接器。


## <a name="manually-trigger-a-logic-app"></a>手動觸發邏輯應用程式

您也可以在流覽任何安全性警示或建議時，手動執行 Logic Apps。

若要手動執行邏輯應用程式，請開啟警示或建議，然後按一下 [ **觸發邏輯應用程式**：

[![手動觸發邏輯應用程式](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)

## <a name="data-types-schemas"></a>資料類型架構

若要查看傳遞給邏輯應用程式實例之安全性警示或建議事件的原始事件架構，請造訪 [工作流程自動化資料類型架構](https://aka.ms/ASCAutomationSchemas)。 如果您未使用上述的「安全性中心」內建邏輯應用程式連接器，但改為使用邏輯應用程式的一般 HTTP 連接器，這項功能就很有用。您可以使用事件 JSON 架構，視需要手動進行剖析。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何建立 Logic Apps、在資訊安全中心自動執行，以及手動執行它們。 

如需相關材質，請參閱： 

- [如何使用工作流程自動化將安全性回應自動化的 Microsoft Learn 課程模組](https://docs.microsoft.com/learn/modules/resolve-threats-with-azure-security-center/)
- [Azure 資訊安全中心的安全性建議](security-center-recommendations.md)
- [Azure 資訊安全中心的安全性警示](security-center-alerts-overview.md)
- [關於 Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)
- [Logic Apps 連接器](https://docs.microsoft.com/connectors/)
- [工作流程自動化資料類型架構](https://aka.ms/ASCAutomationSchemas)
