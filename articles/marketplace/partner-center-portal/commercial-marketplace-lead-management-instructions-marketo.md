---
title: Marketo 中的潛在客戶管理-Microsoft 商業 marketplace
description: 瞭解如何使用 Marketo CRM 系統來管理來自 Microsoft AppSource 和 Azure Marketplace 的潛在客戶。
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 1f3a097629f8c5f4a3f0ecefa5ee50f2d3e62162
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82789873"
---
# <a name="use-marketo-to-manage-commercial-marketplace-leads"></a>使用 Marketo 管理商業 marketplace 潛在客戶

本文說明如何設定您的 Marketo CRM 系統，以在 Microsoft AppSource 和 Azure Marketplace 中處理您供應專案的銷售潛在客戶。

## <a name="set-up-your-marketo-crm-system"></a>設定您的 Marketo CRM 系統

1. 登入 Marketo。

1. 選取 [Design Studio]**** \(設計工作室\)。

    ![Marketo [Design Studio] \(設計工作室\)](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

1.  選取 [New Form]**** \(新增表單\)。

    ![Marketo 新表單](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

1.  在 [**新表單**] 對話方塊中填入必要欄位，然後選取 [**建立**]。

    ![Marketo 建立新表單](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

1.  在 [**欄位詳細資料**] 頁面上，選取 **[完成]**。

    ![Marketo 完成表單](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

1.  [核准] 和 [關閉]。

1. 在 [ **MarketplaceLeadBackend** ] 索引標籤上，選取 [**內嵌程式碼**]。 

    ![Marketo 內嵌程式碼](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

1. Marketo [Embed Code] \(內嵌程式碼\) 會顯示類似下列範例的程式碼。

    ```html
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

1. 複製 [內嵌程式碼] 表單中顯示的下欄欄位的值。 您將會使用這些值來設定您的供應專案，以便在下一個步驟中收到潛在客戶。 使用下一個範例做為指南，從 Marketo 內嵌程式碼範例取得您需要的識別碼。

    - 伺服器識別碼 = **ys12**
    - Munchkin ID = **123-PQR-789**
    - 表單識別碼 = **1179**

    另一種找出這些值的方式：

    - 伺服器識別碼可在 Marketo 實例的 URL 中找到，例如`serverID.marketo.com`。
    - 前往 [ **Munchkin 帳戶識別碼**] 欄位中的 [系統**管理** > ] [**Munchkin** ] 功能表，或從 Marketo REST API 主機子域的第一個部分，取得訂用`https://{Munchkin ID}.mktorest.com`帳戶的 Munchkin 識別碼：。
    - [表單識別碼] 是您在步驟7中建立的內嵌程式碼表單識別碼，可從 marketplace 路由潛在客戶。

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>設定您的供應專案以將潛在客戶傳送至 Marketo

當您準備好在發佈入口網站中為您的供應專案設定潛在客戶管理資訊時，請遵循下列步驟。 

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/home)。

1. 選取您的供應專案，然後移至 [**供應專案設定**] 索引標籤。

1. 在 [**潛在客戶管理**] 區段下，選取 **[連線]**。 

    ![潛在客戶管理區段 [連線] 按鈕](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. 在 [連線**詳細資料**] 快顯視窗中，針對 [**潛在客戶目的地**] 選取 [ **Marketo** ]。

    ![選擇潛在客戶目的地](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

1. 提供 [**伺服器識別碼**]、[ **Munchkin 帳戶識別碼**] 和 [**表單識別碼**]。

    > [!NOTE]
    > 您必須先完成供應專案的其餘部分設定並加以發佈，才能接收供應專案的潛在客戶。 

1. 在 [**連絡人電子郵件**] 底下，輸入公司人員在收到新的潛在客戶時應接收電子郵件通知的電子郵件地址。 您可以提供多個電子郵件地址，並以分號分隔。

1. 選取 [確定]  。

   若要確定您已成功連線到潛在客戶目的地，請選取 [**驗證**]。 如果成功，您會在潛在客戶目的地中擁有測試潛在客戶。

   ![[連接詳細資料] 快顯視窗](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)
