---
title: Power BI 應用建立概述 - Azure 應用商店
description: 本文介紹了將 Power BI 應用發表到 Microsoft AppSource 的高級步驟。 還提供了 Power BI 應用必須滿足的技術和業務需求才能發佈到商業市場。
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: c348a172b16e12334d33cf2718609694147fdce3
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80674523"
---
# <a name="power-bi-app-creation-overview"></a>電源 BI 應用建立概述

> [!IMPORTANT]
> 我們將 Power BI 應用產品的管理從雲端合作夥伴門戶遷移到合作夥伴中心。 在遷移產品/服務之前,請按照雲合作夥伴門戶[Power BI 應用產品/服務](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/power-bi/cpp-power-bi-offer)中的說明來管理您的優惠。

本文介紹如何將 Power BI 應用程式發布到微軟[AppSource](https://appsource.microsoft.com/)。 Power BI 應用包可自定義的內容,包括數據集、報表和儀錶板。 然後,您可以使用 AppSource 將應用與其他 Power BI 平臺一起使用,執行開發人員允許的調整和自定義,並將其連接到您自己的數據。

## <a name="publishing-benefits"></a>發佈的優點

發佈到商業市場的好處:

- 使用 Microsoft 品牌推廣您的公司。
- AppSource 上可能覆蓋超過 1 億個 Office 365 和 Dynamics 365 使用者,並通過 Azure 應用商店覆蓋 200,000 多個組織。
- 從這些市場獲得高質量的潛在客戶。
- 讓 Microsoft 現場和電話銷售團隊推廣您的服務。

## <a name="overview"></a>概觀

:::image type="content" source="media/power-bi-app-publishing-steps.png" alt-text="發佈 Power BI 應用的步驟概述" border="false":::

以下是關鍵發佈步驟:

1. 在 Power BI 中創建應用程式。 您將收到一個包安裝連結,這是產品/服務的主要技術資產。 在合作夥伴中心創建產品/服務之前,將測試包發送到預生產。 有關詳細資訊,請參閱[什麼是 Power BI 應用?](https://docs.microsoft.com/power-bi/service-template-apps-overview)
2. 添加行銷材料,如官方名稱、說明和徽標。
3. 包括產品/服務的法律和支持文檔,如使用條款、隱私政策、支援策略和用戶説明。
4. 創建產品/服務:使用合作夥伴中心編輯詳細資訊,包括產品/服務說明、行銷材料、法律資訊、支援資訊和資產規範。
5. 提交以供發佈。
6. 在合作夥伴中心中監視流程,其中 AppSource 載入團隊測試、驗證和驗證您的應用。
7. 認證后,在測試環境中查看應用併發佈它。 這將在 AppSource 上列出它(它"
8. 在 Power BI 中,將包發送到生產。 有關詳細資訊,請參閱[管理 Power BI 應用版本](https://docs.microsoft.com/power-bi/service-template-apps-create#manage-the-template-app-release)。

## <a name="before-you-begin"></a>開始之前

查看以下連結,這些連結提供範本、提示和範例。

- [建立 Power BI 應用程式](https://docs.microsoft.com/power-bi/service-template-apps-create)
- [製作 Power BI 應用程式的提示](https://docs.microsoft.com/power-bi/service-template-apps-tips)
- [範例](https://docs.microsoft.com/power-bi/service-template-apps-samples)

## <a name="requirements"></a>需求

要在商業市場發佈,您的 Power BI 應用產品/服務必須滿足以下技術和業務需求。

### <a name="technical-requirements"></a>技術需求

您需要的主要技術資產是 Power BI[應用程式](https://go.microsoft.com/fwlink/?linkid=2028636)。 這是主要數據集、報表或儀錶板的集合。 它還包括可選擇的連線服務和嵌入式資料集,以前稱為[內容套件](https://docs.microsoft.com/power-bi/service-organizational-content-pack-introduction)。 有關開發此類應用的詳細資訊,請參閱什麼是 Power [BI 應用?](https://go.microsoft.com/fwlink/?linkid=2028636)

#### <a name="get-an-installation-web-address"></a>取得安裝 Web 位址

您只能在[Power BI](https://powerbi.microsoft.com/)環境中建構 Power BI 應用。

1. 使用 Power [BI Pro 許可證](https://docs.microsoft.com/power-bi/service-admin-purchasing-power-bi-pro)登錄。
2. 在 Power BI 中創建和測試應用。
3. 收到應用安裝 Web 位址後,將其添加到合作夥伴中心**的技術配置**頁面。

在 Power BI 中建立與測試應用後,請儲存應用程式安裝 Web 位址,因為您需要它才能[創建 Power BI 應用產品/](https://aka.ms/AzureCreatePBIServiceApp)

### <a name="business-requirements"></a>商業需求

業務需求包括程式、合同和法律義務。 您必須：

- 成為註冊的商業市場發行者。 如果您尚未註冊,請按照[《成為商業市場發佈者》](https://docs.microsoft.com/azure/marketplace/become-publisher)中的步驟操作。
- 提供符合在 AppSource 上列出的產品標準的內容。 有關詳細資訊,請參閱在[AppSource 上列出應用?下面是如何](https://appsource.microsoft.com/blogs/have-an-app-to-list-on-appsource-here-s-how)。
- 同意並遵循[微軟隱私聲明](https://privacy.microsoft.com/privacystatement)。

## <a name="next-steps"></a>後續步驟

- [在合作夥伴中心創建 Power BI 應用產品/服務](https://aka.ms/AzureCreatePBIServiceApp)