---
title: Salesforce 中的潛在客戶管理 - Microsoft 商業市集
description: 了解如何使用 Salesforce 設定 Microsoft AppSource 和 Azure Marketplace 的潛在客戶
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 7fbe27845164eb7711d3531173fb440c5a3570f4
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849125"
---
# <a name="configure-lead-management-for-salesforce"></a>針對 Salesforce 設定潛在客戶管理

本文說明如何設定您的 Salesforce 系統，以處理您在 Microsoft AppSource 和 Azure Marketplace 中供應項目的銷售潛在客戶。

> [!NOTE]
> Azure Marketplace 不支援預先填入清單，例如 [國家/地區] 欄位的值清單。 在繼續之前，請確定未設定任何清單。 或者，您可以設定 [HTTPS 端點](./commercial-marketplace-lead-management-instructions-https.md)或 [Azure 資料表](./commercial-marketplace-lead-management-instructions-azure-table.md)接收潛在客戶。

## <a name="set-up-your-salesforce-system"></a>設定 Salesforce 系統

1. 登入 Salesforce。
1. 瀏覽至 [Web 到潛在客戶] 設定。 
    
    如果使用的是 Salesforce Lighting Experience
    1. 在 Salesforce 首頁上選取 [安裝]。

       ![Salesforce 設定](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. 在 [安裝] 頁面上，移至 [平台工具] > [功能設定] > [行銷] > [Web 到潛在客戶]。

        ![Salesforce Web 到潛在客戶](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

    如果使用的是 Salesforce 傳統體驗：

    1. 在 Salesforce 首頁上選取 [安裝]。

       ![Salesforce 傳統設定](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. 在 [安裝] 頁面上，選取 [組建] > [自訂] > [潛在客戶] > [Web 到潛在客戶]。

        ![Salesforce 傳統 Web 到潛在客戶](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

   這兩種 Salesforce 體驗的其餘步驟都相同。

1. 在 [Web 到潛在客戶設定] 頁面上，選取 [建立 Web 到潛在客戶表單] 按鈕。
1. 在 [Web 到潛在客戶設定] 中，選取 [建立 Web 到潛在客戶表單]。

    ![Salesforce Web 到潛在客戶設定](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

1. 在 [建立Web 到潛在客戶表單] 上，請確定已清除 `Include reCAPTCHA in HTML` 設定，然後選取 [產生]。

    ![Salesforce 建立 Web 到潛在客戶表單窗格](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

1. 您會看到一些 HTML 文字。 搜尋文字「oid」，並從 HTML 文字複製 **「oid」值** (僅限在引號之間的文字) 並加以儲存。 請在發佈入口網站的 [組織識別碼] 欄位中貼上此值。

    ![Salesforce 建立顯示 HTML「oid」值的 Web 到潛在客戶表單](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

1. 選取 [完成]。

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>設定您的供應項目，以便將潛在客戶傳送至 Salesforce

當您準備好在發佈入口網站中設定供應項目的潛在客戶管理資訊時，請遵循下列步驟。

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/home)。

1. 選取您的供應項目，然後移至 [供應項目設定] 索引標籤。

1. 在 [潛在客戶] 區段底下，選取 [連線]。

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-salesforce/customer-leads.png" alt-text="潛在客戶":::

1. 在 [連線詳細資料] 快顯視窗中，在 [潛在客戶目的地] 選取 [Salesforce]，並將您建立的 Web 到潛在客戶表單中的 `oid` 值貼到 [組織識別碼] 欄位中。

    ![連線詳細資料快顯視窗的驗證連絡人電子郵件方塊](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)

1. 在 [連絡人電子郵件] 下輸入公司收到新的潛在客戶時，會收到電子郵件通知之員工的電子郵件地址。 您可以提供多個電子郵件，方法是以分號分隔。

1. 選取 [確定]。

為確定您已成功連線到潛在客戶目的地，請選取 [驗證]。 如果成功，即會在潛在客戶目的地中看到測試潛在客戶。

>[!NOTE]
>您必須完成供應項目的其餘設定並加以發佈，才能接收到該供應項目的潛在客戶。
