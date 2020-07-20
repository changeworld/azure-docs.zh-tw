---
title: SaaS 應用程式發行指南 - Microsoft 商業市集
description: 向 Microsoft AppSource 和 Azure Marketplace 發佈 SaaS 應用程式供應項目的需求和資源。
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: c19799265679eeead96bf95943f274aa32c75ff2
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86121566"
---
# <a name="saas-applications-offer-publishing-guide"></a>SaaS 應用程式供應項目發佈指南

您可以透過三種不同的動作呼叫，在商業市集中發佈 SaaS 應用程式：「與我連絡」、「立即試用」和「立即取得」。 本文說明這三個選項，包括每個選項的需求。 

## <a name="offer-overview"></a>供應項目概觀  

SaaS 應用程式可在 Microsoft AppSource 和 Azure Marketplace 中使用。  這兩個店面皆支援清單、試用版和交易供應項目。

**列出清單：** 「列出清單」發行選項包含「與我連絡」的供應項目類型，當試用版層級或交易層級的參與方式不可行時，適用此方法。 此方法的優點是可讓發行者的解決方案上市，立即開始接收可能產生交易的潛在客戶，進而提升業績。  
**試用版/交易：** 客戶可以選擇直接購買或要求試用您的解決方案。 提供試用版體驗會提升客戶的參與度，並可讓客戶先探索您的解決方案，再進行購買。 使用試用版體驗，較可能在店面獲得推廣機會，而且從客戶參與而來的潛在客戶也應會更多、更廣泛。 試用版必須至少包括在試用期間提供免費支援。  

| SaaS 應用程式供應項目 | 商業需求 | 技術需求 |  
| --- | --- | --- |  
| **與我們連絡** | 是 | 否 |  
| **Power BI / 動態** | 是 | 是 (Azure AD 整合) |  
| **SaaS 應用程式**| 是 | 是 (Azure AD 整合) |     

## <a name="saas-list"></a>SaaS 清單

對於沒有試用版也沒有計費功能的 SaaS 列出清單呼籲行動是「與我連絡」。 

您不需要設定 Azure Active Directory 就可以列出 SaaS 應用程式。 

|需求  |詳細資料  |
|---------|---------|
|您的應用程式是 SaaS 供應項目  |   您的解決方案是 SaaS 供應項目，且您提供多租用戶 SaaS 產品。      |


## <a name="saas-trial"></a>SaaS 試用版

您提供使用免費試用、軟體即服務 (SaaS) 型試用版的解決方案或應用程式。 您可以採用有限用途或有期限試用帳戶的形式，提供免費試用供應項目。 


|需求  |詳細資料  |
|---------|---------|
|您的應用程式是 SaaS 供應項目  |   您的解決方案是 SaaS 供應項目，且您提供多租用戶 SaaS 產品。      |
|您的應用程式已啟用 AAD     |   系統會將客戶重新導向到您的網域，讓您直接與客戶進行交易       |


## <a name="saas-trial-technical-requirements"></a>SaaS 試用版技術需求

SaaS 應用程式的技術需求很簡單。 發行者只需要與 Azure Active Directory (Azure AD) 整合即可發行。 Azure AD 與應用程式的整合已有完善的記載，Microsoft 會提供多個 SDK 和資源來完成這項作業。  

一開始，建議您備妥專用於 Azure Marketplace 發行的訂用帳戶，以便您可以讓此工作與其他計劃分開進行。 準備好之後，您就可以開始在此訂用帳戶中部署 SaaS 應用程式，並開始進行開發工作。  

您可以在下列網站取得最適合的 Azure Active Directory 文件、範例和指引： 

* [Azure Active Directory 開發人員指南](../active-directory/develop/index.yml)

* [與 Azure Active Directory 整合](../active-directory/develop/active-directory-how-to-integrate.md)

