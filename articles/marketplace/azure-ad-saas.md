---
title: 在商業市場 Azure Active Directory 及可交易 SaaS 供應專案
description: 瞭解 Azure Active Directory 如何與 Microsoft 商用 marketplace 中的可交易 SaaS 供應專案搭配運作。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: 674f267d3d99dd22c1ae06b6d32587761d5983ce
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93124912"
---
# <a name="azure-ad-and-transactable-saas-offers-in-the-commercial-marketplace"></a>在商業市場 Azure AD 及可交易 SaaS 供應專案

Microsoft 雲端式身分識別與存取管理服務（ [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) 可協助使用者登入和存取內部和外部資源。 在 Microsoft 商業市集中，Azure AD 讓每個人都能更輕鬆且更安全，包括發行者、購買者和使用者。 透過 Azure AD，發行者可以自動將使用者布建到其軟體即服務 (SaaS) 應用程式，而買方本身可以管理這些布建的使用者。 

此外， [Azure AD 單一登入](../active-directory/manage-apps/what-is-single-sign-on.md) (SSO) 可在使用者于 Azure AD 中登入應用程式時提供安全性和便利性。 更快速的參與和優化體驗也可讓買方和使用者信賴與發行者的 SaaS 應用程式的第一次互動。 這會提供正面的印象，讓您建立可見度並鼓勵重複的商務。

