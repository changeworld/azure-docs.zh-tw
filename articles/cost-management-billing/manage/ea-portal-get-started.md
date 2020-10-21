---
title: 開始使用 Azure 企業版入口網站
description: 本文說明 Azure Enterprise 合約 (Azure EA) 客戶如何使用 Azure 企業版入口網站。
author: bandersmsft
ms.author: banders
ms.date: 08/20/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 617351d5d1bdf9b6528803552e2d34766e9ab217
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132647"
---
# <a name="get-started-with-the-azure-enterprise-portal"></a>開始使用 Azure 企業版入口網站

本文協助直接和間接 Azure Enterprise 合約 (Azure EA) 客戶開始使用 [Azure 企業版入口網站](https://ea.azure.com)。 取得下列各項的基本資訊：

- Azure 企業版入口網站的結構。
- Azure 企業版入口網站中使用的角色。
- 訂用帳戶建立。
- Azure 企業版入口網站和 Azure 入口網站中的成本分析。

觀看這段影片以觀賞完整的 Azure 企業版入口網站上線工作階段：

> [!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="azure-enterprise-portal-hierarchy"></a>Azure 企業版入口網站階層

Azure 企業版入口網站階層包含：

- **Azure 企業版入口網站** - 可協助您管理 Azure EA 服務成本的線上管理入口網站。 您可以：

  - 建立 Azure EA 階層，其中包含部門、帳戶和訂用帳戶。
  - 協調已取用服務的成本、下載使用量報告，以及查看價格清單。
  - 建立用於註冊的 API 金鑰。

- **部門**可協助您將成本劃分成邏輯群組。 部門可讓您設定部門層級的預算或配額。

- **帳戶**是 Azure 企業版入口網站中的組織單位。 您可以使用帳戶來管理訂用帳戶和存取報告。

- **訂用帳戶**是 Azure 企業版入口網站中的最小單位。 這些訂用帳戶是由服務管理員所管理的 Azure 服務容器。

下圖說明簡單的 Azure EA 階層。

![簡單 Azure EA 階層的圖表](./media/ea-portal-get-started/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>企業使用者角色

下列系統管理使用者角色是您企業註冊的一部分：

- 企業系統管理員
- 部門系統管理員
- 帳戶擁有者
- 服務管理員
- 通知連絡人

角色可在兩個不同的入口網站中完成工作。 您可以使用 [Azure 企業版入口網站](https://ea.azure.com)來管理計費和成本，以及使用 [Azure 入口網站](https://portal.azure.com)來管理 Azure 服務。

使用者角色會與使用者帳戶相關聯。 若要驗證使用者的真確性，每位使用者都必須擁有有效的公司、學校或 Microsoft 帳戶。 請確定每個帳戶都與主動監視的電子郵件地址相關聯。 帳戶通知會傳送至該電子郵件地址。

設定使用者時，您可以將多個帳戶指派給企業系統管理員角色。 不過，只有一個帳戶可以持有帳戶擁有者角色。 此外，您可以將企業系統管理員和帳戶擁有者角色指派給單一帳戶。

### <a name="enterprise-administrator"></a>企業系統管理員

具有此角色的使用者具備最高層級的存取權。 他們可以：

- 管理帳戶和帳戶擁有者。
- 管理其他企業系統管理員。
- 管理部門系統管理員。
- 管理通知連絡人。
- 檢視所有帳戶上的使用量。
- 檢視所有帳戶上未開立帳單的費用。

一個企業註冊中可以有多個企業系統管理員。 您可以將唯讀存取權授與企業系統管理員。 他們都會繼承部門系統管理員角色。

### <a name="department-administrator"></a>部門系統管理員

具有此角色的使用者可以：

- 建立和管理部門。
- 建立新的帳戶擁有者。
- 針對他們所管理的部門檢視使用量詳細資料。
- 檢視成本 (若已具備必要權限)。

每個企業註冊可以有多個部門系統管理員。

當您編輯或建立新的部門系統管理員時，您可以授與部門系統管理員唯讀存取權。 將 [唯讀] 選項設為 [是]。

### <a name="account-owner"></a>帳戶擁有者

具有此角色的使用者可以：

- 建立及管理訂用帳戶。
- 管理服務管理員。
- 檢視訂用帳戶的使用量。

每個帳戶都需要唯一的公司、學校或 Microsoft 帳戶。 如需有關 Azure 企業版入口網站系統管理角色的詳細資訊，請參閱[了解 Azure 中的 Azure Enterprise 合約系統管理角色](understand-ea-roles.md)。

### <a name="service-administrator"></a>服務管理員

服務管理員角色有權管理 Azure 入口網站中的服務，並可將使用者指派給共同管理員角色。

### <a name="notification-contact"></a>通知連絡人

通知連絡人可接收與註冊相關的使用量通知。

## <a name="activate-your-enrollment"></a>啟動您的註冊

若要啟動您的服務，最初的企業系統管理員需從邀請電子郵件開啟 [Azure 企業版入口網站](https://ea.azure.com)，並使用電子郵件地址登入。

如果您已設定為企業管理員，則不需要接收啟用電子郵件。 移至 [Azure 企業版入口網站](https://ea.azure.com)，然後使用您的公司、學校或 Microsoft 帳戶電子郵件地址和密碼登入。

如果您有多個註冊，請選擇一個進行啟用。 預設只會顯示作用中的註冊。 若要檢視註冊記錄，請清除 Azure 企業版入口網站右上方的 [作用中] 選項。

在 [註冊] 底下，狀態會顯示 [作用中]。

![顯示作用中註冊的範例](./media/ea-portal-get-started/ea-enrollment-status.png)

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

如需有關企業系統管理角色的詳細資訊，請參閱[了解 Azure 中的 Azure Enterprise 合約系統管理角色](understand-ea-roles.md)。

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

![顯示 [新增部門系統管理員] 對話方塊的範例](./media/ea-portal-get-started/ea-create-add-department-admin.png)

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

![顯示帳戶清單和 [+ 新增帳戶] 選項的範例](./media/ea-portal-get-started/create-ea-add-an-account.png)

若要新增其他帳戶，請選取 [新增其他帳戶]，或選取左側工具列右下角的 [新增]。

若要確認帳戶擁有權：

1. 登入 Azure 企業版入口網站。
1. 檢視狀態。

   狀態應該會從 [擱置] 變更為 [開始/結束日期]。 開始/結束日期是使用者第一次登入的日期和合約結束日期。
1. 出現 [警告] 訊息時，帳戶擁有者必須選取 [繼續]，才能在第一次登入 Azure 企業版入口網站時啟用帳戶。

## <a name="change-account-owner"></a>變更帳戶擁有者

企業系統管理員可以使用 Azure 企業版入口網站來轉移註冊中的訂用帳戶擁有權。 此動作會將來源使用者帳戶中的所有訂用帳戶移至目的地使用者帳戶。

轉移帳戶時，請注意這項重要資訊：

- 您可以進行下列轉移：
  - 從公司或學校帳戶轉移至另一個公司或學校帳戶。
  - 從 Microsoft 帳戶轉移至公司或學校帳戶。
  - 從 Microsoft 帳戶轉移至另一個 Microsoft 帳戶。

    目標帳戶必須是有效的 Azure Commerce 帳戶，才能作為轉移的有效目標。 若使用新帳戶，當您登入 Azure 企業版入口網站時，系統會要求您建立 Azure Commerce 帳戶。 若使用現有帳戶，您必須先建立新的 Azure 訂用帳戶，才能讓該帳戶符合資格。

- 您無法從公司或學校帳戶轉移至 Microsoft 帳戶。

- 當您完成訂用帳戶轉移時，Microsoft 會更新帳戶擁有者。

了解這些 Azure 角色型存取控制 (Azure RBAC) 原則：

- 當您在相同租用戶中的兩個組織識別碼之間執行訂用帳戶轉移時，Azure RBAC 原則和現有的服務管理員及共同管理員角色都會保留。
- 其他訂用帳戶轉移會導致您的 Azure RBAC 原則和角色指派遺失。
- 原則和管理員角色不會在不同的目錄之間轉移。 服務管理員會更新為目的地帳戶的擁有者。

變更帳戶擁有者之前：

1. 在 Azure 企業版入口網站中，檢視 [帳戶] 索引標籤並識別來源帳戶。 來源帳戶的狀態必須為作用中。
1. 識別目的地帳戶並確定其為作用中狀態。

若要轉移所有訂用帳戶的帳戶擁有權：

1. 登入 Azure 企業版入口網站。
1. 在左側導覽區域中，選取 [管理]。
1. 選取 [帳戶] 索引標籤，並將滑鼠停留在帳戶上。
1. 選取右側的變更帳戶擁有者圖示。 該圖示類似一個人型。
1. 選擇合格的帳戶，然後選取 [下一步]。
1. 確認轉移，然後選取 [提交]。

![顯示變更帳戶擁有者符號的影像](./media/ea-portal-get-started/create-ea-create-sub-transfer-account-ownership-of-sub.png)

若要轉移單一訂用帳戶的帳戶擁有權：

1. 登入 Azure 企業版入口網站。
1. 在左側導覽區域中，選取 [管理]。
1. 選取 [帳戶] 索引標籤，並將滑鼠停留在帳戶上。
1. 選取右側的轉移訂用帳戶圖示。 該圖示類似一個頁面。
1. 選擇合格的訂用帳戶，然後選取 [下一步]。
1. 確認轉移，然後選取 [提交]。

![顯示轉移訂用帳戶符號的影像](./media/ea-portal-get-started/ea-transfer-subscriptions.png)

觀看這段影片以查看 Azure 企業版入口網站使用者管理：

> [!VIDEO https://www.youtube.com/embed/621jVkvmwm8]

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

## <a name="transfer-an-enterprise-subscription-to-a-pay-as-you-go-subscription"></a>將企業訂用帳戶轉移到隨用隨付訂用帳戶

若要將企業訂用帳戶轉移至採隨用隨付費率的個別訂用帳戶，您必須在 Azure 企業版入口網站中建立新的支援要求。 若要建立支援要求，請選取 [說明及支援] 區域中的 [+ 新增支援要求]。

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

## <a name="view-usage-summary-and-download-reports"></a>檢視使用量摘要及下載報表

企業系統管理員可以在 Azure 企業版入口網站中檢視其使用量資料的摘要、已使用預付款，以及與其他使用量相關聯的費用。 費用會顯示在所有帳戶和訂用帳戶的摘要層級上。

若要檢視特定帳戶的詳細使用量，請下載使用量詳細資料報告：

1. 登入 Azure 企業版入口網站。
1. 選取 [報表]。
1. 選取 [下載使用量] 索引標籤。
1. 在報告清單中，針對想要取得的每月報告選取 [下載]。

   > [!NOTE]
   > 使用量詳細資料報告不包含任何適用的稅金。
   >
   > 從使用量發生到使用量在報告上反映時，可能會有最多 8 小時的延遲時間。

若要檢視使用量摘要報告和圖表：

1. 登入 Azure 企業版入口網站。

1. 選取款預付字詞。

   若要變更 [使用量摘要] 的日期範圍，您可以從頁面右上方的 **M** (每月) 切換到 **C** (自訂)，然後輸入自訂開始和結束日期。

   ![在自訂檢視中建立和檢視使用量摘要及下載報告](./media/ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
1. 若要檢視其他詳細資料，您可選取圖表上的期間或月份。

   - 此圖表會顯示每月使用量明細，其中包含已使用的用量、超額服務、個別計費的費用和 Azure Marketplace 費用。
   - 對於所選的月份，您可以使用圖表下方的欄位，依部門、帳戶和訂用帳戶進行篩選。
   - 您可以在 [依服務收費] 與 [依階層收費] 之間切換。
   - 藉由展開相關區段，從 [Azure 服務]、[個別計費的費用] 及 [ Azure Marketplace] 檢視詳細資料。

觀看這段影片以了解如何檢視使用量：

> [!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>下載 CSV 報表

企業系統管理員可使用每月報告下載頁面，將下列報告下載為 CSV 檔案：

- 餘額和費用
- 使用量詳細資料
- Azure Marketplace 費用
- 價位表

若要下載報表：

1. 在 Azure 企業版入口網站中，選取 [報告]。
2. 選取頁面頂端的 [下載使用量]。
3. 選取月份報表旁的 [下載]。

   > [!NOTE]
   > 產生使用量的時間與使用量顯示在報表中的時間可能會有最多五天的延遲。
   >
   > 使用 Safari 將 CSV 檔案下載至 Excel 的使用者可能會遇到格式錯誤。 若要避免錯誤，請使用文字編輯器來開啟檔案。

![顯示 [下載使用量] 頁面的範例](./media/ea-portal-get-started/create-ea-download-csv-reports.png)

觀看這段影片以了解如何下載使用量資訊：

> [!VIDEO https://www.youtube.com/embed/eY797htT1qg]

### <a name="advanced-report-download"></a>進階報表下載

您可以使用進階報告下載來取得涵蓋特定日期範圍或帳戶的報告。 輸出檔的格式為 CSV，可容納大型記錄集。

1. 在 Azure 企業版入口網站中，選取 [進階報告下載]。
1. 選取適當的日期範圍和適當的帳戶。
1. 選取 [要求使用量資料]。
1. 選取 [重新整理] 按鈕，直到報告狀態更新為 [下載] 為止。
1. 下載報告。

### <a name="download-usage-reports-and-billing-information-for-a-prior-enrollment"></a>下載先前註冊的使用量報告和計費資訊

您可以在進行註冊轉移之後，下載先前註冊的使用量報告和計費資訊。 歷程記錄報告適用於 Azure 企業版入口網站與成本管理。

Azure 企業版入口網站會篩選掉非作用中的註冊。 您必須取消勾選 [作用中] 方塊，才能檢視非作用中的已轉移註冊。  

![取消勾選 [作用中] 方塊可讓使用者查看非作用中的註冊](./media/ea-portal-get-started/unchecked-active-box.png)

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
- **公司或學校帳戶**：適用於已設定 Active Directory 與雲端同盟，且所有帳戶都在單一租用戶上的組織。

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

## <a name="get-started-on-azure-ea---faq"></a>開始使用 Azure EA - 常見問題集

這一節提供在上線過程中，客戶所詢問的一般問題詳細資料。  

### <a name="can-i-associate-my-existing-azure-account-to-azure-ea-enrollment"></a>我可以將現有的 Azure 帳戶與 Azure EA 註冊建立關聯嗎？

是。 您身為帳戶擁有者的所有 Azure 訂用帳戶都會轉換成您的 Enterprise 合約。 包含的訂用帳戶會使用每月點數，例如 Visual Studio、AzurePass、MPN、BizSpark 等等。 轉換這類訂用帳戶時，您將會失去每月點數。

### <a name="i-accidentally-associated-my-existing-azure-account-with-azure-ea-enrollment-as-a-result-i-lost-my-monthly-credit-can-i-get-my-monthly-credit-back"></a>我不小心將現有的 Azure 帳戶與 Azure EA 註冊建立關聯。 因此，我失去了每月點數。 我可以拿回每月點數嗎？

如果您以具有與 Visual Studio 訂用帳戶相同認證的 Azure EA 帳戶擁有者身分登入，您可以執行下列其中一項動作來復原個別 Visual Studio 訂用帳戶的 Azure 權益：

- 移除或移動任何相關聯的 Azure 訂用帳戶之後，請從 Azure 企業版入口網站刪除您的帳戶擁有者。 然後，再次註冊個別 Visual Studio Azure 權益。
- 從 VLSC 中的系統管理網站刪除 Visual Studio 訂閱者，這次將訂用帳戶重新指派給具有不同認證的帳戶。 然後，再次註冊個別 Visual Studio Azure 權益。

### <a name="what-type-of-subscription-should-i-create"></a>我應該建立哪種類型的訂用帳戶？

Azure 企業版入口網站為企業客戶提供兩種類型的訂用帳戶：

- Microsoft Azure 企業版，其適合用於：
  - 所有生產使用量
  - 以基礎結構費用為基礎的最佳價格

  如需詳細資訊，[請連絡 Azure 業務代表](https://azure.microsoft.com/pricing/enterprise-agreement/)。

- Enterprise 開發/測試，其適合用於：
  - 所有團隊開發/測試工作負載
  - 中度至重度的個人開發/測試工作負載
  - 存取特殊 MSDN 映像和優惠服務費率

  如需詳細資訊，請參閱 [Enterprise 開發/測試供應項目](https://azure.microsoft.com/offers/ms-azr-0148p/)。

### <a name="is-it-possible-to-transfer-subscription-ownership-to-another-account"></a>是否可能將 Azure 訂用帳戶擁有權轉移給另一個帳戶？

是，您可以將訂用帳戶擁有權轉移給不同帳戶。 例如，如果帳戶 A 有三個訂用帳戶，則企業系統管理員能夠將一個訂用帳戶轉移至帳戶 B、一個轉移至帳戶 C、一個轉移至帳戶 D。或者將所有訂用帳戶轉移至帳戶 E。

若要轉移訂用帳戶：

1. 在 Azure 企業版入口網站中，選取 [管理] > [帳戶]。
1. 將滑鼠停留在最右邊的 [帳戶] 上，可查看 [轉移擁有權] (人員圖示) 和 [轉移訂用帳戶] (清單圖示) 選項。 只有作用中的帳戶才會顯示這些選項。

### <a name="my-subscription-name-is-the-same-as-the-offer-name-should-i-change-the-subscription-name-to-something-meaningful-to-my-organization"></a>我的訂用帳戶名稱與供應項目名稱相同。 我應該將訂用帳戶名稱變更為對我組織有意義的名稱嗎？

當您建立訂用帳戶時，名稱會預設為您選擇的供應項目類型。 我們建議您將訂用帳戶名稱變更為可讓您輕鬆追蹤訂用帳戶的名稱。

若要變更名稱：

1. 登入 [https://account.windowsazure.com](https://account.windowsazure.com)。
1. 選取訂用帳戶清單。
1. 選取您要編輯的訂用帳戶。
1. 選取 [管理訂用帳戶] 圖示。
1. 編輯訂用帳戶詳細資料。

### <a name="how-can-i-track-costs-incurred-by-a-cost-center"></a>如何追蹤成本中心所產生的成本？

若要依成本中心追蹤成本，您必須在下列其中一個層級定義成本中心：

- department
- 帳戶
- 訂用帳戶

根據您的需求，您可以使用相同的成本中心來追蹤與特定成本中心相關聯的使用量和成本。

例如，為了追蹤牽涉到多個部門的特殊專案成本，您可以在訂用帳戶層級定義成本中心來追蹤使用量和成本。

您不能在服務層級定義成本中心。 如果您想要追蹤服務層級的使用量，可以使用服務層級提供的「標記」功能。

### <a name="how-do-i-track-usage-and-spend-by-different-departments-in-my-organization"></a>如何追蹤組織中不同部門的使用量和支出？

您可以視需要在 Azure EA 註冊之下建立許多部門。 若要正確地追蹤使用量，請確保您不會跨部門共用訂用帳戶。

建立部門和訂用帳戶之後，您可以在使用量報告中查看資料。 此資訊可協助您追蹤使用量，以及管理部門層級的成本與支出。

您也可以透過報告 API 來存取使用量資料。 如需詳細資訊和範例程式碼，請參閱 [Azure Enterprise REST API](./ea-portal-rest-apis.md)。

### <a name="can-i-set-a-spending-quota-and-get-alerts-as-i-approach-my-limit"></a>我可以設定消費配額，並在接近限制時收到警示嗎？

您可以設定部門層級的消費配額，而系統會在您的消費限制符合您所定義配額的 50%、75%、90% 和 100% 時自動通知您。

若要定義消費配額，請選取部門，然後選取編輯圖示。 編輯消費限制詳細資料之後，請選取 [儲存]。

### <a name="i-used-resource-groups-to-implement-azure-rbac-and-track-usage-how-can-i-view-the-associated-usage-details"></a>我使用了資源群組來實行 Azure RBAC 和追蹤使用量。 如何才能檢視相關聯的使用量詳細資料？

如果您使用「資源群組」和「標記」，則會在服務層級追蹤此資訊，而且您可在詳細使用量下載 (CSV) 檔案中加以存取。 請參閱 Azure 企業版入口網站中的[下載使用量報告](https://ea.azure.com/report/downloadusage)。

您也可以透過 API 存取使用量。 如需詳細資訊和範例程式碼，請參閱 [Azure Enterprise REST API](./ea-portal-rest-apis.md)。

> [!NOTE]
> 您只能將標籤套用到支援 Azure Resource Manager 作業的資源。 如果您透過傳統部署模型 (例如透過傳統入口網站) 建立虛擬機器、虛擬網路或儲存體，就無法將標記套用至該資源。 您必須透過 Resource Manager 重新部署這些資源才能支援標記。 所有其他資源皆支援標記。

### <a name="can-i-perform-analyses-using-power-bi"></a>我可以使用 Power BI 執行分析嗎？

是。 使用適用於 Power BI 的 Microsoft Azure 企業版內容套件，您可以：

- 快速匯入和分析您企業註冊的 Azure 耗用量。
- 找出哪個部門、帳戶或訂用帳戶耗用了最多使用量。
- 了解貴組織最常使用的服務。
- 追蹤消費和使用量趨勢。

若要使用 Power BI：

1. 移至 Power BI 網站。
1. 使用有效的公司或學校帳戶登入。

   公司或學校帳戶可以是用於透過 Azure 企業版入口網站存取註冊的相同或不同帳戶。
1. 在服務的儀表板上，選擇 Microsoft Azure 企業版圖格，然後按一下 [連線]。
1. 在 [連線到 Azure 企業版] 畫面上，輸入：
    - Azure 環境 URL：[https://ea.azure.com](https://ea.azure.com)
    - 月數：介於 1 與 36 之間
    - 註冊號碼：您的註冊號碼
1. 選取 [下一步] 。
1. 在 [帳戶金鑰] 方塊中輸入 API 金鑰。

   您可以在 Azure 企業版入口網站中找到 API 金鑰。 在 [下載使用量] 索引標籤底下查看，然後選取 [API 存取金鑰]。 複製該金鑰，然後其貼到 Power BI 的 [帳戶金鑰] 方塊中。

視資料集的大小而定，可能需要 5 到 30 分鐘的時間，才能在 Power BI 中載入資料。

Power BI 報告適用於能夠檢視帳單資訊的 Azure EA 直接、合作夥伴和間接客戶。

## <a name="next-steps"></a>後續步驟

- Azure 企業版入口網站系統管理員應閱讀 [ Azure 企業版入口網站的系統管理](ea-portal-administration.md)，以了解常見的管理工作。
- 如果您需要 Azure 企業版入口網站問題的疑難排解協助，請參閱[針對 Azure 企業版入口網站的存取進行疑難排解](ea-portal-troubleshoot.md)。
- 如需 Azure EA 上線指南，請參閱 [Azure EA 上線指南 (PDF)](https://ea.azure.com/api/v3Help/v2AzureEAOnboardingGuide)。