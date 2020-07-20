---
title: 交易夥伴協定
description: 使用 Azure Logic Apps 和企業整合套件來建立和管理交易夥伴之間的協定
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: c8cbfb619c9eed325161503f705bf5c4c0746265
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82612328"
---
# <a name="create-and-manage-trading-partner-agreements-in-azure-logic-apps"></a>在 Azure Logic Apps 中建立和管理交易夥伴協議

[交易夥伴](../logic-apps/logic-apps-enterprise-integration-partners.md)  
 *協定*可透過定義要在交換企業對企業（B2B）訊息時使用的特定產業標準通訊協定，協助組織和企業彼此緊密通訊。 協定提供了常見的優點，例如：

* 讓組織能夠使用已知的格式來交換資訊。
* 提升執行 B2B 交易時的效率。
* 可以輕鬆地建立、管理和使用，以建立企業整合解決方案。

本文說明如何建立 AS2、EDIFACT 或 X12 合約，您可以在使用[企業整合套件](../logic-apps/logic-apps-enterprise-integration-overview.md)和[Azure Logic Apps](../logic-apps/logic-apps-overview.md)建立 B2B 案例的企業整合解決方案時使用。 建立合約之後，您就可以使用 AS2、EDIFACT 或 X12 連接器來交換 B2B 訊息。

若要建立交換 RosettaNet 訊息的協定，請參閱[Exchange rosettanet 訊息](../logic-apps/logic-apps-enterprise-integration-rosettanet.md)。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 用於儲存合約和其他 B2B 成品的[整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 此整合帳戶必須與您的 Azure 訂用帳戶相關聯。

* 至少有兩個您已在整合帳戶中建立的[交易夥伴](../logic-apps/logic-apps-enterprise-integration-partners.md)。 合約需要主機夥伴和來賓夥伴。 這兩個夥伴都必須使用與您想要建立的合約相同的「商務身分識別」辨識符號，例如 AS2、X12 或 EDIFACT。

* 選擇性：您要在其中使用合約的邏輯應用程式，以及啟動邏輯應用程式工作流程的觸發程式。 若只要建立整合帳戶和 B2B 構件，您就不需要邏輯應用程式。 不過，在邏輯應用程式可以使用整合帳戶中的 B2B 成品之前，您必須先將整合帳戶連結至邏輯應用程式。 如果您不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps](../logic-apps/logic-apps-overview.md) 和[快速入門：建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-agreements"></a>建立合約

1. 登入 [Azure 入口網站](https://portal.azure.com)。
在主要 Azure 功能表上，選取 [所有服務]****。 在搜尋方塊中，輸入「整合」作為篩選準則。 從結果中，選取 [此資源：**整合帳戶**]

   ![尋找您的整合帳戶](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. 在 [**整合帳戶**] 底下，選取您要在其中建立合約的整合帳戶。

   ![選取您要在其中建立合約的整合帳戶](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. 在右窗格的 [**元件**] 底下，選擇 [合約 **] 圖格**。

   ![選擇 [合約]](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. 在 [合約]**** 之下，選擇 [新增]****。 在 [**新增**] 窗格中，提供有關您合約的資訊，例如：

   ![選擇 [新增]](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | 屬性 | 必要 | 值 | 描述 |
   |----------|----------|-------|-------------|
   | **名稱** | 是 | <*合約-名稱*> | 合約的名稱 |
   | **合約類型** | 是 | **AS2**、 **X12**或**EDIFACT** | 合約的通訊協定類型。 當您建立合約檔案時，該檔案中的內容必須與合約類型相符。 | |  
   | **主控夥伴** | 是 | <*主機-夥伴名稱*> | 主機合作夥伴代表指定合約的組織 |
   | **主控身分識別** | 是 | <*主機-夥伴識別碼*> | 主機合作夥伴的識別碼 |
   | **來賓夥伴** | 是 | <*來賓-夥伴名稱*> | 來賓合作夥伴代表與主機合作夥伴有生意往來的組織 |
   | **來賓身分識別** | 是 | <*來賓-合作夥伴識別碼*> | 來賓夥伴的識別碼 |
   | **接收設定** | 不定 | 不定 | 這些屬性會指定主機夥伴如何從合約中的來賓夥伴接收所有傳入訊息。 如需詳細資訊，請參閱個別的合約類型： <p>- [AS2 訊息設定](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT 訊息設定](logic-apps-enterprise-integration-edifact.md) <br>- [X12 訊息設定](logic-apps-enterprise-integration-x12.md) |
   | **傳送設定** | 不定 | 不定 | 這些屬性會指定主機夥伴如何將所有外寄訊息傳送給合約中的來賓夥伴。 如需詳細資訊，請參閱個別的合約類型： <p>- [AS2 訊息設定](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT 訊息設定](logic-apps-enterprise-integration-edifact.md) <br>- [X12 訊息設定](logic-apps-enterprise-integration-x12.md) |
   |||||

   > [!IMPORTANT]
   > 合約的解決方式取決於符合夥伴和傳入訊息中定義的這些專案：
   >
   > * 寄件者的辨識符號和識別碼
   > * 接收者的辨識符號和識別碼
   >
   > 如果您的合作夥伴變更這些值，請確定您也更新了合約。

1. 當您完成合約的建立時，請在 [**新增**] 頁面上選擇 [**確定]**，然後返回您的整合帳戶。

   [**合約**] 清單現在會顯示您的新協定。

## <a name="edit-agreements"></a>編輯協定

1. 在 [ [Azure 入口網站](https://portal.azure.com)] 的主要 Azure 功能表上，選取 [**所有服務**]。

1. 在搜尋方塊中，輸入「整合」作為篩選準則。 從結果中，選取 [此資源：**整合帳戶**]

1. 在 [**整合帳戶**] 底下，選取具有您要編輯之協定的整合帳戶。

1. 在右窗格的 [**元件**] 底下，選擇 [合約 **] 圖格**。

1. 在 [**協定**] 底下選取您的合約，然後選擇 [**編輯**]。

1. 建立並儲存您的變更。

## <a name="delete-agreements"></a>刪除協定

1. 在 [ [Azure 入口網站](https://portal.azure.com)] 的主要 Azure 功能表上，選取 [**所有服務**]。

1. 在搜尋方塊中，輸入「整合」作為篩選準則。 從結果中，選取 [此資源：**整合帳戶**]

1. 在 [**整合帳戶**] 底下，選取具有您想要刪除之協定的整合帳戶。

1. 在右窗格的 [**元件**] 底下，選擇 [合約 **] 圖格**。

1. 在 [**協定**] 底下選取您的合約，然後選擇 [**刪除**]。

1. 確認您要刪除選取的合約。

## <a name="next-steps"></a>後續步驟

* [交換 AS2 訊息](logic-apps-enterprise-integration-as2.md)
* [交換 EDIFACT 訊息](logic-apps-enterprise-integration-edifact.md)
* [交換 X12 訊息](logic-apps-enterprise-integration-x12.md)
