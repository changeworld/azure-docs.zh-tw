---
title: 發佈 Power BI 應用程式供應專案 |Azure Marketplace
description: 在 Microsoft AppSource marketplace 上發佈 Power BI 應用程式供應專案。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: eb31520c81a4064edbe54a0256b694c4ad88fb49
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82141704"
---
# <a name="publish-a-power-bi-app-offer"></a>發佈 Power BI 應用程式供應專案

>[!Important]
>從2020年4月13日開始，我們會開始將您 Power BI 應用程式供應專案的管理移至合作夥伴中心。 在遷移之後，您將在合作夥伴中心建立和管理您的供應專案。 依照[Power BI 應用程式建立總覽](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer)中的指示，管理您遷移的供應專案。

在 Cloud Partner 入口網站中定義供應專案並建立相關聯的技術資產之後，最後一個步驟是提交供應專案以供發佈。 若要啟動此程式，請在 [**新增供應**專案] 視窗的左窗格中選取 [**發佈**]。 如需詳細資訊，請參閱[發佈 Azure Marketplace 和 AppSource 供應項目](../manage-offers/cpp-publish-offer.md)。


## <a name="publishing-steps"></a>發佈步驟

以下是發佈程式中的主要步驟：

![發佈 Power BI 應用程式供應專案的流程步驟](./media/publishing-process-steps.png)

下表描述每個步驟，並提供其預估完成時間：

|   發行步驟            |   時間     |   說明                                                                  |
| --------------------         |------------| ----------------                                                               |
| 檢查必要條件       | 15 分鐘     | 檢查供應項目資訊和供應項目設定。                            |
| 認證                | 1-7 天   | Power BI 認證小組會分析您的供應專案。 小組會透過手動驗證測試來執行您的 Power BI 應用程式，方法是透過提供的安裝 URL 來安裝應用程式。 主要驗證會在應用程式認證過程中執行（本檔稍後會加以說明）。         |
| 包裝                    | \< 1 小時  | 供應專案的技術資產會封裝供客戶使用。                        |
| 潛在客戶產生註冊 | \< 1 小時  | 設定和部署潛在客戶系統。                                      |
| 發行者簽核            | \-         | 在供應專案上線之前，您必須先完成最後的評論和確認。 您現在也會有一個連結可預覽您的供應專案。 當您滿意預覽的外觀之後，請選取 [**狀態**] 索引標籤上的 **[上線]** 。這會將要求傳送給上線小組，以在 AppSource 上列出您的應用程式。    |
| 即時                         | \< 3 小時 | 您的供應專案現在會在 AppSource 上公開列出（「即時」），而客戶可以查看您的應用程式，並將其部署在 Power BI 訂用帳戶中。 您也會收到確認電子郵件。 在 [**所有**供應專案] 索引標籤的右側欄位中，您可以看到所有供應專案的狀態。 在 [**狀態**] 索引標籤上，您可以看到供應專案的詳細發佈流程狀態。 |
|   |   |

此程式最多允許8天完成。 完成這些發佈步驟之後，您的 Power BI 應用程式供應專案將會列在[AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) Power BI apps 區段中。


### <a name="app-certification-process"></a>應用程式認證流程

Microsoft 上架小組會使用此程式來驗證您的 Power BI 應用程式供應專案提交：

1. 查看法律檔和說明連結。
2. 驗證支援連絡人資訊。
3. 使用安裝程式 URL 來驗證正確的安裝。
4. 掃描應用程式中是否有惡意程式碼和其他惡意內容。
5. 確認顯示的內容符合應用程式的描述。
6. 確認應用程式相關作業在 Power BI 中如預期般運作。 小組會開啟包含範例資料的報表和儀表板、連接到自訂資料來源、重新整理資料等等。

如果認證小組發現任何問題，他們會提供意見反應。  如需 Power BI 應用程式需求的詳細資訊，請參閱[Power BI 應用程式檔](https://go.microsoft.com/fwlink/?linkid=2028636)。


## <a name="next-steps"></a>後續步驟

我們建議您定期在[AppSource marketplace](https://appsource.microsoft.com)中監視您的應用程式。  您也應該使用[Cloud Partner 入口網站](https://cloudpartner.azure.com/#insights)的[賣方見解](../../cloud-partner-portal-orig/si-getting-started.md)功能，取得 marketplace 客戶和應用程式使用方式的深入解析。 最後，您可以[更新您的供應](./cpp-update-existing-offer.md)專案。
