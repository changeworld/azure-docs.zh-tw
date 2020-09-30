---
title: 為 B2B 整合新增交易夥伴
description: 在整合帳戶中建立交易夥伴以搭配 Azure Logic Apps 使用
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 8e3805fae5bf6cc5ad8cf759d3ba75220c6ddbd8
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91565066"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>將合作對象新增至 Azure Logic Apps 的整合帳戶

在 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)中，您可以將 [整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 與邏輯應用程式搭配使用，以建立自動化的企業對企業 (B2B) 整合工作流程。 若要代表您的組織和其他人，您可以建立交易夥伴，並將其新增為您整合帳戶的構件。 合作夥伴是參與 B2B 交易及在彼此之間交換訊息的實體。

在建立這些夥伴之前，請務必與您的合作夥伴討論和分享有關如何識別及驗證其他傳送之訊息的資訊。 在您同意這些詳細資料之後，您就可以在整合帳戶中建立合作夥伴。

## <a name="partner-roles-in-integration-accounts"></a>整合帳戶中的夥伴角色

若要定義與您的夥伴交換之訊息的詳細資料，您可以建立合約，並將 [其新增為](../logic-apps/logic-apps-enterprise-integration-agreements.md) 您的整合帳戶的構件。 合約要求您的整合帳戶中至少要有兩個夥伴。 您的組織一律是合約中的 *主機夥伴* 。 與您的組織交換訊息的組織是 *來賓合作夥伴*。 來賓夥伴可以是另一家公司，或甚至是您自己組織中的部門。 在您新增這些合作夥伴之後，您可以建立合約。

在協定中，您可以指定從主機夥伴的觀點來處理傳入和傳出訊息的詳細資料。 對於內送訊息， **接收設定會** 指定主機夥伴如何從合約中的來賓夥伴接收訊息。 針對外寄訊息， **傳送設定** 會指定主機夥伴如何將訊息傳送給來賓夥伴。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 用於儲存您的夥伴、協定和其他 B2B 成品的 [整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 。 此整合帳戶必須與您的 Azure 訂用帳戶相關聯。

## <a name="create-partner"></a>建立合作夥伴

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在主要 Azure 功能表上，選取 [所有服務]****。 在搜尋方塊中，輸入「整合」，然後選取 [ **整合帳戶**]。

   ![選取 [整合帳戶]](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. 在 [整合帳戶]**** 底下，選取要將合作夥伴新增到其中的整合帳戶。

   ![選取企業整合帳戶](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. 選擇 [合作夥伴]**** 圖格。

   ![顯示 [合作夥伴] 磚的螢幕擷取畫面。](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. 在 [合作夥伴]**** 底下，選擇 [新增]****。 在 [ **新增夥伴**] 下，提供夥伴的詳細資料，如下表所述。

   ![選擇 [新增] 並提供合作夥伴詳細資料](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | 屬性 | 必要 | 描述 |
   |----------|----------|-------------|
   | **名稱** | 是 | 夥伴的名稱 |
   | **Qualifier** | 是 | 為組織提供獨特商務身分識別的驗證本文，例如， **D-U-N-S (Dun & Bradstreet) **。 <p>合作夥伴可以選擇一個相互定義的商務識別。 在這些情況下，請選取 [針對 EDIFACT 進行 **互斥** ] 或 [針對 X12 ** (x12) 相互 ** 定義]。 <p>針對 RosettaNet，只選取 [ **DUNS**]，這是標準的。 |
   | **ReplTest1** | 是 | 識別您的邏輯應用程式所收到之檔的值。 <p>對於 RosettaNet，此值必須是對應至 DUNS 號碼的九位數數位。 |
   ||||

   > [!NOTE]
   > 針對使用 RosettaNet 的合作夥伴，您可以先建立這些夥伴，然後稍後再 [進行編輯](#edit-partner)，以指定其他資訊。

1. 完成時，選擇 [確定]。

   新的夥伴現在會出現在 [ **合作夥伴** ] 清單中。 此外，[ **夥伴** ] 磚也會更新目前的夥伴數目。

   ![新夥伴](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>編輯合作夥伴

1. 在 [Azure 入口網站](https://portal.azure.com)中，尋找並選取整合帳戶。
選擇 [合作夥伴]**** 圖格。

   ![選擇「合作夥伴」圖格](./media/logic-apps-enterprise-integration-partners/edit.png)

1. 在 [ **夥伴**] 下，選取您要編輯的夥伴，然後選擇 [ **編輯**]。 在 [ **編輯**] 下進行變更。

   ![進行變更並儲存](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   針對 RosettaNet，在 **Rosettanet 夥伴屬性**下，您可以指定下列其他資訊：

   | 屬性 | 必要 | 描述 |
   |----------|----------|-------------|
   | **夥伴分類** | 否 | 合作夥伴的組織類型 |
   | **供應鏈碼** | 否 | 合作夥伴的供應鏈碼，例如「資訊技術」或「電子元件」 |
   | **連絡人名稱** | 否 | 合作夥伴的連絡人名稱 |
   | **電子郵件** | 否 | 合作夥伴的電子郵件地址 |
   | **傳真** | 否 | 合作夥伴的傳真號碼 |
   | **Telephone** | 否 | 合作夥伴的電話號碼 |
   ||||

1. 當您完成時，請選擇 **[確定]** 儲存您的變更。

## <a name="delete-partner"></a>刪除合作夥伴

1. 在 [Azure 入口網站](https://portal.azure.com)中，尋找並選取整合帳戶。 選擇 [合作夥伴]**** 圖格。

   ![顯示您想要刪除夥伴時所選取之 [合作夥伴] 磚的螢幕擷取畫面。](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. 在 [合作夥伴]**** 底下，選取您要刪除的合作夥伴。 選擇 [ **刪除**]。

   ![刪除合作夥伴](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>後續步驟

* 深入瞭解 [協定](../logic-apps/logic-apps-enterprise-integration-agreements.md)