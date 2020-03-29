---
title: 交易夥伴協定
description: 使用 Azure 邏輯應用和企業集成包創建和管理交易夥伴之間的協定
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 521a0ef4053be55e6c7322da5af26ccfc6c844e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790734"
---
# <a name="create-and-manage-trading-partner-agreements-in-azure-logic-apps"></a>在 Azure 邏輯應用中創建和管理交易夥伴協定

[交易夥伴](../logic-apps/logic-apps-enterprise-integration-partners.md) 
*協定*通過定義在交換企業對企業 （B2B） 消息時要使用的特定行業標準協定，説明組織和企業無縫地通信。 協定提供共同的好處，例如：

* 使組織能夠使用已知的格式交換資訊。
* 在進行 B2B 交易時提高效率。
* 易於創建、管理和使用，用於構建企業集成解決方案。

本文演示如何創建 AS2、EDIFACT 或 X12 協定，在使用[企業集成包](../logic-apps/logic-apps-enterprise-integration-overview.md)和[Azure 邏輯應用](../logic-apps/logic-apps-overview.md)為 B2B 方案構建企業集成解決方案時可以使用該協定。 創建協定後，可以使用 AS2、EDIFACT 或 X12 連接器交換 B2B 消息。

要創建交換羅塞塔網消息的協定，請參閱[交換羅塞塔網消息](../logic-apps/logic-apps-enterprise-integration-rosettanet.md)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 用於存儲協定和其他 B2B 工件的[集成帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 此集成帳戶必須與 Azure 訂閱關聯。

* 您已在集成帳戶中創建的至少兩個[交易夥伴](../logic-apps/logic-apps-enterprise-integration-partners.md)。 協定需要主機合作夥伴和來賓合作夥伴。 兩個合作夥伴必須使用與要創建的協定相同的"業務標識"限定詞，例如 AS2、X12 或 EDIFACT。

* 可選：要使用協定的邏輯應用和啟動邏輯應用工作流的觸發器。 只需創建集成帳戶和 B2B 專案，就不需要邏輯應用。 但是，在邏輯應用可以在集成帳戶中使用 B2B 專案之前，必須將集成帳戶連結到邏輯應用。 如果您還不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps？](../logic-apps/logic-apps-overview.md)和[快速入門：建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-agreements"></a>建立合約

1. 登錄到 Azure[門戶](https://portal.azure.com)。
在主要 Azure 功能表上，選取 [所有服務]****。 在搜索框中，輸入"集成"作為篩選器。 從結果中選擇此資源：**集成帳戶**

   ![尋找您的整合帳戶](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. 在 **"集成帳戶**"下，選擇要創建協定的集成帳戶。

   ![選取您要在其中建立合約的整合帳戶](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. 在右側窗格中，在 **"元件"** 下，選擇"**協定**"磁貼。

   ![選擇"協定"](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. 在 [合約]**** 之下，選擇 [新增]****。 在 **"添加"** 窗格中，提供有關協定的資訊，例如：

   ![選擇 [新增]](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | 屬性 | 必要 | 值 | 描述 |
   |----------|----------|-------|-------------|
   | **名稱** | 是 | <*協定名稱*> | 協定名稱 |
   | **協定類型** | 是 | **AS2** **、X12**或**EDIFACT** | 協定的協定類型。 創建協定檔時，該檔中的內容必須與協定類型匹配。 | |  
   | **主控夥伴** | 是 | <*主機合作夥伴名稱*> | 主機合作夥伴表示指定協定的組織 |
   | **主控身分識別** | 是 | <*主機合作夥伴識別碼*> | 主機合作夥伴的識別碼 |
   | **來賓夥伴** | 是 | <*客人-合作夥伴名稱*> | 來賓合作夥伴代表與主機合作夥伴有生意往來的組織 |
   | **來賓身分識別** | 是 | <*來賓合作夥伴識別碼*> | 來賓合作夥伴的識別碼 |
   | **接收設定** | 不定 | 不定 | 這些屬性指定主機合作夥伴如何接收來自協定中來賓合作夥伴的所有傳入消息。 有關詳細資訊，請參閱相應的協定類型： <p>- [AS2 消息設置](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT 消息設置](logic-apps-enterprise-integration-edifact.md) <br>- [X12 消息設置](logic-apps-enterprise-integration-x12.md) |
   | **傳送設定** | 不定 | 不定 | 這些屬性指定主機合作夥伴如何向協定中的來賓合作夥伴發送所有傳出消息。 有關詳細資訊，請參閱相應的協定類型： <p>- [AS2 消息設置](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT 消息設置](logic-apps-enterprise-integration-edifact.md) <br>- [X12 消息設置](logic-apps-enterprise-integration-x12.md) |
   |||||

1. 創建完協定後，在 **"添加**"頁上，選擇 **"確定**"並返回到集成帳戶。

   "**協定**"清單現在顯示您的新協定。

## <a name="edit-agreements"></a>編輯協定

1. 在[Azure 門戶](https://portal.azure.com)中，在主 Azure 功能表上，選擇 **"所有服務**"。

1. 在搜索框中，輸入"集成"作為篩選器。 從結果中選擇此資源：**集成帳戶**

1. 在 **"集成帳戶**"下，選擇具有要編輯的協定的集成帳戶。

1. 在右側窗格中，在 **"元件"** 下，選擇"**協定**"磁貼。

1. 在 **"協定**"下，選擇您的協定，然後選擇 **"編輯**"。

1. 進行更改，然後保存更改。

## <a name="delete-agreements"></a>刪除協定

1. 在[Azure 門戶](https://portal.azure.com)中，在主 Azure 功能表上，選擇 **"所有服務**"。

1. 在搜索框中，輸入"集成"作為篩選器。 從結果中選擇此資源：**集成帳戶**

1. 在 **"集成帳戶**"下，選擇具有要刪除的協定的集成帳戶。

1. 在右側窗格中，在 **"元件"** 下，選擇"**協定**"磁貼。

1. 在 **"協定**"下，選擇您的協定，然後選擇 **"刪除**"。

1. 確認您要刪除選取的合約。

## <a name="next-steps"></a>後續步驟

* [交換 AS2 訊息](logic-apps-enterprise-integration-as2.md)
* [交換 EDIFACT 訊息](logic-apps-enterprise-integration-edifact.md)
* [交換 X12 訊息](logic-apps-enterprise-integration-x12.md)
