---
title: DoD 影響等級 5 藍圖範例
description: DoD 影響等級 5 藍圖範例的部署步驟，其中包括藍圖成品參數的詳細資料。
ms.date: 01/08/2021
ms.topic: sample
ms.openlocfilehash: 6136136eef5d405ae0849e5ce8c8faede138fb00
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98034895"
---
# <a name="deploy-the-dod-impact-level-5-blueprint-sample"></a>部署 DoD 影響等級 5 藍圖範例

若要部署 Azure 藍圖的美國國防部影響等級 5 (DoD IL5) 藍圖範例，您必須採取下列步驟：

> [!div class="checklist"]
> - 從範例建立新的藍圖
> - 將您的範例複本標記為 **已發佈**
> - 將您的藍圖複本指派給現有的訂用帳戶

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free)。

## <a name="create-blueprint-from-sample"></a>從範例建立藍圖

首先，若要實作藍圖範例，請使用範例作為起點，在您的環境中建立新藍圖。

1. 在左側窗格中選取 [所有服務]。 搜尋並選取 [藍圖]。

1. 在左側的 [快速入門] 頁面上，選取 [建立藍圖] **下方的 [建立]** 按鈕。

1. 在 [其他範例] 下方尋找 **DoD 影響等級 5** 藍圖範例，然後選取 [使用此範例]。

1. 輸入藍圖範例的 [基本資料]：

   - **藍圖名稱**：為 DoD 影響等級 5 藍圖範例的複本提供名稱。
   - **定義位置**：使用省略符號，然後選取要作為範例複本儲存位置的管理群組。

1. 在頁面頂端選取 [成品]索引標籤，或在頁面底部選取 **[下一步：成品]** 。

1. 檢閱構成此藍圖範例的成品清單。 許多成品都具有我們稍後會定義的參數。 當您檢閱完藍圖範例時，請選取 [儲存草稿]。

## <a name="publish-the-sample-copy"></a>發佈範例複本

您的環境中現已建立了藍圖範例複本。 該複本會以 **草稿** 模式建立，而且必須先 **發佈**，才能進行指派和部署。 您可以根據環境和需求來自訂藍圖範例複本，但是這樣的修改可能會使其與 DoD 影響等級 5 控制不一致。

1. 在左側窗格中選取 [所有服務]。 搜尋並選取 [藍圖]。

1. 選取左側的 [藍圖定義] 頁面。 使用篩選來尋找您的藍圖範例複本，然後將其選取。

1. 選取頁面頂端的 [發佈藍圖]。 在右側的新窗格中，提供藍圖範例複本的 **版本**。 如果您稍後會進行修改，此屬性十分實用。 提供 [變更附註]，例如「從 DoD IL5 藍圖範例發佈的第一版」。 然後選取頁面底部的 [發佈]。

## <a name="assign-the-sample-copy"></a>指派範例複本

成功 **發佈** 藍圖範例複本後，可以將藍圖定義指派給其所在管理群組中的訂用帳戶。 此步驟用於提供參數，以建立每個專屬的藍圖範例複本部署。

1. 在左側窗格中選取 [所有服務]。 搜尋並選取 [藍圖]。

1. 選取左側的 [藍圖定義] 頁面。 使用篩選來尋找您的藍圖範例複本，然後將其選取。

1. 選取藍圖定義頁面頂端的 [指派藍圖]。