* [Azure 藍圖 - 安全性與身分識別](https://azure.microsoft.com/roadmap/?category=security-identity)

如需影片教學課程，請檢閱下列內容：

* [以 Vittorio Bertocci 進行的 Azure Active Directory 驗證](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Azure Active Directory 身分識別技術簡報 - 第 1 部分 (共 2 部分)](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Azure Active Directory 身分識別技術簡報 - 第 2 部分 (共 2 部分)](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [使用 Microsoft Azure Active Directory 建置應用程式](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [內容重點放在 Active Directory 的 Microsoft Azure 影片](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

免費的 Azure Active Directory 訓練如下  
* [IT 專業人員的 Microsoft Azure 內容系列：Azure Active Directory](https://mva.microsoft.com/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

此外，Azure Active Directory 提供可檢查服務更新的網站   
* [Azure AD 服務更新](https://azure.microsoft.com/updates/?product=active-directory)

## <a name="using-azure-active-directory-to-enable-trials"></a>使用 Azure Active Directory 啟用試用版  

Microsoft 可使用 Azure AD 來驗證所有 Marketplace 使用者，因此已驗證的使用者點選試用版清單並重新導向至試用版環境時，您可以將使用者直接佈建置至試用版，而不需要額外的登入步驟。 應用程式在驗證期間從 Azure AD 收到的權杖，包括可用以在應用程式中建立使用者帳戶的寶貴使用者資訊，讓您將佈建體驗自動化並增加轉換的可能性。 如需有關權杖的詳細資訊，請參閱[權杖範例](../active-directory/develop/active-directory-token-and-claims.md)。

使用 Azure AD 對應用程式或試用版啟用單鍵驗證，可達到以下效果：  
* 簡化客戶從 Marketplace 至試用版的體驗。  
* 即使將使用者從 Marketplace 重新導向至您的網域或試用版環境，依然覺得是在「產品內部」。  
* 因為沒有額外的登入步驟，所以可減少在重新導向時放棄的可能性。  
* 減少眾多 Azure AD 使用者的部署障礙。  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>向 Marketplace 證明您的 Azure Active Directory 整合  

您可使用幾個不同的方式來證明 Azure AD 整合，而這取決於您的應用程式是單一租用戶或多租用戶，以及您是初次接觸 Azure AD 同盟單一登入 (SSO)，還是已經支援此項目。  

**對於多租用戶應用程式：**  

如果您已支援 Azure AD，請執行下列動作：
1.    在 Azure 入口網站註冊您的應用程式
2.    在 Azure AD 中啟用多租用戶支援功能，以獲得「單鍵」試用體驗。 您可以在[此處](../active-directory/develop/active-directory-integrating-applications.md)找到更多特定資訊。  

如果您剛剛接觸 Azure AD 同盟 SSO，請執行下列動作： 
1.  在 Azure 入口網站註冊您的應用程式
2.  使用 [OpenID Connect](../active-directory/develop/active-directory-protocols-openid-connect-code.md) 或 [OAuth 2.0](../active-directory/develop/active-directory-protocols-oauth-code.md)，開發透過 Azure AD 的 SSO。
3.  啟用 AAD 中的多租用戶支援功能以獲得「單鍵」試用版體驗，您可以在[此處](../active-directory/develop/active-directory-devhowto-appsource-certified.md)找到更多特定資訊。  

**對於單一租用戶應用程式，請使用下列任一選項：**  
* 使用 [Azure B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) 將使用者新增至目錄作為來賓使用者
* 使用「與我連絡」為客戶手動佈建試用版
* 開發每個客戶的「試用產品」
* 建置使用 SSO 的多租用戶範例示範應用程式

## <a name="saas-subscriptions"></a>SaaS 訂用帳戶

您可以使用「SaaS 應用程式」供應項目類型，讓客戶以訂用帳戶的形式購買您的 SaaS 型技術解決方案。 您的 SaaS 應用程式必須符合下列需求：
- 依照固定費率 (每月或每年) 或依每個使用者的費率，對服務進行定價和收費。
- 提供可隨時升級或取消服務的方法。
Microsoft 主控商務交易。 Microsoft 代表您向客戶收費。 若要以訂閱形式提供 SaaS 應用程式，您必須與 SaaS 履行 API 整合。  您的服務必須支援佈建、升級和取消。

| 需求 | 詳細資料 |  
|:--- |:--- |  
|計費和計量 | 您的供應項目是根據您在發佈之前選取的價格模型 (固定費率或每位使用者) 來計價。  如果使用固定費率模型，您可以選擇性地加入額外維度以向客戶收取固定費率中未包含的使用量費用。 |  
|取消 | 客戶可隨時取消您的供應項目。 |  
|交易登陸頁面 | 您裝載 Azure 共同品牌的交易登陸頁面，使用者能夠在頁面上建立並管理其 SaaS 服務帳戶。 |   
| 訂用帳戶 API | 您公開一項服務，能與 SaaS 訂用帳戶互動，以建立、更新及刪除使用者帳戶與服務方案。 必須在 24 小時內支援重大的 API 變更。 需定期發行非重大的 API 變更。 |  

>[!Note]
>現已推出雲端解決方案提供者 (CSP) 合作夥伴通道選擇加入。  如需透過 Microsoft CSP 合作夥伴通道為您的供應項目推動行銷的詳細資訊，請參閱[＜雲端解決方案提供者＞](./cloud-solution-providers.md)。

## <a name="next-steps"></a>後續步驟
如果您還沒有這麼做，請

* [了解](https://azuremarketplace.microsoft.com/sell)市集。

若要在合作夥伴中心註冊，請開始建立新供應項目或使用現有供應項目：

* [登入合作夥伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)，以建立或完成您的供應項目。
* 如需詳細資訊，請參閱[＜建立 SaaS 應用程式供應項目＞](./partner-center-portal/create-new-saas-offer.md)。
