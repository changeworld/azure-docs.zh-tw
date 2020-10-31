---
title: Power BI 應用程式建立總覽-Microsoft AppSource
description: 本文說明將 Power BI 應用程式發佈至 Microsoft AppSource 的高階步驟。 此外，也會提供您的 Power BI 應用程式必須符合才能發佈至商用 marketplace 的技術和商務需求。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: navits09
ms.author: navits
ms.date: 04/02/2020
ms.openlocfilehash: af77f18771d8e18ee86b79360ac03307d3330cb0
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93131423"
---
# <a name="power-bi-app-creation-overview"></a>Power BI 應用程式建立總覽

本文說明如何將 Power BI 應用程式發佈至 Microsoft [AppSource](https://appsource.microsoft.com/)。 Power BI 應用程式會封裝可自訂的內容，包括資料集、報表和儀表板。 然後，您可以使用應用程式搭配其他 Power BI 平臺使用 AppSource、執行開發人員所允許的調整和自訂，並將它連接到您自己的資料。

## <a name="publishing-benefits"></a>發佈的優點

發佈至商業市集的優點：

- 使用 Microsoft 品牌來推廣您的公司。
- 透過 Azure Marketplace，有可能觸及超過 100000000 Microsoft 365 和 Dynamics 365 使用者 AppSource 及超過200000個組織。
- 從這些市集獲得高品質潛在客戶。
- 由 Microsoft 現場和電話銷售團隊推廣您的服務。

## <a name="overview"></a>概觀

:::image type="content" source="media/power-bi-app-publishing-steps.png" alt-text="發佈 Power BI 應用程式的步驟總覽" border="false":::

以下是主要的發佈步驟：

1. 在 Power BI 中建立您的應用程式。 您將會收到 [套件安裝] 連結，這是供應專案的主要技術資產。 在合作夥伴中心中建立供應專案之前，請先將測試套件傳送至生產階段前。 如需詳細資訊，請參閱 [什麼是 Power BI apps？](/power-bi/service-template-apps-overview)。
2. 新增行銷材質，例如官方名稱、描述和標誌。
3. 包含供應專案的法律和支援檔，例如使用條款、隱私權原則、支援原則和使用者說明。
4. 建立供應專案–使用合作夥伴中心編輯詳細資料，包括供應專案說明、行銷資料、法律資訊、支援資訊，以及資產規格。
5. 提交以供發行。
6. 在合作夥伴中心中監視程式，AppSource 上架小組會測試、驗證和認證您的應用程式。
7. 通過認證後，請在其測試環境中檢查應用程式，並加以發行。 這將會在 AppSource 上列出它 (「上線」 ) 。
8. 在 Power BI 中，將套件傳送到生產環境。 如需詳細資訊，請參閱 [管理 Power BI 應用程式版本](/power-bi/service-template-apps-create#manage-the-template-app-release)。

## <a name="before-you-begin"></a>開始之前

請參閱下列連結，其中提供範本、秘訣和範例。

- [建立 Power BI 應用程式](/power-bi/service-template-apps-create)
- [撰寫 Power BI 應用程式的秘訣](/power-bi/service-template-apps-tips)
- [範例](/power-bi/service-template-apps-samples)

## <a name="requirements"></a>需求

若要在商業 marketplace 中發佈，您的 Power BI 應用程式供應專案必須符合下列技術和商務需求。

### <a name="technical-requirements"></a>技術需求

您需要的主要技術資產是 [Power BI 應用程式](/power-bi/connect-data/service-template-apps-overview)。 這是主要資料集、報表或儀表板的集合。 它也包含選用的已連接服務和內嵌資料集，之前稱為 [內容套件](/power-bi/service-organizational-content-pack-introduction)。 如需有關開發這種應用程式類型的詳細資訊，請參閱 [什麼是 Power BI apps？](/power-bi/connect-data/service-template-apps-overview)。

#### <a name="get-an-installation-web-address"></a>取得安裝網址

您只能在 [Power BI](https://powerbi.microsoft.com/) 環境內建立 Power BI 應用程式。

1. 使用 [Power BI Pro 授權](/power-bi/service-admin-purchasing-power-bi-pro)登入。
2. 在 Power BI 中建立並測試您的應用程式。
3. 當您收到應用程式安裝網址時，請將它新增至合作夥伴中心中的 **技術** 設定頁面。

在 Power BI 中建立並測試您的應用程式之後，請儲存應用程式安裝 web 位址，因為您將需要它來 [建立 Power BI 應用程式供應](create-power-bi-app-offer.md)專案。

### <a name="business-requirements"></a>商務需求

商務需求包括程式性、合約和法律義務。 您必須：

- 成為註冊的商業 marketplace 發行者。 如果您未註冊，請依照合作夥伴中心中的 [建立商業 marketplace 帳戶](create-account.md)中的步驟執行。
- 提供符合您的供應專案準則的內容，以列在 AppSource 上。 如需詳細資訊，請參閱 [AppSource 上的應用程式清單？做法如下](https://appsource.microsoft.com/blogs/have-an-app-to-list-on-appsource-here-s-how)：
- 同意並遵循 [Microsoft 隱私權聲明](https://privacy.microsoft.com/privacystatement)。

## <a name="next-step"></a>後續步驟

- [在合作夥伴中心中建立 Power BI 應用程式供應專案](create-power-bi-app-offer.md)