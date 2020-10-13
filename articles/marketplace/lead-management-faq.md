---
title: 潛在客戶管理問題與疑難排解-Microsoft 合作夥伴中心
description: 瞭解在合作夥伴中心中設定商用 marketplace 潛在客戶時的常見錯誤和問題
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 10/01/2020
ms.openlocfilehash: b88c5d7692efa64349f9dbb01b2d4645ec0eb366
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91654143"
---
# <a name="common-questions-and-troubleshooting-for-lead-configuration"></a>潛在客戶設定的常見問題和疑難排解

本文針對您的商業 marketplace 供應專案，提供潛在客戶管理的一些常見問題解答。 它也可解決您在合作夥伴中心中設定潛在客戶關係管理 (CRM) 系統時可能遇到的錯誤。

## <a name="common-questions-about-lead-management"></a>潛在客戶管理的常見問題

#### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>可以在哪裡取得設定我潛在客戶目的地的說明？

如需如何將您的 CRM 系統連線至商用 marketplace 供應專案的總覽，請參閱 [您的商業 marketplace 供應專案的客戶潛在客戶](partner-center-portal/commercial-marketplace-get-customer-leads.md) 。 如果您有錯誤，請參閱以下的疑難排解指引。 如需其他支援，請透過合作夥伴中心說明 [及支援](https://aka.ms/marketplacepublishersupport)提交支援票證。 然後選取 [供應項目建立]  >  [您的供應項目類型]  >  [潛在客戶管理設定]。

#### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-in-the-commercial-marketplace"></a>我是否必須設定潛在客戶目的地，才能在商業市集中發佈供應項目？

答案取決於您要發佈的供應項目類型。 軟體即服務 (SaaS) 和 Dynamics 365 Customer Engagement 使用**聯絡我**來列出所有 Dynamics 365 的財務和營運供應項目、所有 Dynamics 365 Business Central 供應項目、所有諮詢服務供應項目。 因此，其需要連線到潛在客戶目的地。 如果未列出您的供應項目類型，則不需要連線到潛在客戶目的地。 我們建議您設定潛在客戶目的地，讓您不會錯過商機。

#### <a name="how-can-i-find-the-test-lead"></a>如何找到測試潛在客戶？

在您的潛在客戶目的地中搜尋 `"MSFT_TEST"`。 以下是 Microsoft 所提供的範例測試潛在客戶。 請注意，測試潛在客戶的格式會根據潛在客戶目的地而有所不同。

```
{
    "UserDetails": {
      "FirstName": "MSFT_TEST_636573304831318844",
      "LastName": "MSFT_TEST_636573304831318844",
      "Email": "MSFT_TEST_636573304831318844@test.com",
      "Phone": "1234567890",
      "Country": "US",
      "Company": "MSFT_TEST_636573304831318844",
      "Title": "MSFT_TEST_636573304831318844"
    },
    "LeadSource": "AzureMarketplace",
    "ActionCode": "INS",
    "OfferTitle": "Contoso Test"
    "Description": "MSFT_TEST_636573304831318844"
}
```

#### <a name="i-have-a-live-offer-but-why-am-i-not-seeing-any-leads"></a>我有作用中的供應項目，但為什麼沒看到任何潛在客戶？

請確定您的潛在客戶目的地連線有效。 您在 [合作夥伴中心] 中選取 [發佈] 您的供應項目後，我們會將測試潛在客戶傳送給您。 如果您看到測試潛在客戶，表示連線有效。 您也可以嘗試在預覽步驟中取得供應項目預覽，以測試您的潛在客戶連線。 選取商業市集中列出的 [立即取得]、[與我聯繫] 或 [免費試用]。

此外，請確定您尋找的是正確的資料。 如需我們傳送給潛在客戶目的地的潛在客戶資料說明，請參閱 [瞭解潛在客戶資料](partner-center-portal/commercial-marketplace-get-customer-leads.md) 。

#### <a name="i-configured-azure-blob-storage-as-my-lead-destination-but-why-dont-i-see-the-lead"></a>我已將 Azure Blob 儲存體設定為我的潛在客戶目的地，為何沒有看到潛在客戶？

Azure Blob 儲存體已不再支援做為潛在客戶目的地，因此您會遺失您的供應項目所產生的任何潛在客戶。 切換至任何其他[潛在客戶目的地選項](partner-center-portal/commercial-marketplace-get-customer-leads.md)。 

#### <a name="i-received-an-email-from-the-commercial-marketplace-but-why-cant-i-find-the-lead-in-my-crm"></a>我收到來自商業市集的電子郵件，但為什麼在我的 CRM 中找不到潛在客戶？

終端使用者的電子郵件網域可能來自 .edu。 基於隱私權考慮，我們不會從 .edu 網域傳遞個人資訊。 請透過[合作夥伴中心說明及支援](https://aka.ms/marketplacepublishersupport)提交支援票證。

#### <a name="i-configured-an-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>我已將 Azure 資料表設定為我的潛在客戶目的地。 如何才能檢視潛在客戶？

您可以從 Azure 入口網站存取儲存在 Azure 資料表中的潛在客戶資料。 您也可以免費下載並安裝 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)，以檢視 Azure 儲存體帳戶的資料表資料。

#### <a name="i-configured-an-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-commercial-marketplace-lead-is-sent"></a>我已將 Azure 資料表設定為我的潛在客戶目的地。 是否可以在每次傳送新的商業市集潛在客戶時收到通知？

是。 依照 [使用 Azure 資料表來設定潛在客戶管理](partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md)中的指示設定 Microsoft 流程，在潛在客戶新增至 Azure 資料表時傳送電子郵件。

#### <a name="i-configured-salesforce-as-my-lead-destination-but-why-cant-i-find-the-leads"></a>我已設定 Salesforce 做為我的潛在客戶目的地，但為什麼找不到潛在客戶？

根據挑選清單檢查潛在客戶表單的 Web 是否為必要欄位。 如果是，請將該欄位切換為必要文字欄位。

#### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>我的潛在客戶目的地發生問題，我遺漏了一些潛在客戶。 是否可以在電子郵件中將這些潛在客戶傳送給我？

由於個人資訊原則的緣故，我們無法透過不安全的電子郵件分享潛在客戶資訊。

#### <a name="i-configured-an-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>我已將 Azure 資料表設定為我的潛在客戶目的地。 需要花費多少成本？

潛在客戶產生資料很少。 幾乎對所有發行者來說都小於 1 GB。 成本取決於收到的潛在客戶數目。 例如，如果一個月收到 1000 個潛在客戶，則成本大約是 50 美分。 如需儲存體定價的詳細資訊，請參閱 [Azure 儲存體概觀定價](https://azure.microsoft.com/pricing/details/storage/)。

如果您的問題未獲得解答，請透過[合作夥伴中心說明及支援](https://aka.ms/marketplacepublishersupport)聯絡 Microsoft 支援服務。 然後選取 [供應項目建立]  >  [您的供應項目類型]  >  [潛在客戶管理設定]。

#### <a name="im-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-someone-else-to-receive-these-emails"></a>當我收到新的潛在客戶時，也會收到電子郵件通知。 如何設定其他人收到這些電子郵件？

在合作夥伴中心存取您的供應項目，移至 [供應項目設定] 頁面 > [潛在客戶管理]  >  [編輯]。 更新 [連絡人電子郵件] 欄位底下的電子郵件地址。

## <a name="troubleshooting-lead-configuration-errors"></a><a id="publishing-config-errors"></a> 針對潛在客戶設定錯誤進行疑難排解

**無法將潛在客戶儲存至 Dynamics CRM。請檢查 Dynamics CRM 帳戶設定。LastCRMError：無法登入 Dynamics CRM、LastCRMException：** 

> 如果選取了 Microsoft 365 authentication，請檢查使用者帳戶和密碼是否有效。 如果選取了 Azure Active Directory，請檢查租使用者識別碼、應用程式識別碼和應用程式秘密金鑰是否符合 Azure Active Directory 上設定的專案。 遵循[這裡](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)的指示。 如果帳戶使用者名稱/密碼有效，請確定其可以存取 Dynamics 365，並已指派授權 (如果是使用 Azure Active Directory，請參考步驟 11-15，如果使用 Office 使用者，請參考安全性設定)。 

**無法將潛在客戶儲存至 Dynamics CRM。使用者沒有在潛在客戶實體中建立 leadsourcecode 屬性的權限** 

> 應用程式/使用者遺失 Microsoft Marketplace 潛在客戶寫入器的安全性角色。 如果使用 Azure Active Directory，請遵循步驟11-15，如果 [在此處](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)使用 Office 使用者，請遵循安全性設定。

**無法使用 AAD 將潛在客戶儲存至 Dynamics CRM。例外狀況：找不到租用戶。如果沒有任何作用中的租用戶訂用帳戶，可能會發生這個情況。**  

> 潛在客戶管理一節中提供的目錄識別碼不是有效的目錄。 請根據 Azure Active Directory) [此處](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)的步驟 2 (中的指示取得目錄識別碼。

**無法將潛在客戶儲存至 Dynamics CRM。LastCRMError：SecLib::RetrievePrivilegeForUser 失敗 - 沒有指派任何角色給使用者。**  

> 解決方案：將安全性角色指派給 Microsoft Marketplace 潛在客戶寫入器。 請依照[此處](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)安全性設定下的指示。

**無法使用 AAD 將潛在客戶儲存至 Dynamics CRM。例外狀況：在目錄中找不到包含識別碼的應用程式** 

> 潛在客戶管理一節中提供的應用程式識別碼不是有效的目錄。 請根據[此處](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)步驟 8 的指示取得目錄識別碼 (在 Azure Active Directory 下)。 

**無法使用 AAD 將潛在客戶儲存至 Dynamics CRM。例外狀況：所要求的租用戶識別碼無效，且不是有效的外部網域格式** 

> 潛在客戶管理一節中提供的目錄識別碼不是有效的目錄。 請根據[此處](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)步驟 2 中的指示取得目錄識別碼 (在 Azure Active Directory 下)。 

**無法使用 AAD 將潛在客戶儲存至 Dynamics CRM。例外狀況：驗證認證時發生錯誤：提供的用戶端祕密無效。** 

> 解決方案：登入 Azure 入口網站，檢查應用程式金鑰是否符合合作夥伴中心的內容。 請根據[此處](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)步驟 10 中的指示產生密碼 (在 Azure Active Directory 下)。 

**無法將潛在客戶儲存至 Dynamics CRM。LastCRMError：等待回覆至 00:02:00 之後，要求通道逾時。提高傳遞至呼叫要求的逾時值，或增加繫結上的 SendTimeout 值。分配給此作業的時間可能是較長逾時的一部分。**  

> 解決方式：登入合作夥伴中心，檢查供應專案設定 >> 客戶潛在客戶 >> URL，檢查它是否為有效的動態 CRM 實例。

