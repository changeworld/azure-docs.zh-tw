---
title: 在 Marketo 中進行潛在客戶管理 - Microsoft 商業市集
description: 了解如何使用 Marketo CRM 系統以管理來自 Microsoft AppSource 和 Azure Marketplace 的潛在客戶。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: cbd12481312d310add15bf3d41e21e9c416f1c39
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491070"
---
# <a name="use-marketo-to-manage-commercial-marketplace-leads"></a>使用 Marketo 來管理商業市集潛在客戶

此文章描述如何設定您的 Marketo CRM 系統，以處理來自 Microsoft AppSource 和 Azure Marketplace 中供應項目的潛在客戶。

## <a name="set-up-your-marketo-crm-system"></a>設定您的 Marketo CRM 系統

1. 登入 Marketo。

1. 選取 [Design Studio] \(設計工作室\)。

    ![Marketo [Design Studio] \(設計工作室\)](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

1.  選取 [New Form] \(新增表單\)。

    ![Marketo [New Form] \(新增表單\)](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

1.  填入 [New Form] \(新增表單\) 對話方塊中的必要欄位，然後選取 [Create] \(建立\)。

    ![Marketo 建立新表單](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

1.  在 [Field Details] \(欄位詳細資料\) 頁面上，選取 [Finish] \(完成\)。

    ![Marketo 完成表單](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

1.  核准並關閉。

1. 在 [MarketplaceLeadBackend] 索引標籤上，選取 [Embed Code] \(內嵌程式碼\)。 

    ![Marketo [Embed Code] \(內嵌程式碼\)](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

1. Marketo [Embed Code] \(內嵌程式碼\) 會顯示類似下列範例的程式碼。

    ```html
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

1. 複製顯示於 [Embed Code] \(內嵌程式碼\) 表單中適用於下列欄位的值。 您將會在下一個步驟中使用這些值來設定供應項目以接收潛在客戶。 使用下一個範例作為從 Marketo [Embed Code] \(內嵌程式碼\) 範例取得所需識別碼的指南。

    - 伺服器識別碼 = **ys12**
    - Munchkin 識別碼 = **123-PQR-789**
    - 表單識別碼 = **1179**

    找出這些值的另一個方式：

    - 伺服器識別碼可以在您 Marketo 執行個體的 URL 中找到，例如 `serverID.marketo.com`。
    - 您可以移至 [管理] > [Munchkin] 功能表中的 [Munchkin 帳戶識別碼] 欄位來取得您訂用帳戶的 Munchkin 識別碼，或是從您 Marketo REST API 主機子網域的第一個部分加以取得：`https://{Munchkin ID}.mktorest.com`。
    - 表單識別碼是您在步驟 7 中建立來從市集路由傳送潛在客戶之 [Embed Code] \(內嵌程式碼\) 表單的識別碼。

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>設定供應項目以將潛在客戶傳送至 Marketo

當您準備好在發行入口網站中設定供應項目的潛在客戶管理資訊時，請遵循下列步驟。 

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/home)。

1. 選取您的供應項目，然後移至 [供應項目設定] 索引標籤。

1. 在 [潛在客戶] 區段底下，選取 [連線]。

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-marketo/customer-leads.png" alt-text="潛在客戶":::

1. 在 [連線詳細資料] 快顯視窗上，選取 [Marketo] 作為 [潛在客戶目的地]。

    ![選擇潛在客戶目的地](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

1. 提供 [伺服器識別碼]、[Munchkin 帳戶識別碼] 及 [表單識別碼]。

    > [!NOTE]
    > 您必須完成供應項目的其餘設定並加以發佈，才能接收到該供應項目的潛在客戶。 

1. 在 [連絡人電子郵件] 下輸入公司收到新的潛在客戶時，會收到電子郵件通知之員工的電子郵件地址。 您可以提供多個電子郵件地址，方法是以分號分隔。

1. 選取 [確定]。

   為確定您已成功連線到潛在客戶目的地，請選取 [驗證]。 如果成功，即會在潛在客戶目的地中看到測試潛在客戶。

   ![[連線詳細資料] 快顯視窗](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)
