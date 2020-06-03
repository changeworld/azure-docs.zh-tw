---
title: Azure EA 入口網站系統管理員
description: 本文將說明系統管理員在 Azure EA 入口網站中完成的一般工作。
author: bandersmsft
ms.author: banders
ms.date: 06/01/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: a2430e29acb83945e9893ca4c6e2e0110ef6e007
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/02/2020
ms.locfileid: "84298154"
---
# <a name="azure-ea-portal-administration"></a>Azure EA 入口網站系統管理員

本文將說明系統管理員在 Azure EA 入口網站 (https://ea.azure.com) 中完成的一般工作。 Azure EA 入口網站是一個線上管理入口網站，可協助客戶管理其 Azure EA 服務的成本。 如需 Azure EA 入口網站的簡介資訊，請參閱[開始使用 Azure EA 入口網站](ea-portal-get-started.md)一文。

## <a name="associate-an-account-to-a-department"></a>將帳戶與部門建立關聯

企業系統管理員可將註冊下的現有帳戶與部門產生關聯。

### <a name="to-associate-an-account-to-a-department"></a>將帳戶與部門產生關聯

1. 以企業系統管理員身分登入 Azure EA 入口網站。
1. 選取左側導覽列上的 [管理]。
1. 選取 [部門]。
1. 將滑鼠停留在包含帳戶的資料列上方，然後選取右側的鉛筆圖示。
1. 從下拉式功能表中選取部門。
1. 選取 [儲存]。

## <a name="department-spending-quotas"></a>部門支出配額

EA 客戶可為註冊下的每個部門設定或變更支出配額。 系統會針對目前的承諾用量期間設定支出配額金額。 在目前的承諾用量期間結束時，系統會將現有的支出配額延長到下一個承諾用量期間，除非值有所更新。

部門系統管理員可檢視支出配額，但只有企業系統管理員可更新配額金額。 當配額達到 50%、75%、90% 和 100% 時，企業系統管理員和部門系統管理員將會收到通知。

### <a name="enterprise-administrator-to-set-the-quota"></a>企業系統管理員若要設定配額：

 1. 開啟 Azure EA 入口網站。
 1. 選取左側導覽列上的 [管理]。
 1. 選取 [部門] 索引標籤。
 1. 選取 [部門]。
 1. 選取 [部門詳細資料] 區段上的鉛筆符號，或按一下 [+ 新增部門] 符號，以新增支出配額和新部門。
 1. 在 [部門詳細資料] 下的 [支出配額金額] 方塊中，以註冊的貨幣輸入支出配額金額 (必須大於 0)。
    - [部門名稱] 和 [成本中心] 也可在此時編輯。
 1. 選取 [儲存]。

部門的支出配額此時會顯示在 [部門清單] 檢視中的 [部門] 索引標籤下。在目前的承諾用量期間結束時，Azure EA 入口網站將會維護下一個承諾用量期限的支出配額。

部門配額金額獨立於目前的承諾用量金額外，且配額金額和警示僅適用於第一方使用量。 部門支出配額僅供參考之用，不會強制執行支出限制。

### <a name="department-administrator-to-view-the-quota"></a>部門系統管理員若要檢視配額：

1. 開啟 Azure EA 入口網站。
1. 選取左側導覽列上的 [管理]。
1. 選取 [部門] 索引標籤，並檢視含有支出配額的 [部門清單] 檢視。

如果您是間接客戶，則必須由通路合作夥伴為您啟用成本功能。

## <a name="enterprise-user-roles"></a>企業使用者角色

Azure EA 入口網站可協助您管理您的 Azure EA 成本和使用量。 Azure EA 入口網站中有三個主要角色：

- EA 系統管理員
- 部門系統管理員
- 帳戶擁有者

每個角色都有不同層級的存取權和授權。

如需使用者角色的詳細資訊，請參閱[企業使用者角色](https://docs.microsoft.com/azure/billing/billing-ea-portal-get-started#enterprise-user-roles)。

## <a name="add-an-azure-ea-account"></a>新增 Azure EA 帳戶

Azure EA 帳戶是 Azure EA 入口網站中的組織單位。 其可用來管理訂用帳戶，也可用來進行報告。 若要存取及使用 Azure 服務，您需要建立一個帳戶或讓人為您建立一個帳戶。

如需有關 Azure 帳戶的詳細資訊，請參閱＜新增帳戶＞。

## <a name="enterprise-devtest-offer"></a>Enterprise 開發/測試供應項目

身為 Azure 企業系統管理員，您可以讓組織中的帳戶擁有者根據 EA 開發/測試供應項目建立訂用帳戶。 若要這麼做，請在 Azure EA 入口網站中選取帳戶擁有者的 [開發/測試] 方塊。

勾選 [開發/測試] 方塊後，請讓帳戶擁有者得知，他們才能夠設定其開發/測試訂閱者的小組所需的 EA 開發/測試訂用帳戶。

此供應項目可讓有效的 Visual Studio 訂閱者以特殊的開發/測試費率，在 Azure 上執行開發和測試工作負載。 其可供存取完整的開發/測試映像資源庫，包括 Windows 8.1 和 Windows 10。

### <a name="to-set-up-the-enterprise-devtest-offer"></a>若要設定 Enterprise 開發/測試供應項目：

1. 以企業系統管理員身分登入。
1. 選取左側導覽列上的 [管理]。
1. 選取 [帳戶] 索引標籤。
1. 選取您要啟用開發/測試存取之帳戶的資料列。
1. 選取資料列右側的鉛筆符號。
1. 選取 [開發/測試] 核取方塊。
1. 選取 [儲存]。

當使用者透過 Azure EA 入口網站新增為帳戶擁有者時，任何與該帳戶擁有者相關聯，且以 PAYG 開發/測試供應項目或 Visual Studio 訂閱者的每月點數供應項目為基礎的 Azure 訂用帳戶，都會轉換為 EA 開發/測試供應項目。 以其他供應項目類型 (例如 PAYG) 為基礎的訂用帳戶，將會轉換為 Microsoft Azure 企業版供應項目。

開發/測試供應項目目前不適用於 Azure Gov 客戶。

## <a name="delete-subscription"></a>刪除訂用帳戶

若要刪除以您作為帳戶擁有者的訂用帳戶：

1. 使用與您的帳戶相關聯的認證登入 Azure 入口網站。
1. 在 [中樞] 功能表中，選取 [訂用帳戶]。
1. 在頁面左上角的 [訂用帳戶] 索引標籤中，選取您要取消的訂用帳戶，然後選取 [取消訂用帳戶] 以啟動 [取消] 索引標籤。
1. 輸入訂用帳戶名稱並選擇取消原因，然後選取 [取消訂用帳戶] 按鈕。

只有帳戶管理員可以取消訂用帳戶。

如需詳細資訊，請參閱[取消訂用帳戶之後會發生什麼情況？](cancel-azure-subscription.md#what-happens-after-i-cancel-my-subscription)。

## <a name="delete-an-account"></a>刪除帳戶

只有作用中的帳戶沒有作用中的訂用帳戶時，才能完成帳戶移除。

1. 在企業版入口網站中，選取左側導覽區中的 [管理]。
1. 選取 [帳戶] 索引標籤。
1. 從 [帳戶] 資料表中，選取您要刪除的帳戶。
1. 選取 [帳戶] 資料列右側的 X 符號。
1. 在帳戶下沒有作用中的訂用帳戶後，選取 [帳戶] 資料列下的 [是]，以確認帳戶已移除。

## <a name="update-notification-settings"></a>更新通知設定

企業系統管理員會自動註冊，以接收與其註冊相關聯的使用通知。 每個企業系統管理員都可以變更個別通知的間隔，或完全關閉這些通知。

通知連絡人會顯示在 Azure EA 入口網站中的 [通知連絡人] 區段。 管理通知連絡人可確保組織中的適當人員會收到 Azure EA 通知。

若要檢視目前的通知設定：

1. 在 Azure EA 入口網站中，瀏覽至 [管理] > [通知連絡人]。
2. 電子郵件地址 – 可接收通知且與企業系統管理員的 Microsoft 帳戶、公司或學校帳戶相關聯的電子郵件地址。
3. 未開立帳單餘額通知頻率 – 傳送給每位個別企業系統管理員的通知頻率。

若要新增連絡人：

1. 選取 [+ 新增連絡人]。
2. 輸入電子郵件地址，然後確認。
3. 選取 [儲存]。

新的通知連絡人會顯示在 [通知連絡人] 區段中。 若要變更通知頻率，請選取通知連絡人，然後選取所選資料列右側的鉛筆符號。 將頻率設定為**每日**、**每週**、**每月**或**無**。

您可以隱藏「逼近範圍期間的結束日期」及「停用和取消佈建日期逼近」的生命週期通知。 停用生命週期通知會隱藏範圍期間和合約結束日期的相關通知。

## <a name="azure-sponsorship-offer"></a>Azure 贊助供應項目

Azure 贊助供應項目是有贊助限制的 Microsoft Azure 帳戶。 只有 Microsoft 所選的限定客戶，才能收到電子郵件邀請。 若您獲選參加 Microsoft Azure 贊助供應項目，您將會收到屬於您帳戶識別碼的電子郵件邀請。

如需詳細資訊，請建立[贊助啟用的支援要求](https://aka.ms/azrsponsorship)。

## <a name="conversion-to-work-or-school-account-authentication"></a>轉換為公司或學校帳戶驗證

Azure 企業版使用者可以從 Microsoft 帳戶 (MSA 或 Live ID) 轉換為工作或學校帳戶 (使用 Azure 中的 Active Directory) 驗證類型。

開始：

1. 將公司或學校帳戶新增至 Azure EA 入口網站中所需的角色。
1. 如果發生錯誤，表示該帳戶在 Active Directory 中可能無效。  Azure 會使用使用者主體名稱 (UPN)，此名稱不一定會與電子郵件地址相同。
1. 使用公司或學校帳戶對 Azure EA 入口網站進行驗證。

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>若要將訂用帳戶從 Microsoft 帳戶轉換為公司或學校帳戶：

1. 使用擁有訂用帳戶的 Microsoft 帳戶登入管理入口網站。
1. 使用帳戶擁有權轉移功能移至新帳戶。
1. 現在，Microsoft 帳戶應可供任何作用中的訂用帳戶使用，且可以刪除。
1. 任何已刪除的帳戶都會在入口網站中保持非作用中狀態，以供歷史帳單使用。  您可以選取核取方塊以將其從檢視中篩選掉，而僅顯示作用中的帳戶。

## <a name="account-subscription-ownership-faq"></a>帳戶訂用帳戶擁有權常見問題集

本文件將解答帳戶訂用帳戶擁有權的相關常見問題。

### <a name="how-many-azure-account-owners-can-you-have-per-subscription"></a>您的每個訂用帳戶可以有多少個 Azure 帳戶擁有者？

每個訂用帳戶只能有一個帳戶擁有者。  您可以在 [portal.azure.com] (https://portal.azure.com) 頁面左上角的 [訂用帳戶] 索引標籤中，使用角色型存取或 (存取控制 (IAM)) 來新增其他角色。

### <a name="can-an-azure-account-owner-be-listed-under-more-than-one-department"></a>一個 Azure 帳戶擁有者是否可列在多個部門下？

否，一個帳戶擁有者只能與單一部門相關聯。 此原則有助於確保在 Azure EA 入口網站中，能夠正確地監視和分配該擁有者在 EA 註冊下對應的部門所產生的相關成本和支出。

### <a name="can-an-azure-account-owner-be-listed-as-a-security-group"></a>Azure 帳戶擁有者是否可以列為安全性群組？

否，訂用帳戶擁有者必須是唯一的 Microsoft 帳戶 (MSA) 或 Azure Active Directory (AAD) 驗證。 若要顧及組織內的延續性，您可以考慮建立一般帳戶，並使用 AAD 來管理訂用帳戶存取。

### <a name="can-an-individual-user-own-multiple-subscriptions"></a>個別使用者可以擁有多個訂用帳戶嗎？

Azure 帳戶擁有者可以建立及管理不限數量的訂用帳戶。

### <a name="how-can-i-accessview-all-my-organizations-subscriptions"></a>如何存取/檢視組織的所有訂用帳戶？

目前，這必須使用原則來完成；也就是說，對於每個建立的訂用帳戶，您都必須要求使用角色型存取將您的帳戶新增至訂用帳戶角色。

### <a name="where-do-i-go-to-create-a-subscription"></a>我應在何處建立訂用帳戶？

EA 註冊的系統管理員必須先將您的帳戶新增至 Azure EA 入口網站中的帳戶擁有者角色，您才能建立 Enterprise Azure (EA) 供應項目訂用帳戶。 接著，您必須登入 Azure EA 入口網站，以取得建立 EA 供應項目類型訂用帳戶的權利。 建議您使用 EA 入口網站，從 [訂用帳戶] 索引標籤中的 [+ 新增訂用帳戶] 連結建立第一個 EA 訂用帳戶。  不過，在您的帳戶獲得授權後，經由 portal.azure.com，在頁面左上角的 [訂用帳戶] 索引標籤中建立訂用帳戶，可能會比較容易，因為在此處您可以用單一步驟建立及重新命名您的訂用帳戶。

### <a name="who-can-create-a-subscription"></a>誰可以建立訂用帳戶？

若要建立 Enterprise Azure 供應項目類型訂用帳戶，您必須獲得 [EA 入口網站](https://ea.azure.com)上的帳戶擁有者角色。

## <a name="next-steps"></a>後續步驟

- 了解[虛擬機器保留](ea-portal-vm-reservations.md)如何協助您節省成本。
- 如果您需要 Azure EA 入口網站問題的疑難排解協助，請參閱[針對 Azure EA 入口網站的存取進行疑難排解](ea-portal-troubleshoot.md)。
