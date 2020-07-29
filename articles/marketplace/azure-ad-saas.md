---
title: 在商業 marketplace 中 Azure Active Directory 和 transactable SaaS 供應專案
description: 瞭解 Azure Active Directory 如何與 Microsoft 商業 marketplace 中的 transactable SaaS 供應專案搭配運作。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/10/2020
ms.openlocfilehash: 0d8e2ee684bc08ec23e052229d50b7e9d62c0ecb
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87328213"
---
# <a name="azure-ad-and-transactable-saas-offers-in-the-commercial-marketplace"></a>在商業 marketplace 中 Azure AD 和 transactable SaaS 供應專案

Microsoft 雲端式身分識別和存取管理服務， [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) （Azure AD）可協助使用者登入及存取內部和外部資源。 在 Microsoft 商業 marketplace 中，Azure AD 讓 transactable SaaS 針對所有人（包括發行者、買家和使用者）提供更輕鬆且更安全的服務。 藉由 Azure AD，發行者可以自動將使用者布建到其軟體即服務（SaaS）應用程式，而買家本身則可以管理這些已布建的使用者。 

此外，當使用者登入 Azure AD 中的應用程式時， [Azure AD 單一登入](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)（SSO）可提供安全性和便利性。 更快速的互動和優化體驗也可讓購買者和使用者安心與發行者的 SaaS 應用程式進行第一次互動。 這會產生正面的印象，讓您建立可見度並鼓勵重複的業務。

