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
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95992938"
---
# <a name="create-and-manage-trading-partner-agreements-in-azure-logic-apps"></a>在 Azure Logic Apps 中建立和管理交易夥伴協議

[交易夥伴](../logic-apps/logic-apps-enterprise-integration-partners.md)  
 *協定* 可協助組織和企業彼此緊密通訊，方法是定義在將企業對企業 (B2B) 訊息交換時所要使用的特定產業標準通訊協定。 合約提供了常見的優點，例如：

* 讓組織可以使用已知的格式來交換資訊。
* 改善執行 B2B 交易時的效率。
* 可輕鬆地建立、管理及使用，以建立企業整合解決方案。

本文說明如何建立 AS2、EDIFACT 或 X12 合約，讓您可以在使用 [企業整合套件](../logic-apps/logic-apps-enterprise-integration-overview.md) 和 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)建立 B2B 案例的企業整合解決方案時使用。 建立合約之後，您就可以使用 AS2、EDIFACT 或 X12 連接器來交換 B2B 訊息。

若要建立交換 RosettaNet 訊息的協定，請參閱 [Exchange rosettanet 訊息](../logic-apps/logic-apps-enterprise-integration-rosettanet.md)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 用於儲存合約和其他 B2B 構件的 [整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 。 此整合帳戶必須與您的 Azure 訂用帳戶相關聯。

* 您已在整合帳戶中建立至少兩個 [交易夥伴](../logic-apps/logic-apps-enterprise-integration-partners.md) 。 合約需要主機夥伴和來賓合作夥伴。 這兩個夥伴都必須使用與您想要建立之協定（例如 AS2、X12 或 EDIFACT）相同的「商務識別」限定詞。

* 選擇性：您要使用協定的邏輯應用程式，以及啟動邏輯應用程式工作流程的觸發程式。 若只要建立您的整合帳戶和 B2B 成品，就不需要邏輯應用程式。 不過，您必須先將整合帳戶連結至邏輯應用程式，您的邏輯應用程式才能使用您整合帳戶中的 B2B 成品。 如果您不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps](../logic-apps/logic-apps-overview.md) 和[快速入門：建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-agreements"></a>建立合約

1. 登入 [Azure 入口網站](https://portal.azure.com)。
在主要 Azure 功能表上，選取 [所有服務]。 在搜尋方塊中，輸入「整合」作為篩選準則。 從結果中，選取 [此資源：**整合帳戶**]

   ![尋找您的整合帳戶](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. 在 [ **整合帳戶**] 底下，選取您要在其中建立合約的整合帳戶。

   ![選取您要在其中建立合約的整合帳戶](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. 在右側窗格的 [ **元件**] 底下，選擇 [合約 **] 圖格** 。

   ![選擇 [合約]](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. 在 [合約] 之下，選擇 [新增]。 在 [ **新增** ] 窗格中，提供合約的相關資訊，例如：

   ![選擇 [新增]](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | 屬性 | 必要 | 值 | 描述 |
   |----------|----------|-------|-------------|
   | **名稱** | 是 | <*合約-名稱*> | 合約的名稱 |
   | **合約類型** | 是 | **AS2**、 **X12** 或 **EDIFACT** | 合約的通訊協定類型。 當您建立合約檔案時，該檔案中的內容必須與合約類型相符。 | |  
   | **主控夥伴** | 是 | <*主機夥伴名稱*> | 主機夥伴代表指定合約的組織 |
   | **主控身分識別** | 是 | <*主機夥伴識別碼*> | 主機夥伴的識別碼 |
   | **來賓夥伴** | 是 | <*來賓-夥伴名稱*> | 來賓合作夥伴代表與主機合作夥伴有生意往來的組織 |
   | **來賓身分識別** | 是 | <*來賓-合作夥伴識別碼*> | 來賓夥伴的識別碼 |
   | **接收設定** | 不定 | 不定 | 這些屬性會指定主機夥伴如何接收合約中來賓合作夥伴的所有傳入訊息。 如需詳細資訊，請參閱個別的合約類型： <p>- [AS2 訊息設定](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT 訊息設定](logic-apps-enterprise-integration-edifact.md) <br>- [X12 訊息設定](logic-apps-enterprise-integration-x12.md) |
   | **傳送設定** | 不定 | 不定 | 這些屬性會指定主機夥伴如何將所有外寄訊息傳送給合約中的來賓夥伴。 如需詳細資訊，請參閱個別的合約類型： <p>- [AS2 訊息設定](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT 訊息設定](logic-apps-enterprise-integration-edifact.md) <br>- [X12 訊息設定](logic-apps-enterprise-integration-x12.md) |
   |||||

   > [!IMPORTANT]
   > 協定的解決方式取決於符合夥伴和內送訊息中定義的這些專案：
   >
   > * 寄件者的辨識符號和識別碼
   > * 接收者的辨識符號和識別碼
   >
   > 如果您的夥伴變更這些值，請確定您也更新合約。

1. 當您完成合約的建立時，請在 [ **新增** ] 頁面上選擇 **[確定]**，然後返回您的整合帳戶。

   合約 **清單現在** 會顯示您的新合約。

## <a name="edit-agreements"></a>編輯協定

1. 在 [Azure 入口網站](https://portal.azure.com)的主要 Azure 功能表上，選取 [ **所有服務**]。

1. 在搜尋方塊中，輸入「整合」作為篩選準則。 從結果中，選取 [此資源：**整合帳戶**]

1. 在 [ **整合帳戶**] 底下，選取具有您要編輯之合約的整合帳戶。

1. 在右側窗格的 [ **元件**] 底下，選擇 [合約 **] 圖格** 。

1. 在 [ **協定**] 下，選取您的合約，然後選擇 [ **編輯**]。

1. 進行變更，然後儲存您的變更。

## <a name="delete-agreements"></a>刪除協定

1. 在 [Azure 入口網站](https://portal.azure.com)的主要 Azure 功能表上，選取 [ **所有服務**]。

1. 在搜尋方塊中，輸入「整合」作為篩選準則。 從結果中，選取 [此資源：**整合帳戶**]

1. 在 [ **整合帳戶**] 底下，選取具有您要刪除之合約的整合帳戶。

1. 在右側窗格的 [ **元件**] 底下，選擇 [合約 **] 圖格** 。

1. 在 [ **協定**] 下，選取您的合約，然後選擇 [ **刪除**]。

1. 確認您要刪除選取的合約。

## <a name="next-steps"></a>後續步驟

* [交換 AS2 訊息](logic-apps-enterprise-integration-as2.md)
* [交換 EDIFACT 訊息](logic-apps-enterprise-integration-edifact.md)
* [交換 X12 訊息](logic-apps-enterprise-integration-x12.md)