遵循本文中的指導方針，您將有助於在商業市場中認證您的 SaaS 供應專案。 如需認證的詳細資訊，請閱讀詳細的 [商業 marketplace 認證原則](/legal/marketplace/certification-policies#100-general)，包括 [SaaS](/legal/marketplace/certification-policies#1000-software-as-a-service-saas)專屬的原則。

## <a name="before-you-begin"></a>開始之前

當您在合作夥伴中心中 [建立 SaaS 供應](./create-new-saas-offer.md) 專案時，您可以從一組將顯示在供應專案清單中的特定清單選項中進行選擇。 您的選擇會決定您的供應專案在商業 marketplace 中的交易方式。 透過 Microsoft 銷售的優惠稱為可交易優惠。 我們會代表您向客戶收取所有可交易優惠的費用。 如果您選擇透過 Microsoft 銷售，並代表您將交易提供給您 ([ **是]** 選項) ，則您已選擇建立可交易供應專案，而這篇文章適用于您。 建議您完整閱讀。

如果您選擇只透過商業 marketplace 列出您的供應專案，並獨立處理交易 ( **沒有** 選項) ，您有三個選項可供潛在客戶存取您的供應專案：立即取得 (免費) 、免費試用版，以及與我聯絡。 如果您選取 [ **立即取得] (免費)** 或 **免費試用版** ，則不會為您提供這篇文章。 如需詳細資訊，請參閱在 [商業市場中建立免費或試用 SaaS 供應專案的登陸頁面](./azure-ad-free-or-trial-landing-page.md) 。 如果您選取 [ **連絡人** ]，就不會有直接的發行者責任。 繼續在合作夥伴中心中建立您的供應專案。

## <a name="how-azure-ad-works-with-the-commercial-marketplace-for-saas-offers"></a>Azure AD 如何搭配使用 SaaS 供應專案的商業 marketplace

Azure AD 可無縫地購買、履行及管理商用 marketplace 解決方案。 [圖 1] 顯示發行者、買方和使用者如何互動以購買和啟用訂用帳戶。 它也會顯示客戶如何使用和管理從商業市場取得的 SaaS 應用程式。 基於此圖的目的，買方是 SaaS 應用程式使用者，該使用者會從商業 marketplace 起始購買。

如圖1所示，當買方選取您的供應專案時，他們會開始一系列的工作流程，其中包括購買、訂用帳戶和使用者管理。 在此鏈中，您是發行者負責特定需求，而 Microsoft 在關鍵點提供支援。

**_圖1：在商用 marketplace 中使用 SaaS_* 供應專案的 Azure AD _

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow.png" alt-text="說明購買管理、訂用帳戶管理和選擇性的使用者管理程式步驟。":::

下列各節提供每個處理步驟之需求的詳細資料。

## <a name="process-steps-for-purchase-management"></a>購買管理的處理步驟

下圖顯示購買管理的四個處理步驟。

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-1-4.png" alt-text="說明購買管理、訂用帳戶管理和選擇性的使用者管理程式步驟。":::

下表提供購買管理處理步驟的詳細資料。

| 處理步驟 | 發行者動作 | 發行者的建議或必要 |
| ------------ | ------------- | ------------- |
| 1. 買方以其 Azure 識別碼身分登入商用 marketplace，並選取 SaaS 供應專案。 | 不需要任何發行者動作。 | 不適用 |
| 2. 購買之後，買方會在 Azure Marketplace 中選取 [_ *設定帳戶* *]，或在 [AppSource] 中選取 [ **立即設定** ]，這會將買方導向此供應專案的發行者登陸頁面。 買方必須能夠使用 Azure AD SSO 登入發行者的 SaaS 應用程式，而且必須只要求不需要 Azure AD 系統管理員核准的最基本同意。 | 設計供應專案的 [登陸頁面](azure-ad-transactable-saas-landing-page.md) ，讓它收到使用者 Azure AD 或 MICROSOFT 帳戶 (MSA) 身分識別，並協助所需的任何額外布建或設定。 | 必要 |
| 3. 發行者要求從 SaaS 履行 API 購買詳細資料。 | 使用從登陸頁面的應用程式識別碼產生的 [存取權杖](./partner-center-portal/pc-saas-registration.md) ， [呼叫解析端點](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) 來取得有關購買的詳細資訊。 | 必要 |
| 4. 透過 Azure AD 和 Microsoft Graph API，發行者會收集在發行者的 SaaS 應用程式中布建買方所需的公司和使用者詳細資料。  | 分解 Azure AD 使用者權杖以尋找名稱和電子郵件，或 [呼叫 MICROSOFT GRAPH API](/graph/use-the-api) ，並使用委派的許可權來取得已登入之使用者的相關 [資訊](/graph/api/user-get) 。 | 必要 |
||||

## <a name="process-steps-for-subscription-management"></a>訂用帳戶管理的處理步驟

下圖顯示訂用帳戶管理的兩個處理步驟。

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-5-6.png" alt-text="說明購買管理、訂用帳戶管理和選擇性的使用者管理程式步驟。":::

下表說明訂用帳戶管理處理步驟的詳細資料。

| 處理步驟 | 發行者動作 | 發行者的建議或必要 |
| ------------ | ------------- | ------------- |
| 5. 發行者透過 SaaS 履行 API 來管理 SaaS 應用程式的訂用帳戶。 | 透過 [SaaS 履行 api](./partner-center-portal/pc-saas-fulfillment-api-v2.md)來處理訂用帳戶變更和其他管理工作。<br><br>此步驟需要存取權杖，如處理步驟3所述。 | 必要 |
| 6. 使用計量付費定價時，發行者會發出使用事件給計量服務 API。 | 如果您的 SaaS 應用程式具有以使用量為基礎的計費，請透過 [Marketplace 計量服務 api](./partner-center-portal/marketplace-metering-service-apis.md)來提供使用通知。<br><br>此步驟需要存取權杖，如步驟3所述。 | 需要進行計量 |
||||

## <a name="process-steps-for-user-management"></a>使用者管理的處理步驟

下圖顯示使用者管理的三個處理步驟。

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-7-9.png" alt-text="說明購買管理、訂用帳戶管理和選擇性的使用者管理程式步驟。":::

處理步驟7到9是選擇性的使用者管理程式步驟。 它們為支援 Azure AD 單一登入 (SSO) 的發行者提供額外的好處。 下表說明使用者管理程式步驟的詳細資料。

| 處理步驟 | 發行者動作 | 發行者的建議或必要 |
| ------------ | ------------- | ------------- |
| 7. 買方公司的 Azure AD 系統管理員可以選擇透過 Azure AD 來管理使用者和群組的存取權。 | 如果已為使用者設定 Azure AD SSO (步驟 9) ，則不需要任何發行者動作即可啟用此功能。 | 不適用 |
| 8. Azure AD 布建服務會在 Azure AD 和發行者的 SaaS 應用程式之間，傳達變更。 | [執行 SCIM 端點](../active-directory/app-provisioning/use-scim-to-provision-users-and-groups.md) ，以便在新增和移除使用者時從 Azure AD 接收更新。 | 建議 |
| 9. 在許可和布建應用程式之後，買方公司的使用者可以使用 Azure AD SSO 來登入發行者的 SaaS 應用程式。 | [使用 AZURE AD SSO](../active-directory/manage-apps/what-is-single-sign-on.md) 可讓使用者使用一個帳戶登入發行者的 SaaS 應用程式。 | 建議 |
||||

## <a name="next-steps"></a>後續步驟

- [在商業市場中建立可交易 SaaS 供應專案的登陸頁面](azure-ad-transactable-saas-landing-page.md)
- [在商業市場中建立免費或試用 SaaS 供應專案的登陸頁面](azure-ad-free-or-trial-landing-page.md)
- [如何在商業市場中建立 SaaS 供應專案](create-new-saas-offer.md)