遵循本文中的指導方針，您將有助於在商業市場中認證您的 SaaS 供應專案。 如需認證的詳細資訊，請閱讀詳細的[商業 marketplace 認證原則](https://aka.ms/commercial-marketplace-certification-policies#100-general)，包括[SaaS](https://aka.ms/commercial-marketplace-certification-policies#1000-software-as-a-service-saas)專屬的憑證。

## <a name="before-you-begin"></a>開始之前

當您在合作夥伴中心[建立 SaaS 供應](./partner-center-portal/create-new-saas-offer.md)專案時，您可以從一組特定的動作選項呼叫中，選擇要在供應專案清單上顯示的專案。 您的選擇會決定您的供應專案在商業 marketplace 中的交易方式。 透過 Microsoft 銷售的優惠稱為 transactable 優惠。 我們會代表您向客戶收取所有 transactable 供應專案的費用。 如果您選擇透過 Microsoft 銷售，並代表您裝載交易（[**是]** 選項），則您已選擇建立 transactable 供應專案，而本文適用于您。 我們建議您完整閱讀。

如果您選擇只透過「商業 marketplace」列出您的供應專案，並獨立處理交易（**無**選項），則有三個選項可讓潛在客戶存取您的供應專案：立即取得（免費）、免費試用及聯絡我。 如果您選取 **[立即取得（免費）** ] 或 [**免費試用**]，則不會為您提供這篇文章。 相反地，請參閱在[商業市場中建立免費或試用版 SaaS 供應專案的登陸頁面](./azure-ad-free-or-trial-landing-page.md)，以取得詳細資訊。 如果您選取 [**聯絡我**]，就不會有任何直接的發行者責任。 繼續在合作夥伴中心建立您的供應專案。

## <a name="how-azure-ad-works-with-the-commercial-marketplace-for-saas-offers"></a>Azure AD 如何與適用于 SaaS 供應專案的商用 marketplace 搭配運作

Azure AD 可以順暢地購買、履行和管理商業 marketplace 解決方案。 [圖 1] 顯示「發行者」、「買方」和「使用者」的互動方式，以購買及啟用訂用帳戶。 它也會顯示客戶如何使用和管理從商業 marketplace 取得的 SaaS 應用程式。 基於此圖例的目的，買方是起始從商用 marketplace 購買的 SaaS 應用程式使用者。

如 [圖 1] 所示，當買方選取您的供應專案時，他們會開始一系列包含購買、訂閱和使用者管理的工作流程。 在此連鎖中，您的發行者會負責特定需求，而 Microsoft 會在關鍵要點提供支援。

***圖1：在商業 marketplace 中使用適用于 SaaS 供應專案的 Azure AD***

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow.png" alt-text="說明「購買管理」、「訂用帳戶管理」和選擇性的「使用者管理」程式步驟。":::

下列各節提供有關每個處理步驟需求的詳細資料。

## <a name="process-steps-for-purchase-management"></a>購買管理的處理步驟

此圖顯示購買管理的四個處理步驟。

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-1-4.png" alt-text="說明購買管理的四個處理步驟。":::

下表提供「購買管理」程式步驟的詳細資料。

| 進程步驟 | 發行者動作 | 發行者的建議或必要 |
| ------------ | ------------- | ------------- |
| 1. 購買者使用其 Azure 識別碼身分識別登入商用 marketplace，並選取 SaaS 供應專案。 | 不需要任何發行者動作。 | 不適用 |
| 2. 購買後，買方會在 Azure Marketplace 中選取 [**設定帳戶**]，或在 AppSource 中**設定 [立即**]，這會將買方導向此供應專案的發行者登陸頁面。 購買者必須能夠使用 Azure AD SSO 登入發行者的 SaaS 應用程式，而且只會要求您提供不需要 Azure AD 系統管理員核准的最低同意。 | 設計供應專案的[登陸頁面](azure-ad-transactable-saas-landing-page.md)，讓它接收具有其 Azure AD 或 MICROSOFT 帳戶（MSA）身分識別的使用者，並協助所需的任何額外布建或設定。 | 必要 |
| 3. 發行者會向 SaaS 履行 API 要求購買詳細資料。 | 使用從登陸頁面的應用程式識別碼產生的[存取權杖](./partner-center-portal/pc-saas-registration.md)，[呼叫解析端點](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription)來抓取有關購買的詳細資訊。 | 必要 |
| 4. 透過 Azure AD 和 Microsoft Graph API，發行者會收集在發行者的 SaaS 應用程式中布建買方所需的公司和使用者詳細資料。  | 分解 Azure AD 的使用者權杖以尋找名稱和電子郵件，或[呼叫 MICROSOFT GRAPH API](https://docs.microsoft.com/graph/use-the-api) ，並使用委派的許可權來抓取已登入使用者的相關[資訊](https://docs.microsoft.com/graph/api/user-get)。 | 必要 |
||||

## <a name="process-steps-for-subscription-management"></a>訂用帳戶管理的處理步驟

此圖顯示兩個訂用帳戶管理的處理步驟。

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-5-6.png" alt-text="說明兩個訂用帳戶管理的處理步驟。":::

下表描述訂用帳戶管理程式步驟的詳細資料。

| 進程步驟 | 發行者動作 | 發行者的建議或必要 |
| ------------ | ------------- | ------------- |
| 5. 發行者透過 SaaS 履行 API 來管理 SaaS 應用程式的訂用帳戶。 | 透過[SaaS 履行 api](./partner-center-portal/pc-saas-fulfillment-api-v2.md)處理訂用帳戶變更和其他管理工作。<br><br>此步驟需要存取權杖，如處理步驟3中所述。 | 必要 |
| 6. 使用計量付費定價時，發行者會發出使用事件給計量服務 API。 | 如果您的 SaaS 應用程式具有以使用量為基礎的計費，請透過[Marketplace 計量服務 api](./partner-center-portal/marketplace-metering-service-apis.md)來進行使用量通知。<br><br>此步驟需要存取權杖，如步驟3中所述。 | 計量的必要 |
||||

## <a name="process-steps-for-user-management"></a>使用者管理的處理步驟

下圖顯示使用者管理的三個處理步驟。

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-7-9.png" alt-text="說明使用者管理的三個選擇性處理步驟。":::

處理步驟7到9是選擇性的使用者管理程式步驟。 它們可為支援 Azure AD 單一登入（SSO）的發行者提供額外的權益。 下表描述有關使用者管理程式步驟的詳細資料。

| 進程步驟 | 發行者動作 | 發行者的建議或必要 |
| ------------ | ------------- | ------------- |
| 7. 購買者公司 Azure AD 系統管理員可以選擇性地透過 Azure AD 管理使用者和群組的存取權。 | 如果為使用者設定 Azure AD SSO，則不需要發行者動作來啟用此操作（步驟9）。 | 不適用 |
| 8. Azure AD 布建服務會在 Azure AD 和發行者的 SaaS 應用程式之間傳達變更。 | [執行 SCIM 端點](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups)，以便在新增和移除使用者時，從 Azure AD 接收更新。 | 建議 |
| 9. 在許可和布建應用程式之後，買方公司的使用者可以使用 Azure AD SSO 來登入發行者的 SaaS 應用程式。 | [使用 AZURE AD SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) ，讓使用者可以使用一個帳戶，登入發行者的 SaaS 應用程式一次。 | 建議 |
||||

## <a name="next-steps"></a>後續步驟

- [在商業 marketplace 中建立 SaaS 供應專案](./partner-center-portal/create-new-saas-offer.md)
- [在商業 marketplace 中建立 transactable SaaS 供應專案的登陸頁面](./azure-ad-transactable-saas-landing-page.md)
