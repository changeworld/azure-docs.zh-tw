---
title: Azure EA 入口網站系統管理員
description: 本文將說明系統管理員在 Azure EA 入口網站中完成的一般工作。
author: bandersmsft
ms.author: banders
ms.date: 11/13/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.custom: contperfq1
ms.openlocfilehash: bb8a52a2258143e1c68ca46ac31a8c986b1bcfe7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96457128"
---
# <a name="azure-ea-portal-administration"></a>Azure EA 入口網站系統管理員

本文將說明系統管理員在 Azure EA 入口網站 (https://ea.azure.com) 中完成的一般工作。 Azure EA 入口網站是一個線上管理入口網站，可協助客戶管理其 Azure EA 服務的成本。 如需 Azure EA 入口網站的簡介資訊，請參閱[開始使用 Azure EA 入口網站](ea-portal-get-started.md)一文。

## <a name="activate-your-enrollment"></a>啟動您的註冊

若要啟動您的服務，最初的企業系統管理員需從邀請電子郵件開啟 [Azure 企業版入口網站](https://ea.azure.com)，並使用電子郵件地址登入。

如果您已設定為企業管理員，則不需要接收啟用電子郵件。 移至 [Azure 企業版入口網站](https://ea.azure.com)，然後使用您的公司、學校或 Microsoft 帳戶電子郵件地址和密碼登入。

如果您有多個註冊，請選擇一個進行啟用。 預設只會顯示作用中的註冊。 若要檢視註冊記錄，請清除 Azure 企業版入口網站右上方的 [作用中] 選項。

在 [註冊] 底下，狀態會顯示 [作用中]。

![顯示作用中註冊的範例](./media/ea-portal-administration/ea-enrollment-status.png)

只有現有的 Azure 企業系統管理員可以建立其他企業系統管理員。

### <a name="create-another-enterprise-administrator"></a>建立另一個企業系統管理員

若要新增另一個企業系統管理員：

1. 登入 [Azure 企業版入口網站](https://ea.azure.com)。
1. 移至 [管理] > [註冊詳細資料]。
1. 選取右上方的 [+ 新增系統管理員]。

請確定您有使用者的電子郵件地址和慣用的驗證方法，例如公司、學校或 Microsoft 帳戶。

如果您不是企業系統管理員，請洽詢企業系統管理員，讓他們將您新增至註冊。 新增至註冊之後，您會收到啟用電子郵件。

如果您的企業系統管理員無法協助您，請建立 [Azure 企業版入口網站支援要求](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c)。 請提供下列資訊：

- 註冊號碼
- 要新增的電子郵件地址及驗證類型 (公司、學校或 Microsoft 帳戶)
- 來自現有企業系統管理員的電子郵件核准
  - 如果沒有現有的企業系統管理員可用，請洽詢您的合作夥伴或軟體顧問，要求他們透過大量授權服務中心 (VLSC) 工具變更連絡人詳細資料。

## <a name="create-an-azure-enterprise-department"></a>建立 Azure 企業版部門

企業系統管理員和部門系統管理員會使用部門來進行組織，並且依據部門及成本中心報告企業 Azure 服務及使用量。 企業系統管理員可以：

- 新增或移除部門。
- 將帳戶與部門建立關聯。
- 建立部門系統管理員。
- 允許部門系統管理員檢視價格和成本。

部門系統管理員可以在其部門中新增帳戶。 他們可以從其部門移除帳戶，但不會從註冊中移除。

若要新增部門：

1. 登入 Azure 企業版入口網站。
1. 在左窗格中選取 [管理]。
1. 選取 [部門] 索引標籤，然後選取 [+ 新增部門]。
1. 輸入資訊。
   部門名稱是唯一的必要欄位。 至少必須有三個字元。
1. 完成時選取 [新增]。

## <a name="add-a-department-administrator"></a>新增部門系統管理員

建立部門之後，企業系統管理員可以新增部門系統管理員，並將其與部門產生關聯。 部門系統管理員可以為其部門執行下列動作：

- 建立其他部門系統管理員
- 檢視和編輯部門屬性，例如名稱或成本中心
- 新增帳戶
- 移除帳戶
- 下載使用量詳細資料
- 檢視每月使用量和費用 <sup>1</sup>

> <sup>1</sup> 企業系統管理員必須授與這些許可權。 如果您已獲得檢視部門每月使用量和費用的權限，卻看不到這些項目，請與您的合作夥伴連絡。

### <a name="to-add-a-department-administrator"></a>新增部門系統管理員

企業系統管理員：

1. 登入 Azure 企業版入口網站。
1. 在左窗格中選取 [管理]。
1. 選取 [部門] 索引標籤，然後選取部門。
1. 選取 [+ 新增系統管理員] 並新增必要資訊。
1. 如需唯讀存取權，請將 [唯讀] 選項設為 [是]，然後選取 [新增]。

![顯示 [新增部門系統管理員] 對話方塊的範例](./media/ea-portal-administration/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>若要設定唯讀存取權

您可以將唯讀存取權授與部門系統管理員。

- 當您建立新的部門系統管理員時，請將唯讀選項設為 [是]。

- 若要編輯現有的部門系統管理員：
   1. 選取部門，然後在您想要編輯的 [部門系統管理員] 旁邊，選取鉛筆符號。
   1. 將 [唯讀開啟] 設定為 [是]，然後選取 [儲存]。

企業系統管理員會自動取得部門系統管理員權限。

## <a name="add-an-account"></a>新增帳戶

帳戶和訂用帳戶的結構會影響其管理方式，以及在您發票和報告上顯示的方式。 一般組織結構的範例包括營業單位、功能小組和地理位置。

若要新增帳戶：

1. 在 Azure 企業版入口網站中，選取左側導覽區中的 [管理]。
1. 選取 [帳戶] 索引標籤。在 [帳戶] 頁面上，選取 [+ 新增帳戶]。
1. 選取部門，或將其保留為 [未指派]，然後選取所需的驗證類型。
1. 輸入自訂名稱以識別報告中的帳戶。
1. 輸入要與新帳戶產生關聯的 [帳戶擁有者電子郵件] 地址。
1. 確認電子郵件地址，然後選取 [新增]。

![顯示帳戶清單和 [+ 新增帳戶] 選項的範例](./media/ea-portal-administration/create-ea-add-an-account.png)

若要新增其他帳戶，請選取 [新增其他帳戶]，或選取左側工具列右下角的 [新增]。

若要確認帳戶擁有權：

1. 登入 Azure 企業版入口網站。
1. 檢視狀態。

   狀態應該會從 [擱置] 變更為 [開始/結束日期]。 開始/結束日期是使用者第一次登入的日期和合約結束日期。
1. 出現 [警告] 訊息時，帳戶擁有者必須選取 [繼續]，才能在第一次登入 Azure 企業版入口網站時啟用帳戶。

## <a name="change-azure-subscription-or-account-ownership"></a>變更 Azure 訂用帳戶或帳戶擁有權

企業系統管理員可以使用 Azure 企業版入口網站來轉移註冊中所選或所有訂用帳戶的帳戶擁有權。

當您完成訂用帳戶或帳戶所有權轉移時，Microsoft 會更新帳戶擁有者。

在執行擁有權轉移之前，請先了解這些 Azure 角色型存取控制 (Azure RBAC) 原則：

- 當您在相同租用戶中的兩個組織識別碼之間執行訂用帳戶或帳戶擁有權轉移時，Azure RBAC 原則、現有的服務管理員及共同管理員角色都會保留。
- 跨訂用帳戶或帳戶擁有權轉移會導致您的 Azure RBAC 原則和角色指派遺失。
- 原則和管理員角色不會在不同的目錄之間轉移。 服務管理員會更新為目的地帳戶的擁有者。
- 若要避免在租用戶之間轉移訂用帳戶時遺失 Azure RBAC 原則和角色指派，請確定 [將訂用帳戶移至收件者的 Azure AD 租用戶] 核取方塊仍是 [未核取] 的狀態。 這會在目前的 Azure AD 租用戶上保留服務、Azure 角色和原則，只轉移帳戶的帳單擁有權。  
    :::image type="content" source="./media/ea-portal-administration/unselected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="顯示未選取核取方塊來將訂用帳戶移至 Azure AD 租用戶的影像" lightbox="./media/ea-portal-administration/unselected-checkbox-move-subscriptions-to-recipients-tenant.png" :::


變更帳戶擁有者之前：

1. 在 Azure 企業版入口網站中，檢視 [帳戶] 索引標籤並識別來源帳戶。 來源帳戶的狀態必須為作用中。
1. 識別目的地帳戶並確定其為作用中狀態。

若要轉移所有訂用帳戶的帳戶擁有權：

1. 登入 Azure 企業版入口網站。
1. 在左側導覽區域中，選取 [管理]。
1. 選取 [帳戶] 索引標籤，並將滑鼠停留在帳戶上。
1. 選取右側的變更帳戶擁有者圖示。 該圖示類似一個人型。  
    ![顯示變更帳戶擁有者符號的影像](./media/ea-portal-administration/create-ea-create-sub-transfer-account-ownership-of-sub.png)
1. 選擇要轉移的目的地帳戶，然後選取 [下一步]。
1. 如果您想要在 Azure AD 租用戶之間轉移帳戶擁有權，請選取 [將訂用帳戶移至收件者的 Azure AD 租用戶] 核取方塊。  
    :::image type="content" source="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="顯示已選取核取方塊來將訂用帳戶移至 Azure AD 租用戶的影像" lightbox="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" :::
1. 確認轉移，然後選取 [提交]。

若要轉移單一訂用帳戶的帳戶擁有權：

1. 登入 Azure 企業版入口網站。
1. 在左側導覽區域中，選取 [管理]。
1. 選取 [帳戶] 索引標籤，並將滑鼠停留在帳戶上。
1. 選取右側的轉移訂用帳戶圖示。 該圖示類似一個頁面。  
    ![顯示轉移訂用帳戶符號的影像](./media/ea-portal-administration/ea-transfer-subscriptions.png)
1. 選擇要轉移訂用帳戶的目的地帳戶，然後選取 [下一步]。
1. 如果您想要在 Azure AD 租用戶之間轉移訂用帳戶擁有權，請選取 [將訂用帳戶移至收件者的 Azure AD 租用戶] 核取方塊。  
    :::image type="content" source="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="顯示已選取核取方塊來將訂用帳戶移至 Azure AD 租用戶的影像" lightbox="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" :::
1. 確認轉移，然後選取 [提交]。


## <a name="associate-an-account-to-a-department"></a>將帳戶與部門建立關聯

企業系統管理員可將註冊下的現有帳戶與部門產生關聯。

### <a name="to-associate-an-account-to-a-department"></a>將帳戶與部門產生關聯

1. 以企業系統管理員身分登入 Azure EA 入口網站。
1. 選取左側導覽列上的 [管理]。
1. 選取 [部門]。
1. 將滑鼠停留在包含帳戶的資料列上方，然後選取右側的鉛筆圖示。
1. 從下拉式功能表中選取部門。
1. 選取 [儲存]。

## <a name="associate-an-existing-account-with-your-pay-as-you-go-subscription"></a>將現有的帳戶與您的隨用隨付訂用帳戶建立關聯

如果您在 Azure 入口網站上已經有現有的 Microsoft Azure 帳戶，請輸入相關聯的學校、公司或 Microsoft 帳戶，以便將其與您的 Enterprise 合約註冊建立關聯。

### <a name="associate-an-existing-account"></a>建立現有帳戶的關聯

1. 在 Azure 企業版入口網站中，選取 [管理]。
1. 選取 [帳戶] 索引標籤。
1. 選取 [+ 新增帳戶]。
1. 輸入與現有 Azure 帳戶相關聯的公司、學校或 Microsoft 帳戶。
1. 確認與現有 Azure 帳戶相關聯的帳戶。
1. 在報告中提供您想要用來識別此帳戶的名稱。
1. 選取 [新增]。
1. 若要新增其他帳戶，您可以再次選取 [+ 新增帳戶] 選項，或選取 [系統管理員] 按鈕返回首頁。
1. 如果您檢視 [帳戶] 頁面，則新增的帳戶會以 [擱置] 狀態出現。

### <a name="confirm-account-ownership"></a>確認帳戶擁有權

1. 登入與您所提供公司、學校或 Microsoft 帳戶相關聯的電子郵件帳戶。
1. 開啟標題為「邀請經由 Microsoft 大量授權在 Microsoft Azure 服務上啟用您的帳戶」的電子郵件通知。
1. 選取邀請中的 [登入 Microsoft Azure 企業版入口網站] 連結。
1. 選取 [登入]。
1. 輸入您的公司、學校或 Microsoft 帳戶和密碼，以登入並確認帳戶擁有權。

### <a name="azure-marketplace"></a>Azure Marketplace

雖然大部分的訂用帳戶都能從隨用隨付環境轉換為 Azure Enterprise 合約，但 Azure Marketplace 服務不能。 若要擁有所有訂用帳戶和費用的單一檢視，建議您將 Azure Marketplace 服務新增至 Azure 企業版入口網站。

1. 登入 Azure 企業版入口網站。
1. 選取左側導覽列上的 [管理]。
1. 選取 [註冊] 索引標籤。
1. 檢視 [註冊詳細資料] 區段。
1. 在 Azure Marketplace 欄位的右邊，選取鉛筆圖示並加以啟用。 選取 [儲存]。

帳戶擁有者現在可以購買先前在隨用隨付訂用帳戶中所擁有的 Azure Marketplace 服務。

在您的 EA 註冊下啟用新的 Azure Marketplace 訂用帳戶之後，請取消在隨用隨付環境中建立的 Azure Marketplace 服務。 此步驟很重要，如此一來，當您的隨用隨付付款方式到期時，您的 Azure Marketplace 訂用帳戶就不會進入不良狀態。

### <a name="msdn"></a>MSDN

MSDN 訂用帳戶會自動轉換成 MSDN 開發/測試，而 Azure EA 供應項目將會喪失所有現有的貨幣點數。

### <a name="azure-in-open"></a>Azure in Open

如果您建立 Azure in Open 訂用帳戶與 Enterprise 合約的關聯，將會喪失所有尚未使用的 Azure in Open 點數。 因此，建議您在將帳戶新增至 Enterprise 合約之前，先取用 Azure in Open 訂用帳戶的所有點數。  

### <a name="accounts-with-support-subscriptions"></a>具有支援訂用帳戶的帳戶

如果您的 Enterprise 合約沒有支援訂用帳戶，且您新增的現有帳戶具有 Azure 企業版入口網站的支援訂用帳戶，您的 MOSA 支援訂用帳戶將不會自動轉移。 您需要在寬限期內 (在後續月份結束前) 重新購買 Azure EA 中的支援訂用帳戶。

## <a name="department-spending-quotas"></a>部門支出配額

EA 客戶可為註冊下的每個部門設定或變更支出配額。 系統會針對目前的預付款期間設定支出配額金額。 在目前的預付款期間結束時，系統會將現有的支出配額延長到下一個預付款期間，除非值有所更新。

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

部門的支出配額此時會顯示在 [部門清單] 檢視中的 [部門] 索引標籤下。在目前的預付款期間結束時，Azure EA 入口網站將會維護下一個預付款期限的支出配額。

部門配額金額獨立於目前的 Azure 預付款金額外，且配額金額和警示僅適用於第一方使用量。 部門支出配額僅供參考之用，不會強制執行支出限制。

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

如需使用者角色的詳細資訊，請參閱[企業使用者角色](./understand-ea-roles.md#enterprise-user-roles)。

## <a name="add-an-azure-ea-account"></a>新增 Azure EA 帳戶

Azure EA 帳戶是 Azure EA 入口網站中的組織單位。 其可用來管理訂用帳戶，也可用來進行報告。 若要存取及使用 Azure 服務，您需要建立一個帳戶或讓人為您建立一個帳戶。

如需有關 Azure 帳戶的詳細資訊，請參閱[新增帳戶](#add-an-account)。

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

## <a name="create-a-subscription"></a>建立訂用帳戶

帳戶擁有者可以檢視和管理訂用帳戶。 您可以使用訂用帳戶將開發環境和專案的存取權提供給組織中的小組。 例如：測試、生產、開發和預備。

當您為每個應用程式環境建立不同的訂用帳戶後，您可以協助保護每個環境的安全。

- 您也可以為每個訂用帳戶指派不同的服務管理員帳戶。
- 您可以將訂用帳戶與任意數目的服務建立關聯。
- 帳戶擁有者會建立訂用帳戶，並將服務管理員帳戶指派給其帳戶中的每個訂用帳戶。

### <a name="add-a-subscription"></a>新增訂用帳戶

使用下列資訊來新增訂用帳戶。

第一次將訂用帳戶新增至您的帳戶時，系統會要求您接受 Microsoft 線上訂用帳戶合約 (MOSA) 和費率方案。 雖然這些不適用於 Enterprise 合約客戶，但 MOSA 和費率方案是建立訂用帳戶的必要項目。 您的 Microsoft Azure Enterprise 合約註冊修訂會取代上述項目，而您的合約關係並不會變更。 出現提示時，選取表示您接受條款的方塊。

_Microsoft Azure 企業版_ 是建立訂用帳戶時的預設名稱。 您可以變更此名稱，以便與您的註冊中的其他訂用帳戶區別，並確保其可在企業層級的報告中辨識。

若要新增訂用帳戶：

1. 在 Azure 企業版入口網站中，登入帳戶。
1. 選取 [系統管理員]索引標籤，然後選取頁面頂端的 [訂用帳戶]。
1. 確認您已以帳戶的帳戶擁有者身分登入。
1. 選取 [+ 新增訂用帳戶]，然後選取 [購買]。

   第一次將訂用帳戶新增至帳戶時，您必須提供連絡人資訊。 新增其他訂用帳戶時，系統會為您加入連絡人資訊。

1. 選取 [訂用帳戶] ，然後選取您所建立的訂用帳戶。
1. 選取 [編輯訂用帳戶詳細資料]。
1. 編輯 [訂用帳戶名稱] 和 [服務管理員]，然後選取核取記號。

   訂用帳戶名稱會出現在報告上。 這是與開發入口網站中訂用帳戶相關聯的專案名稱。

新的訂用帳戶可能需要最多 24 小時的時間，才會出現在訂用帳戶清單中。 建立訂用帳戶之後，您可以：

- [編輯訂用帳戶詳細資料](https://account.azure.com/Subscriptions)
- [管理訂用帳戶服務](https://portal.azure.com/#home)

## <a name="delete-subscription"></a>刪除訂用帳戶

若要刪除以您作為帳戶擁有者的訂用帳戶：

1. 使用與您的帳戶相關聯的認證登入 Azure 入口網站。
1. 在 [中樞] 功能表中，選取 [訂用帳戶]。
1. 在頁面左上角的 [訂用帳戶] 索引標籤中，選取您要取消的訂用帳戶，然後選取 [取消訂用帳戶] 以啟動 [取消] 索引標籤。
1. 輸入訂用帳戶名稱並選擇取消原因，然後選取 [取消訂用帳戶] 按鈕。

只有帳戶管理員可以取消訂用帳戶。

如需詳細資訊，請參閱[取消訂用帳戶之後會發生什麼情況？](cancel-azure-subscription.md#what-happens-after-subscription-cancellation)。

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

新的通知連絡人會顯示在 [通知連絡人] 區段中。 若要變更通知頻率，請選取通知連絡人，然後選取所選資料列右側的鉛筆符號。 將頻率設定為 **每日**、**每週**、**每月** 或 **無**。

您可以隱藏「逼近範圍期間的結束日期」及「停用和取消佈建日期逼近」的生命週期通知。 停用生命週期通知會隱藏範圍期間和合約結束日期的相關通知。

## <a name="azure-sponsorship-offer"></a>Azure 贊助供應項目

Azure 贊助供應項目是有贊助限制的 Microsoft Azure 帳戶。 只有 Microsoft 所選的限定客戶，才能收到電子郵件邀請。 若您獲選參加 Microsoft Azure 贊助供應項目，您將會收到屬於您帳戶識別碼的電子郵件邀請。

如需詳細資訊，請建立[贊助啟用的支援要求](https://aka.ms/azrsponsorship)。

## <a name="conversion-to-work-or-school-account-authentication"></a>轉換為公司或學校帳戶驗證

Azure 企業版使用者可以從 Microsoft 帳戶 (MSA 或 Live ID) 轉換為公司或學校帳戶 (使用 Azure Active Directory) 驗證類型。

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

### <a name="can-i-associate-my-existing-azure-account-to-azure-ea-enrollment"></a>我可以將現有的 Azure 帳戶與 Azure EA 註冊建立關聯嗎？

是。 您身為帳戶擁有者的所有 Azure 訂用帳戶都會轉換成您的 Enterprise 合約。 包含的訂用帳戶會使用每月點數，例如 Visual Studio、AzurePass、MPN、BizSpark 等等。 轉換這類訂用帳戶時，您將會失去每月點數。

### <a name="how-many-azure-account-owners-can-you-have-per-subscription"></a>您的每個訂用帳戶可以有多少個 Azure 帳戶擁有者？

每個訂用帳戶只能有一個帳戶擁有者。  您可以在 [Azure 入口網站](https://portal.azure.com)頁面左上角的 [訂用帳戶] 索引標籤中，使用角色型存取或 (存取控制 (IAM)) 來新增其他角色。

### <a name="is-it-possible-to-transfer-subscription-ownership-to-another-account"></a>是否可能將 Azure 訂用帳戶擁有權轉移給另一個帳戶？

是，您可以將訂用帳戶擁有權轉移給不同帳戶。 例如，如果帳戶 A 有三個訂用帳戶，則企業系統管理員能夠將一個訂用帳戶轉移至帳戶 B、一個轉移至帳戶 C、一個轉移至帳戶 D。或者將所有訂用帳戶轉移至帳戶 E。

若要轉移訂用帳戶：

1. 在 Azure 企業版入口網站中，選取 [管理] > [帳戶]。
1. 將滑鼠停留在最右邊的 [帳戶] 上，可查看 [轉移擁有權] (人員圖示) 和 [轉移訂用帳戶] (清單圖示) 選項。 只有作用中的帳戶才會顯示這些選項。

### <a name="can-an-azure-account-owner-be-listed-under-more-than-one-department"></a>一個 Azure 帳戶擁有者是否可列在多個部門下？

否，一個帳戶擁有者只能與單一部門相關聯。 此原則有助於確保在 Azure EA 入口網站中，能夠正確地監視和分配該擁有者在 EA 註冊下對應的部門所產生的相關成本和支出。

### <a name="can-an-azure-account-owner-be-listed-as-a-security-group"></a>Azure 帳戶擁有者是否可以列為安全性群組？

否，訂用帳戶擁有者必須是唯一的 Microsoft 帳戶 (MSA) 或 Azure Active Directory (Azure AD) 驗證。 若要顧及組織內的延續性，您可以考慮建立一般帳戶，並使用 Azure AD 來管理訂用帳戶存取。

### <a name="can-an-individual-user-own-multiple-subscriptions"></a>個別使用者可以擁有多個訂用帳戶嗎？

Azure 帳戶擁有者可以建立及管理不限數量的訂用帳戶。

### <a name="how-can-i-accessview-all-my-organizations-subscriptions"></a>如何存取/檢視組織的所有訂用帳戶？

目前，這必須使用原則來完成；也就是說，對於每個建立的訂用帳戶，您都必須要求使用角色型存取將您的帳戶新增至訂用帳戶角色。

### <a name="where-do-i-go-to-create-a-subscription"></a>我應在何處建立訂用帳戶？

EA 註冊的系統管理員必須先將您的帳戶新增至 Azure EA 入口網站中的帳戶擁有者角色，您才能建立 Enterprise Azure (EA) 供應項目訂用帳戶。 接著，您必須登入 Azure EA 入口網站，以取得建立 EA 供應項目類型訂用帳戶的權利。 建議您使用 EA 入口網站，從 [訂用帳戶] 索引標籤中的 [+ 新增訂用帳戶] 連結建立第一個 EA 訂用帳戶。  不過，在您的帳戶獲得授權後，經由 portal.azure.com，在頁面左上角的 [訂用帳戶] 索引標籤中建立訂用帳戶，可能會比較容易，因為在此處您可以用單一步驟建立及重新命名您的訂用帳戶。

### <a name="who-can-create-a-subscription"></a>誰可以建立訂用帳戶？

若要建立 Enterprise Azure 供應項目類型訂用帳戶，您必須獲得 [EA 入口網站](https://ea.azure.com)上的帳戶擁有者角色。

## <a name="azure-ea-term-glossary"></a>Azure EA 字詞詞彙

- **帳戶**：Azure 企業版入口網站上的組織單位。 其可用來管理訂用帳戶和進行報告。
- **帳戶擁有者**：在 Azure 上管理訂用帳戶和服務管理員的人員。 他們可以檢視此帳戶及其相關聯訂用帳戶的使用量資料。
- **增修條款訂用帳戶**：依據註冊增修條款的一年期或相接訂用帳戶。
- **預付款**：Azure 服務年度購買金額的預付款，其針對此預付款的使用量採用折扣預付費率。
- **部門系統管理員**：負責管理部門、建立新帳戶和帳戶擁有者、檢視所管理部門的使用量詳細資料，而且在獲得授權的情況下可檢視成本的人員。
- **註冊號碼**：由 Microsoft 提供的唯一識別碼，用來識別與 Enterprise 合約相關聯的特定註冊。
- **企業系統管理員**：在 Azure 上管理部門、部門擁有者、帳戶和帳戶擁有者的人員。 他們能夠管理企業系統管理員，以及針對與 Enterprise 註冊相關聯的所有帳戶和訂用帳戶，檢視使用量資料、已計費數量和未計費費用。
- **Enterprise 合約**：適用於集中購買客戶的 Microsoft 授權合約，這類客戶想倚賴 Microsoft 技術將其整個組織標準化，並依據 Microsoft 軟體標準來維護資訊技術基礎結構。
- **Enterprise 合約註冊**：Enterprise 合約方案中的註冊，可以折扣費率提供大量的 Microsoft 產品。
- **Microsoft 帳戶**：一種 Web 架構服務，可讓參與的網站以一組認證驗證使用者。
- **Microsoft Azure Enterprise 註冊增修條款 (註冊增修條款)** ：由企業簽署的增修條款，可讓他們在其 Enterprise 註冊範圍內存取 Azure。
- **Azure 企業版入口網站**：企業客戶用來管理其 Azure 帳戶及其相關訂用帳戶的入口網站。
- **取用的資源數量**：一個月內使用的個別 Azure 服務數量。
- **服務管理員**：負責存取和管理 Azure 企業版入口網站上訂用帳戶和開發專案的人員。
- 訂用帳戶：代表 Azure 企業版入口網站訂用帳戶，而且是由相同服務管理員所管理 Azure 服務的容器。
- **公司或學校帳戶**：適用於已設定 Azure Active Directory、與雲端同盟，且所有帳戶都在單一租用戶上的組織。

### <a name="enrollment-statuses"></a>註冊狀態

- **新增**：此狀態會指派給在 24 小時內建立的註冊，並會在 24 小時內更新為 [擱置中] 狀態。
- **Pending**：註冊系統管理員必須登入 Azure 企業版入口網站。 登入之後，註冊將會切換為 [作用中] 狀態。
- **使用中**：註冊為 [作用中] 狀態，而且可以在 Azure 企業版入口網站中建立帳戶和訂用帳戶。 註冊的作用中狀態會維持到 Enterprise 合約結束日期為止。
- **無限延期**：無限延期會出現在超過 Enterprise 合約結束日期的時候。 其可讓選擇延長期限的 Azure EA 客戶在 Enterprise 合約結束時繼續無限期地使用 Azure 服務。

   在 Azure EA 註冊到達 Enterprise 合約結束日期之前，註冊系統管理員應決定要採用下列哪個選項：

  - 藉由新增額外的 Azure 預付款以更新註冊。
  - 轉移至新的註冊。
  - 遷移至 Microsoft 線上訂閱方案 (MOSP)。
  - 確認停用所有與註冊相關聯的服務。
- **已到期**：Azure EA 客戶已選擇不延長期限，且 Azure EA 註冊已達到 Enterprise 合約結束日期。 註冊將會到期，且所有相關聯的服務都會停用。
- **已轉移**：所有相關聯的帳戶和服務都已轉移到新註冊的註冊，將會顯示為已傳輸狀態。
  >[!NOTE]
  > 如果在續約時產生新的註冊號碼，則註冊不會自動轉移。 您必須將先前的註冊號碼包含在續約文件中，以利自動轉移。

## <a name="next-steps"></a>後續步驟

- 了解[虛擬機器保留](ea-portal-vm-reservations.md)如何協助您節省成本。
- 如果您需要 Azure EA 入口網站問題的疑難排解協助，請參閱[針對 Azure EA 入口網站的存取進行疑難排解](ea-portal-troubleshoot.md)。