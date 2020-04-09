---
title: 發佈電源 BI 應用產品 /服務 |Azure 應用商店
description: 在 Microsoft AppSource 市場上發表 Power BI 應用產品/服務。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: a5eb5b652f7a419c6c2d1b3e6880c3c3003fb679
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985519"
---
# <a name="publish-a-power-bi-app-offer"></a>發表 Power BI 應用產品/服務

>[!Important]
>從 2020 年 4 月 13 日起,我們將開始將 Power BI 應用優惠的管理轉移到合作夥伴中心。 遷移後,您將在合作夥伴中心創建和管理您的優惠。 按照[Power BI 應用創建概述](https://aka.ms/AzureCreatePBIServiceApp)中的說明進行操作,以管理遷移的優惠。

在雲合作夥伴門戶中定義產品/服務並創建關聯的技術資產后,最後一步是提交發佈產品/服務。 要啟動此過程,請在 **「新優惠**」視窗的左側窗格中,選擇 **「發布**」。 如需詳細資訊，請參閱[發佈 Azure Marketplace 和 AppSource 供應項目](../manage-offers/cpp-publish-offer.md)。


## <a name="publishing-steps"></a>發佈步驟

以下是發佈過程中的主要步驟:

![發表 Power BI 應用產品/服務的過程步驟](./media/publishing-process-steps.png)

下表描述了每個步驟,並提供其估計完成時間:

|   發佈步驟            |   Time     |   描述                                                                  |
| --------------------         |------------| ----------------                                                               |
| 檢查必要條件       | 15 分鐘     | 檢查供應項目資訊和供應項目設定。                            |
| 認證                | 1-7 天   | Power BI 認證團隊分析您的產品/服務。 團隊通過手動驗證測試運行您的 Power BI 應用,透過提供的安裝 URL 安裝應用。 主驗證作為應用認證過程的一部分執行(本文檔後面所述)。         |
| 包裝                    | \< 1 小時  | 產品/服務的技術資產打包供客戶使用。                        |
| 鉛產生註冊 | \< 1 小時  | 設定和部署潛在客戶系統。                                      |
| 發行者簽核            | \-         | 在優惠生效之前,您完成最終審核和確認。 現在,您還將有一個連結來預覽您的產品/ 對預覽的外觀感到滿意後,請在「**狀態**」選項卡上選擇 **「上線**」。這將向載入團隊發送請求,以在 AppSource 上列出您的應用。    |
| 即時                         | \< 3 小時 | 您的產品/服務現已在 AppSource 上公開列出("即時"),客戶可以查看您的應用並將其部署在其 Power BI 訂閱中。 您還將收到確認電子郵件。 在「**所有優惠」** 選項卡的右列中,您可以看到所有優惠的狀態。 在「**狀態」** 選項卡上,您可以看到產品/服務的詳細發佈流狀態。 |
|   |   |

此過程最多允許八天。 完成這些發佈步驟後,您的 Power BI 應用產品/服務將列在[AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) Power BI 應用部分。


### <a name="app-certification-process"></a>應用程式認證流程

Microsoft 載入團隊使用此過程驗證您的 Power BI 應用產品/服務提交:

1. 查看法律文件和幫助連結。
2. 驗證支援聯繫資訊。
3. 使用安裝程式 URL 驗證正確的安裝。
4. 掃描應用以發現惡意軟體和其他惡意內容。
5. 驗證顯示的內容是否與應用的說明匹配。
6. 驗證與應用相關的操作在 Power BI 中是否按預期工作。 團隊打開包含範例數據的報告和儀表板、連接到自訂數據來源、刷新資料等。

如果認證小組發現任何問題，他們會提供意見反應。  有關 Power BI 應用要求的詳細資訊,請參閱[Power BI 應用文件](https://go.microsoft.com/fwlink/?linkid=2028636)。


## <a name="next-steps"></a>後續步驟

我們建議您定期在[AppSource 市場](https://appsource.microsoft.com)中監視你的應用程式。  您還應使用[雲合作夥伴門戶](https://cloudpartner.azure.com/#insights)的[賣家見解](../../cloud-partner-portal-orig/si-getting-started.md)功能,獲取有關市場客戶和應用使用方式的見解。 最後,您可以[更新您的優惠](./cpp-update-existing-offer.md)。
