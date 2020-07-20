---
title: 新增 B2B 整合的交易夥伴
description: 在整合帳戶中建立與 Azure Logic Apps 搭配使用的交易夥伴
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: e58cbe85f30ea09adde45d55bb7b80c710c45495
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74792435"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>將合作對象新增至 Azure Logic Apps 的整合帳戶

在[Azure Logic Apps](../logic-apps/logic-apps-overview.md)中，您可以使用[整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)搭配您的邏輯應用程式，建立自動化的企業對企業（B2B）整合工作流程。 若要代表您的組織和其他人，您可以建立交易夥伴並將其新增為整合帳戶的構件。 合作夥伴是參與 B2B 交易及在彼此之間交換訊息的實體。

建立這些合作夥伴之前，請務必與您的合作夥伴討論並分享相關資訊，以瞭解如何識別和驗證其他傳送的訊息。 在您同意這些詳細資料之後，您就可以開始在整合帳戶中建立合作夥伴。

## <a name="partner-roles-in-integration-accounts"></a>整合帳戶中的夥伴角色

若要定義與合作夥伴交換之訊息的相關詳細資料，您可以建立[合約，並將其新增為](../logic-apps/logic-apps-enterprise-integration-agreements.md)整合帳戶的構件。 合約要求您的整合帳戶中至少要有兩個合作夥伴。 貴組織一律是合約中的*主機合作夥伴*。 與您的組織交換訊息的組織是*來賓夥伴*。 來賓合作夥伴可以是另一家公司，或甚至是您自己組織中的部門。 在您新增這些合作夥伴之後，您可以建立合約。

在協定中，您可以指定從主機合作夥伴的觀點來處理傳入和傳出訊息的詳細資料。 對於內送訊息，**接收設定會**指定主機夥伴如何從合約中的來賓夥伴接收訊息。 若為外寄訊息，**傳送設定**會指定主機夥伴如何將訊息傳送給來賓夥伴。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 用於儲存您的夥伴、合約和其他 B2B 成品的[整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 此整合帳戶必須與您的 Azure 訂用帳戶相關聯。

## <a name="create-partner"></a>建立合作夥伴

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在主要 Azure 功能表上，選取 [所有服務]****。 在搜尋方塊中，輸入「整合」，然後選取 [**整合帳戶**]。

   ![選取 [整合帳戶]](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. 在 [整合帳戶]**** 底下，選取要將合作夥伴新增到其中的整合帳戶。

   ![選取企業整合帳戶](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. 選擇 [合作夥伴]**** 圖格。

   ![選擇「合作夥伴」圖格](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. 在 [合作夥伴]**** 底下，選擇 [新增]****。 在 [**新增夥伴**] 底下，提供合作夥伴的詳細資料，如下表所述。

   ![選擇 [新增] 並提供合作夥伴詳細資料](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | 屬性 | 必要 | 描述 |
   |----------|----------|-------------|
   | **名稱** | Yes | 夥伴的名稱 |
   | **Qualifier** | Yes | 為組織提供獨特商務身分識別的驗證主體，例如， **D-U-N-S （Dun & Bradstreet）**。 <p>合作夥伴可以選擇進行相互定義的商務身分識別。 針對這些案例，請針對 [EDIFACT] 選取 [**相互定義**] 或 [針對 X12 進行**相互定義（X12）** ]。 <p>針對 RosettaNet，請選取 [僅**DUNS**]，這是標準。 |
   | **ReplTest1** | 是 | 識別您的邏輯應用程式所接收之檔的值。 <p>對於 RosettaNet，此值必須是對應到 DUNS 數位的九位數數位。 |
   ||||

   > [!NOTE]
   > 針對使用 RosettaNet 的合作夥伴，您可以先建立這些夥伴，然後在[之後進行編輯](#edit-partner)，以指定其他資訊。

1. 完成時，選擇 [確定]。

   您的新合作夥伴現在會顯示在 [**合作夥伴**] 清單中。 此外，[**合作夥伴**] 圖格也會更新目前的夥伴數目。

   ![新增合作夥伴](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>編輯合作夥伴

1. 在 [Azure 入口網站](https://portal.azure.com)中，尋找並選取整合帳戶。
選擇 [合作夥伴]**** 圖格。

   ![選擇「合作夥伴」圖格](./media/logic-apps-enterprise-integration-partners/edit.png)

1. 在 [**合作夥伴**] 底下，選取您要編輯的合作夥伴，然後選擇 [**編輯**]。 在 [**編輯**] 底下進行變更。

   ![進行變更並儲存](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   針對 RosettaNet，您可以在 [ **Rosettanet 夥伴屬性**] 下指定下列其他資訊：

   | 屬性 | 必要 | 說明 |
   |----------|----------|-------------|
   | **夥伴分類** | No | 合作夥伴的組織類型 |
   | **供應鏈代碼** | No | 合作夥伴的供應鏈代碼，例如「資訊技術」或「電子元件」 |
   | **連絡人名稱** | No | 合作夥伴的連絡人姓名 |
   | **電子郵件** | No | 合作夥伴的電子郵件地址 |
   | **Fax** | No | 合作夥伴的傳真號碼 |
   | **Telephone** | No | 合作夥伴的電話號碼 |
   ||||

1. 當您完成時，請選擇 **[確定]** 以儲存變更。

## <a name="delete-partner"></a>刪除合作夥伴

1. 在 [Azure 入口網站](https://portal.azure.com)中，尋找並選取整合帳戶。 選擇 [合作夥伴]**** 圖格。

   ![選擇「合作夥伴」圖格](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. 在 [合作夥伴]**** 底下，選取您要刪除的合作夥伴。 選擇 [**刪除**]。

   ![刪除合作夥伴](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>後續步驟

* 深入瞭解[協定](../logic-apps/logic-apps-enterprise-integration-agreements.md)