1. 提供用於指派藍圖的參數值：

   - 基本概念

     - **訂用帳戶**：在用來儲存藍圖範例複本的管理群組中，選取一或多個訂用帳戶。 如果您選取多個訂用帳戶，輸入的參數就會用來為每個訂用帳戶建立指派。
     - **指派名稱**：名稱會根據藍圖名稱預先填入。
       視需要變更，或保持原狀。
     - **位置**：選取要在其中建立受控識別的區域。 Azure 藍圖會使用此受控識別來部署指派的藍圖之中的所有成品。 若要深入了解，請參閱[適用於 Azure 資源的受控識別](../../../../active-directory/managed-identities-azure-resources/overview.md)。
     - **藍圖定義版本**：挑選 **已發佈** 版本的藍圖範例複本。

   - 鎖定指派

     為您的環境選取藍圖鎖定設定。 如需詳細資訊，請參閱[藍圖資源鎖定](../../concepts/resource-locking.md)。

   - 受控識別

     保留預設的 [系統指派] 受控識別選項。

   - 構件參數

     本節中定義的參數會套用至其定義所屬的成品。 這些參數是[動態參數](../../concepts/parameters.md#dynamic-parameters)，因為定義於藍圖指派期間。 如需完整清單或成品參數及其說明，請參閱[成品參數資料表](#artifact-parameters-table)。

1. 輸入所有參數後，選取頁面底部的 [指派]。 藍圖指派會隨即建立，並且開始部署成品。 部署需要大約一小時的時間。 若要檢查部署的狀態，請開啟藍圖指派。

> [!WARNING]
> Azure 藍圖服務和內建藍圖範例皆是 **免費** 項目。 Azure 資源會[依據產品計價](https://azure.microsoft.com/pricing/)。 請使用[定價計算機](https://azure.microsoft.com/pricing/calculator/)來預估此藍圖範例所部署資源的執行成本。

## <a name="artifact-parameters-table"></a>成品參數資料表

下表提供藍圖成品參數的清單：

|成品名稱|成品類型|參數名稱|描述|
|-|-|-|-|
|DoD 影響等級 5|原則指派|必須包含在 Windows VM 系統管理員群組中的使用者清單|應在系統管理員本機群組中受納入使用者的分號分隔清單；例如：Administrator; myUser1; myUser2|
|DoD 影響等級 5|原則指派|從 Windows VM 系統管理員群組排除的使用者清單|應在系統管理員本機群組中受排除使用者的分號分隔清單；例如：Administrator; myUser1; myUser2|
|DoD 影響等級 5|原則指派|應啟用診斷記錄的資源類型清單||
|DoD 影響等級 5|原則指派|VM 代理程式報表的 Log Analytics 工作區識別碼|VM 代理程式應回報的 Log Analytics 工作區識別碼 (GUID)|
|DoD 影響等級 5|原則指派|應啟用網路監看員的區域清單|若要查看使用 Get-AzLocation 的區域完整清單，|
|DoD 影響等級 5|原則指派|Windows Web 伺服器的 TLS 最舊版本|至少要為 Windows Web 伺服器啟用的 TLS 通訊協定版本|
|DoD 影響等級 5|原則指派|PHP 最新版本|應用程式服務的最新支援 PHP 版本|
|DoD 影響等級 5|原則指派|Java 最新版本|應用程式服務的最新支援 Java 版本|
|DoD 影響等級 5|原則指派|最新 Windows Python 版本|應用程式服務的最新支援 Python 版本|
|DoD 影響等級 5|原則指派|最新 Linux Python 版本|應用程式服務的最新支援 Python 版本|
|DoD 影響等級 5|原則指派|選擇性：要新增至稽核範圍且支援 Log Analytics 代理程式的 Windows VM 映像清單|以分號分隔的映像清單；範例：/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage|
|DoD 影響等級 5|原則指派|選擇性：要新增至稽核範圍且支援 Log Analytics 代理程式的 Linux VM 映像清單|以分號分隔的映像清單；範例：/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage|
|DoD 影響等級 5|原則指派|原則的效果：應將一個以上的擁有者指派給您的訂用帳戶|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應在虛擬機器上套用磁碟加密|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應已針對高嚴重性警示啟用傳送給訂用帳戶擁有者的電子郵件通知|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：函式應用程式的遠端偵錯應關閉|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：確定 '.NET Framework' 在作為函式應用程式的一部分時，版本是最新的|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應在 SQL 資料庫上啟用透明資料加密|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：SQL 受控執行個體上應啟用弱點評定|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：確定 'PHP' 在作為 API 應用程式的一部分時，版本是最新的|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應針對 SQL 伺服器佈建 Azure Active Directory 管理員|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應該只允許對 Redis Cache 的安全連線|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應在虛擬機器擴展集上安裝端點保護解決方案|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：稽核不受限制的儲存體帳戶網路存取|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：SQL 受控執行個體的進階資料安全性設定應包含用來接收安全性警示的電子郵件地址|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應修復虛擬機器擴展集上安全性組態的弱點|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應啟用儲存體帳戶的安全傳輸|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應在虛擬機器上啟用自適性應用程式控制|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應為適用於 PostgreSQL 的 Azure 資料庫啟用異地備援備份|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：確定 'Java' 在作為 Web 應用程式的一部分時，版本是最新的|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應針對您的訂用帳戶指定最多 3 位擁有者|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應為您的訂用帳戶提供安全性連絡人電子郵件地址|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：CORS 不應允許每項資源存取您的 Web 應用程式|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應從訂用帳戶移除具有寫入權限的外部帳戶|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：具有讀取權限的外部帳戶應該從您的訂用帳戶中移除|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：已取代帳戶應該從您的訂用帳戶中移除|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：函式應用程式應只可經由 HTTPS 存取|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：確定 'Python' 在作為 Web 應用程式的一部分時，版本是最新的|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：確定 'Python' 在作為函式應用程式的一部分時，版本是最新的|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：確定 'PHP' 在作為 Web 應用程式的一部分時，版本是最新的|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：確定 'Python' 在作為 API 應用程式的一部分時，版本是最新的|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：弱點評量解決方案應修復弱點|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應為適用於 MySQL 的 Azure 資料庫啟用異地備援備份|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：確定 '.NET Framework' 在作為 Web 應用程式的一部分時，版本是最新的|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：您應在機器上安裝系統更新|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：確定 'Java' 在作為 API 應用程式的一部分時，版本是最新的|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：確定 'HTTP' 在用來執行 Web 應用程式時，版本是最新的|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：您的 API 應用程式應使用最新的 TLS 版本|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應在您訂用帳戶上具有寫入權限的帳戶上啟用 MFA|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：SQL 伺服器的進階資料安全性設定應包含用來接收安全性警示的電子郵件地址|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：確定 'HTTP' 在用來執行 API 應用程式時，版本是最新的|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：Microsoft IaaSAntimalware 延伸模組應該部署在 Windows 伺服器上|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：確定 'Java' 在作為函式應用程式的一部分時，版本是最新的|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應限制透過網際網路面向端點存取|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應選取資訊安全中心標準定價層|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：稽核自訂 RBAC 規則的使用方式|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：Web 應用程式應只可經由 HTTPS 存取|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應啟用 SQL 伺服器上的稽核|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：Log Analytics 代理程式應該安裝在虛擬機器上|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應啟用 DDoS 保護標準|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應在您訂用帳戶上具有擁有者權限的帳戶上啟用 MFA|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：確定 'PHP' 在作為函式應用程式的一部分時，版本是最新的|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應在 SQL 伺服器上啟用進階資料安全性|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應在 SQL 受控執行個體上啟用進階資料安全性|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應在 SQL 受控執行個體進階資料安全性設定中，啟用傳給系統管理員和訂用帳戶擁有者的電子郵件通知|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：在 Azure 資訊安全中心中監視缺少的 Endpoint Protection|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：Just-In-Time 網路存取控制應套用在虛擬機器上|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應為您的訂用帳戶提供安全性連絡人電話號碼|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：Service Fabric 叢集應只能使用 Azure Active Directory 進行用戶端驗證|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：API 應用程式應只可經由 HTTPS 存取|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：在 SQL 受控執行個體的進階資料安全性設定中，進階威脅防護類型應設定為 [全部]|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應為儲存體帳戶啟用異地備援儲存體|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：確定 '.NET Framework' 在作為 API 應用程式的一部分時，版本是最新的|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應在虛擬機器擴展集上安裝系統更新|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應在 SQL 伺服器進階資料安全性設定中啟用傳給系統管理員和訂用帳戶擁有者的通知|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應關閉 Web 應用程式的遠端偵錯|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應為 Azure SQL Database 啟用長期異地備援備份|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：您應在機器上修復安全性組態的弱點|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：確定 'HTTP' 在用來執行函式應用程式時，版本是最新的|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應在您訂用帳戶上具有讀取權限的帳戶上啟用 MFA|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：在 SQL 伺服器的進階資料安全性設定中，進階威脅防護類型應設定為 [全部]|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應補救容器安全性設定中的弱點|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應關閉 API 應用程式的遠端偵錯|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：弱點評定應於您的 SQL 伺服器上啟用|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：Log Analytics 代理程式應該安裝在虛擬機器擴展集上|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：您的 Web 應用程式應使用最新的 TLS 版本|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：您的函式應用程式應使用最新的 TLS 版本|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：[預覽]：Kubernetes 服務應升級為不易受攻擊的 Kubernetes 版本|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|
|DoD 影響等級 5|原則指派|原則的效果：應修復 SQL 資料庫的弱點|此原則的 Azure 原則效果；如需效果的詳細資訊，請造訪 https://aka.ms/policyeffects|

## <a name="next-steps"></a>後續步驟

您已檢閱過部署 DoD 影響等級 5 藍圖範例的步驟，接下來請瀏覽下列文章，以了解藍圖和控制對應：

> [!div class="nextstepaction"]
> [DoD 影響等級 5 藍圖 - 概觀](./index.md)
> [DoD 影響等級 5 藍圖 - 控制對應](./control-mapping.md)

有關藍圖及其使用方式的其他文件：

- 了解[藍圖生命週期](../../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../../how-to/update-existing-assignments.md)。