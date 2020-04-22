---
title: 在 Marketo 中設定潛在客戶管理 |Azure 應用商店
description: 為 Azure 市場客戶配置 Marketo 的潛在客戶管理。
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 90f42954fd4d7324a7684795fca6ec302411790c
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731105"
---
# <a name="configure-lead-management-in-marketo"></a>在 Marketo 中設定潛在客戶管理

本文介紹如何設置 Marketo CRM 系統來處理市場產品/服務的銷售線索。

## <a name="set-up-your-marketo-crm-system"></a>設定市場 CRM 系統

1. 登入 Marketo。

2. 選取 [Design Studio]**** \(設計工作室\)。

    ![Marketo [Design Studio] \(設計工作室\)](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

3.  選取 [New Form]**** \(新增表單\)。

    ![Marketo [New Form] \(新增表單\)](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

4.  填入 [New Form] \(新增表單\) 中的必要欄位，然後選取 [Create]**** \(建立\)。

    ![Marketo 建立新表單](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

5.  在 [Field Details] \(欄位詳細資料\) 上，選取 [Finish]**** \(完成\)。

    ![Marketo 完成表單](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

6.  核准並關閉。

7. 在 *「市場討論」 支援「* 選項卡上,選擇 **」嵌入代碼**。 

    ![內嵌程式碼](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

8. Marketo [Embed Code] \(內嵌程式碼\) 會顯示類似下列範例的程式碼。

    ```html
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

9. 複製嵌入程式碼窗體中顯示的以下欄位的值。 您將使用這些值將產品/服務配置為在下一步中接收潛在顧客。 使用下一個範例作為從 Marketo [Embed Code] \(內嵌程式碼\) 範例取得所需識別碼的指南。

    - 伺服器代碼 = **ys12**
    - 蒙奇金 ID = **123-PQR-789**
    - 表格代碼 = **1179**

    **找出這些值的另一種方法**

    - 伺服器 ID 在 Marketo 實例的`serverID.marketo.com`URL 中找到,例如 ""。
    - 使用「蒙奇金帳戶 ID」欄位中的管理員>Munchkin 選單,或從 Marketo REST API 主機子網域的第一部分`https://{Munchkin ID}.mktorest.com`取得訂閱的蒙廷 ID: 。
    - 表單 ID 是您在步驟 7 中創建的嵌入代碼表單的 ID,用於從我們的市場路由潛在顧客。

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>將您的優惠設定為向 Marketo 發送潛在顧客

準備好在發佈門戶中配置產品/服務的首席管理資訊時,請按照以下步驟操作: 

1. 導航到產品 **/服務"產品/服務"設置**頁面。

1. 在「潛在顧客管理」部分下選擇 **「連接**」。 

    ![鉛管理 - 連線](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. 在"連接詳細資訊"彈出視窗上,為潛在顧客目標選擇 **"市場市場**"。

    ![選擇潛在客戶目標](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

4. 提供**伺服器 ID,****蒙奇帳戶 ID**與**表單**。

    > [!Note]
    > 您必須完成配置產品/服務的其餘部分併發佈它,然後才能收到產品/服務的潛在顧客。 

5. **聯繫電子郵件**- 為公司中應接收新潛在顧客的電子郵件通知的人員提供電子郵件。 您可以通過用分號分隔它們來提供多封電子郵件。

6. 選取 [確定]  。

   要確保已成功連接到潛在顧客目標,請單擊驗證按鈕。 如果成功,您將在潛在顧客目標中具有測試潛在顧客。

   ![連線詳細資料](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)
