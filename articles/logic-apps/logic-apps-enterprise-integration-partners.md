---
title: 為 B2B 集成添加交易夥伴
description: 在集成帳戶中創建交易夥伴，以便與 Azure 邏輯應用一起使用
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: e58cbe85f30ea09adde45d55bb7b80c710c45495
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792435"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>將交易夥伴添加到 Azure 邏輯應用的集成帳戶

在[Azure 邏輯應用中](../logic-apps/logic-apps-overview.md)，可以使用邏輯應用的[集成帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)創建自動企業對企業 （B2B） 集成工作流。 為了表示您的組織和其他人，您可以創建交易夥伴並將其添加為集成帳戶中的工件。 合作夥伴是參與 B2B 事務並相互交換消息的實體。

在創建這些合作夥伴之前，請確保與合作夥伴討論和共用有關如何識別和驗證對方發送的消息的資訊。 就這些詳細資訊達成一致後，您就可以在集成帳戶中創建合作夥伴了。

## <a name="partner-roles-in-integration-accounts"></a>集成帳戶中的合作夥伴角色

要定義與合作夥伴交換的消息的詳細資訊，請創建[協定](../logic-apps/logic-apps-enterprise-integration-agreements.md)並將其添加為集成帳戶中的專案。 協定需要集成帳戶中至少兩個合作夥伴。 您的組織始終是協定中的*主機合作夥伴*。 與您的組織交換消息的組織是*來賓合作夥伴*。 客戶合作夥伴可以是另一家公司，甚至可以是您自己組織中的部門。 在您新增這些合作夥伴之後，您可以建立合約。

在協定中，從主機合作夥伴的角度指定處理傳入和傳出消息的詳細資訊。 對於傳入郵件，"**接收設置"** 指定主機合作夥伴如何接收協定中的來賓合作夥伴的消息。 對於傳出郵件，"**發送設置"** 指定主機合作夥伴如何向來賓合作夥伴發送消息。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 用於存儲合作夥伴、協定和其他 B2B 工件的[集成帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 此集成帳戶必須與 Azure 訂閱關聯。

## <a name="create-partner"></a>建立合作夥伴

1. 登錄到 Azure[門戶](https://portal.azure.com)。

1. 在主要 Azure 功能表上，選取 [所有服務]****。 在搜索框中，輸入"集成"，然後選擇 **"集成帳戶**"。

   ![選擇"集成帳戶"](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. 在 [整合帳戶]**** 底下，選取要將合作夥伴新增到其中的整合帳戶。

   ![選取企業整合帳戶](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. 選擇 [合作夥伴]**** 圖格。

   ![選擇「合作夥伴」圖格](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. 在 [合作夥伴]**** 底下，選擇 [新增]****。 在 **"添加合作夥伴**"下，提供下表所述合作夥伴的詳細資訊。

   ![選擇"添加"並提供合作夥伴詳細資訊](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | 屬性 | 必要 | 描述 |
   |----------|----------|-------------|
   | **名稱** | 是 | 合作夥伴姓名 |
   | **Qualifier** | 是 | 向組織提供唯一業務標識的身份驗證機構，例如**D-U-N-S（Dun & Bradstreet）。** <p>合作夥伴可以選擇相互定義的業務標識。 對於這些方案，選擇用於 EDIFACT**的相互定義**或**X12 的互定義 （X12）。** <p>對於羅塞塔網，僅選擇**DUNS**，這是標準。 |
   | **價值** | 是 | 標識邏輯應用接收的文檔的值。 <p>對於羅塞塔網，此值必須是對應于 DUNS 編號的九位數位。 |
   ||||

   > [!NOTE]
   > 對於使用羅塞塔網的合作夥伴，可以通過首先創建這些合作夥伴，然後[編輯它們](#edit-partner)來指定其他資訊。

1. 完成時，選擇 [確定]****。

   您的新合作夥伴現在將顯示在**合作夥伴**清單中。 此外，"**合作夥伴"** 磁貼更新當前合作夥伴數。

   ![新合作夥伴](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>編輯合作夥伴

1. 在 [Azure 入口網站](https://portal.azure.com)中，尋找並選取整合帳戶。
選擇 [合作夥伴]**** 圖格。

   ![選擇「合作夥伴」圖格](./media/logic-apps-enterprise-integration-partners/edit.png)

1. 在 **"合作夥伴**"下，選擇要編輯的合作夥伴，然後選擇 **"編輯**"。 在 **"編輯"** 下，進行更改。

   ![進行變更並儲存](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   對於羅塞塔網，在**羅塞塔網合作夥伴屬性**下，您可以指定以下附加資訊：

   | 屬性 | 必要 | 描述 |
   |----------|----------|-------------|
   | **夥伴分類** | 否 | 合作夥伴的組織類型 |
   | **供應鏈代碼** | 否 | 合作夥伴的供應鏈代碼，例如"資訊技術"或"電子元件" |
   | **連絡人姓名** | 否 | 合作夥伴的連絡人姓名 |
   | **電子郵件** | 否 | 合作夥伴的電子郵件地址 |
   | **傳真** | 否 | 合作夥伴的傳真號碼 |
   | **電話** | 否 | 合作夥伴的電話號碼 |
   ||||

1. 完成後，選擇 **"確定"** 以保存更改。

## <a name="delete-partner"></a>刪除合作夥伴

1. 在 [Azure 入口網站](https://portal.azure.com)中，尋找並選取整合帳戶。 選擇 [合作夥伴]**** 圖格。

   ![選擇「合作夥伴」圖格](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. 在 [合作夥伴]**** 底下，選取您要刪除的合作夥伴。 選擇 **"刪除**"。

   ![刪除合作夥伴](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>後續步驟

* 瞭解有關協定的更多[內容](../logic-apps/logic-apps-enterprise-integration-agreements.md)