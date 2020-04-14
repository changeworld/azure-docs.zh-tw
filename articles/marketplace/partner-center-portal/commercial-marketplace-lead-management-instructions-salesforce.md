---
title: 配置銷售隊伍的潛在客戶管理 |Azure 應用商店
description: 為 Azure 應用商店客戶配置銷售隊伍上的潛在顧客管理。
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 087cdafe8b819e4929e1608ed7e00be2c1169414
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262982"
---
# <a name="configure-lead-management-for-salesforce"></a>針對 Salesforce 設定潛在客戶管理

本文介紹如何設置 Salesforce 系統來處理來自商業市場產品/服務的銷售線索。

> [!Note]
> 應用商店不支援預填充清單,例如 **「國家/地區」** 欄位的值清單。 在繼續之前,請確保沒有設置任何清單。 或者,可以將[HTTPS 終結點](./commercial-marketplace-lead-management-instructions-https.md)或[Azure 表](./commercial-marketplace-lead-management-instructions-azure-table.md)配置為接收潛在顧客。

## <a name="set-up-your-salesforce-system"></a>設定銷售隊伍系統

1. 登入 Salesforce。
2. 如果您使用的是 Salesforce 照明體驗。
    1. 從 Salesforce 主頁中選擇 **"設置**" 。
    ![Salesforce 設定](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. 從「設定」頁面,通過左側導航導航到**平臺工具->功能设置->营销->网络到潜在顾客**。

        ![Salesforce Web 到潛在客戶](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

3. 如果您使用的是 Salesforce 經典體驗:
    1. 從 Salesforce 主頁中選擇 **"設置**" 。
    ![Salesforce 經典設定](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. 從「設定」頁面,通過左側導航導航到**生成->自定义>潛在顧客>Web 到潛在顧客**。

        ![Salesforce 經典 Web 到領導](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

其餘的說明是相同的,無論您使用的 Salesforce 體驗如何。

4. 在**Web 到潛在顧客設定「頁上**,選擇」**創建 Web 到潛在顧客窗體「** 按鈕。
5. 在 [Web 到潛在客戶設定]**** 中，選取 [建立 Web 到潛在客戶表單]****。
    ![銷售隊伍 - 網路到討論串設定](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

6. 在 **"創建 Web 到潛在顧客表單**`the Include reCAPTCHA in HTML`'窗體上 ,請確保未選中設定,然後選擇"**生成**"。 
    ![銷售隊伍 - 建立網路到串列表單](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

7. 將顯示一些 HTML 文字。 搜尋文字"oid"並從 HTML 文本複製**oid 值**(僅在引號之間的文本),並儲存它。 您將在發佈門戶上的 **「組織識別元」** 欄位中貼上此值。
    ![銷售隊伍 - 建立網路到串列表單](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

8. 選取選取**Finished**。

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>將產品/服務配置為向 Salesforce 傳送潛在顧客

準備好在發佈門戶中配置產品/服務的首席管理資訊時,請按照以下步驟操作:

1. 導航到產品 **/服務"產品/服務"設置**頁面。
1. 在「潛在顧客管理」部分下選擇 **「連接**」。
    ![鉛管理 - 連線](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. 在"連接詳細資訊"彈出視窗上,選擇 **「潛在顧客目標****」的 Salesforce,** 然後`oid`按照前面的步驟貼到 **「組織」標識字**段,從 Web 到潛在顧客窗體。

1. **聯繫電子郵件**- 為公司中應接收新潛在顧客的電子郵件通知的人員提供電子郵件。 您可以通過用分號分隔它們來提供多封電子郵件。

1. 選擇 **"確定**"。

要確保已成功連接到潛在顧客目標,請單擊驗證按鈕。 如果成功,您將在潛在顧客目標中具有測試潛在顧客。

>[!Note]
>您必須完成配置產品/服務的其餘部分併發佈它,然後才能收到產品/服務的潛在顧客。

![連線詳細資訊 ─ 選擇潛在客戶目標](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

![連線詳細資訊 ─ 選擇潛在客戶目標](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)
