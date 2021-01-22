---
title: 部署澳大利亞政府 ISM 受保護藍圖範例
description: 澳大利亞政府 ISM 受保護藍圖範例的部署步驟，包括藍圖成品參數的詳細資料。
ms.date: 01/21/2021
ms.topic: sample
ms.openlocfilehash: 354d010442f896728ef9af1e3f372c2abbc58ef3
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98682274"
---
# <a name="deploy-the-australian-government-ism-protected-blueprint-sample"></a>部署澳大利亞政府 ISM 受保護藍圖範例

若要部署 Azure ISM 受保護藍圖範例，必須採取下列步驟：

> [!div class="checklist"]
> - 從範例建立新的藍圖
> - 將您的範例複本標記為 **已發佈**
> - 將您的藍圖複本指派給現有的訂用帳戶

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free)。

## <a name="create-blueprint-from-sample"></a>從範例建立藍圖

首先，若要實作藍圖範例，請使用範例作為起點，在您的環境中建立新藍圖。

1. 在左側窗格中選取 [所有服務]。 搜尋並選取 [藍圖]。

1. 在左側的 [快速入門] 頁面上，選取 [建立藍圖] **下方的 [建立]** 按鈕。

1. 在 [其他範例] 下方尋找 **ISM 受保護** 藍圖範例，然後選取 [使用此範例]。

1. 輸入藍圖範例的 [基本資料]：

   - **藍圖名稱**：為 ISM 受保護藍本範例複本提供名稱。
   - **定義位置**：使用省略符號，然後選取要作為範例複本儲存位置的管理群組。

1. 在頁面頂端選取 [成品]索引標籤，或在頁面底部選取 **[下一步：成品]** 。

1. 檢閱構成此藍圖範例的成品清單。 許多成品都具有我們稍後會定義的參數。 當您檢閱完藍圖範例時，請選取 [儲存草稿]。

## <a name="publish-the-sample-copy"></a>發佈範例複本

您的環境中現已建立了藍圖範例複本。 該複本會以 **草稿** 模式建立，而且必須先 **發佈**，才能進行指派和部署。 您可以根據環境和需求來自訂藍圖範例複本，但是這樣的修改可能會使其與 ISM 受保護控制項不一致。

1. 在左側窗格中選取 [所有服務]。 搜尋並選取 [藍圖]。

1. 選取左側的 [藍圖定義] 頁面。 使用篩選來尋找您的藍圖範例複本，然後將其選取。

1. 選取頁面頂端的 [發佈藍圖]。 在右側的新窗格中，提供藍圖範例複本的 **版本**。 如果您稍後會進行修改，此屬性十分實用。 提供 [變更附註]，例如「從 ISM 受保護藍圖範例發佈的第一個版本。」 然後選取頁面底部的 [發佈]。

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
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應設定 VM 的 Log Analytics 工作區識別碼|這是應設定 VM 的 Log Analytics 工作區識別碼 (GUID)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應啟用診斷記錄的資源類型清單|診斷記錄設定未啟用時，可稽核的資源類型清單。 您可以在 [Azure 監視器資源記錄類別](../../../../azure-monitor/platform/resource-logs-categories.md#supported-log-categories-per-resource-type)中找到可接受的值。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應從 Windows VM 系統管理員群組中排除的使用者清單|應從系統管理員本機群組中排除之成員的分號分隔清單。 例如：Administrator; myUser1; myUser2|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應納入 Windows VM 系統管理員群組中的使用者清單|應納入系統管理員本機群組中之成員的分號分隔清單。 例如：Administrator; myUser1; myUser2|
|\[預覽\]：為 Linux VM 擴展集 (VMSS) 部署 Log Analytics 代理程式|原則指派|Linux VM 擴展集 (VMSS) 的 Log Analytics 工作區|如果工作區是在指派的範圍之外，您必須手動將「Log Analytics 參與者」權限 (或類似權限) 授與原則指派的主體識別碼。|
|\[預覽\]：為 Linux VM 擴展集 (VMSS) 部署 Log Analytics 代理程式|原則指派|選擇性：要新增至範圍的 VM 映像清單，這些映像具有支援的 Linux OS|空陣列可以用來表示沒有選擇性參數：\[\]|
|\[預覽\]：部署適用於 Linux VM 的 Log Analytics 代理程式|原則指派|Linux VM 的 Log Analytics 工作區|如果工作區是在指派的範圍之外，您必須手動將「Log Analytics 參與者」權限 (或類似權限) 授與原則指派的主體識別碼。|
|\[預覽\]：部署適用於 Linux VM 的 Log Analytics 代理程式|原則指派|選擇性：要新增至範圍的 VM 映像清單，這些映像具有支援的 Linux OS|空陣列可以用來表示沒有選擇性參數：\[\]|
|\[預覽\]：為 Windows VM 擴展集 (VMSS) 部署 Log Analytics 代理程式|原則指派|Windows VM 擴展集 (VMSS) 的 Log Analytics 工作區|如果工作區是在指派的範圍之外，您必須手動將「Log Analytics 參與者」權限 (或類似權限) 授與原則指派的主體識別碼。|
|\[預覽\]：為 Windows VM 擴展集 (VMSS) 部署 Log Analytics 代理程式|原則指派|選擇性：要新增至範圍的 VM 映像清單，這些映像具有支援的 Windows OS|空陣列可以用來表示沒有選擇性參數：\[\]|
|\[預覽\]：部署適用於 Windows VM 的 Log Analytics 代理程式|原則指派|Windows VM 的 Log Analytics 工作區|如果工作區是在指派的範圍之外，您必須手動將「Log Analytics 參與者」權限 (或類似權限) 授與原則指派的主體識別碼。|
|\[預覽\]：部署適用於 Windows VM 的 Log Analytics 代理程式|原則指派|選擇性：要新增至範圍的 VM 映像清單，這些映像具有支援的 Windows OS|空陣列可以用來表示沒有選擇性參數：\[\]|
|在儲存體帳戶部署進階威脅防護|原則指派|效果|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|在 SQL 伺服器上部署稽核|原則指派|保留期間天數中的值 (0 表示無保留天數限制)|保留天數 (選擇性，若未指定則為 180 天)|
|在 SQL 伺服器上部署稽核|原則指派|儲存體帳戶用於 SQL 伺服器稽核的資源群組名稱|稽核會將資料庫事件寫入您 Azure 儲存體帳戶的稽核記錄中 (儲存體帳戶會建立於 SQL Server 建立所在的每個區域中，且將由該區域中的所有伺服器共用)。 重要 - 為使稽核作業正常運作，請勿刪除或重新命名資源群組或儲存體帳戶。|
|部署網路安全性群組的診斷設定|原則指派|網路安全性群組診斷的儲存體帳戶前置詞|此前置詞會與網路安全性群組位置結合，構成所建立的儲存體帳戶名稱。|
|部署網路安全性群組的診斷設定|原則指派|儲存體帳戶用於網路安全性群組診斷的資源群組名稱 (必須存在)|將於其中建立儲存體帳戶的資源群組。 此資源群組必須已存在。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|允許資源和資源群組使用的位置|貴組織可以在部署資源時指定的 Azure 位置清單。 原則方案內「允許的位置」原則也會使用此提供的值。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|SQL 受控執行個體上應啟用弱點評定|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|弱點評定應於您的 SQL 伺服器上啟用|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|虛擬機器上應啟用弱點評定|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應為儲存體帳戶啟用異地備援儲存體|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應為適用於 MariaDB 的 Azure 資料庫啟用異地備援備份|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應為適用於 MySQL 的 Azure 資料庫啟用異地備援備份|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應為適用於 PostgreSQL 的 Azure 資料庫啟用異地備援備份|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應強化與虛擬機器互動的網際網路網路安全性群組規則|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|Web 應用程式應只可經由 HTTPS 存取|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|函式應用程式應只可經由 HTTPS 存取|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應從訂用帳戶移除具有寫入權限的外部帳戶|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|具有讀取權限的外部帳戶應該從您的訂用帳戶中移除|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|已取代帳戶應該從您的訂用帳戶中移除|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|CORS 不得允許每項資源存取您的 Web 應用程式|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應在虛擬機器擴展集上安裝系統更新|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應在您訂用帳戶上具有讀取權限的帳戶上啟用 MFA|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應在您訂用帳戶上具有擁有者權限的帳戶上啟用 MFA|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應在您訂用帳戶上具有寫入權限的帳戶上啟用 MFA|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應為 Azure SQL Database 啟用長期異地備援備份|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|從 Windows VM 系統管理員群組排除的使用者清單|應從系統管理員本機群組中排除之成員的分號分隔清單。 例如：Administrator; myUser1; myUser2|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應設定 VM 的 Log Analytics 工作區識別碼|這是應設定 VM 的 Log Analytics 工作區識別碼 (GUID)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|SQL 伺服器的弱點評估設定應包含接收掃描報告的電子郵件地址|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應在網際網路對應虛擬機器中套用自適性網路強化建議|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應將一個以上的擁有者指派給您的訂用帳戶|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應在虛擬機器上套用磁碟加密| 如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|函式應用程式的遠端偵錯應關閉|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應在 SQL 資料庫上啟用透明資料加密| 如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|SQL 受控執行個體上應啟用弱點評定|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應針對 SQL 伺服器佈建 Azure Active Directory 管理員|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應該只允許對 Redis Cache 的安全連線|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應在虛擬機器擴展集上安裝端點保護解決方案 |如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|選擇性：要新增至範圍的 VM 映像清單，這些映像具有支援的 Windows OS|空陣列可以用來表示沒有選擇性參數：[]
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|選擇性：要新增至範圍的 VM 映像清單，這些映像具有支援的 Linux OS |空陣列可以用來表示沒有選擇性參數：[]|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|稽核不受限制的儲存體帳戶網路存取|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應修復虛擬機器擴展集上安全性組態的弱點|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應啟用儲存體帳戶的安全傳輸  |如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應在虛擬機器上啟用自適性應用程式控制|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應針對您的訂用帳戶指定最多 3 位擁有者|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|[預覽] 應在虛擬機器上啟用弱點評量  |如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|CORS 不應允許每項資源存取您的 Web 應用程式|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應從訂用帳戶移除具有寫入權限的外部帳戶 |  如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|已取代帳戶應該從您的訂用帳戶中移除 |如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|函式應用程式應只可經由 HTTPS v2 存取  |如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|弱點評量解決方案應修復弱點  |如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|Azure 訂用帳戶應具有用於活動記錄的記錄設定檔  |如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應啟用診斷記錄的資源類型清單|診斷記錄設定未啟用時，可稽核的資源類型清單。 您可以在 [Azure 監視器資源記錄類別](../../../../azure-monitor/platform/resource-logs-categories.md#supported-log-categories-per-resource-type)中找到可接受的值。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|您應在機器上安裝系統更新|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|App Service 應使用最新的 TLS 版本|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應在您訂用帳戶上具有寫入權限的帳戶上啟用 MFA|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|Microsoft IaaSAntimalware 延伸模組應該部署在 Windows 伺服器上|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。 |
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|Web 應用程式應只可經由 HTTPS v2 存取  |如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。 |
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應啟用 DDoS 保護標準  |如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。 |
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應在您訂用帳戶上具有擁有者權限的帳戶上啟用 MFA |如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應在 SQL 伺服器上啟用進階資料安全性 |如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應在 SQL 受控執行個體上啟用進階資料安全性 |如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|在 Azure 資訊安全中心中監視缺少的 Endpoint Protection  |如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應在虛擬機器上應套用 Just-In-Time 網路存取控制 |如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|Service Fabric 叢集應只能使用 Azure Active Directory 進行用戶端驗證 |如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|App Service 應只可經由 HTTPS v2 存取 |如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應在虛擬機器擴展集上安裝系統更新  |如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|Web 應用程式的遠端偵錯應關閉|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|您應在機器上修復安全性組態的弱點  |如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。|
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應在您訂用帳戶上具有讀取權限的帳戶上啟用 MFA  |如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。 |
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|強制密碼歷程記錄  |  指定密碼重複使用的限制 - 在可以重複使用密碼之前必須為使用者帳戶建立新密碼的次數。 |
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|密碼最長使用期限  |  指定最多在經過多少天之後即必須變更使用者帳戶密碼。 值的格式是以逗號分隔的兩個整數，表示其內含範圍。 |
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|密碼最短使用期限 |   指定最少須經過多少天之後才可變更使用者帳戶密碼。 |
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|密碼長度下限  |   指定使用者帳戶密碼可包含的字元數目下限。 |
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|密碼必須符合複雜性需求|指定使用者帳戶密碼是否必須是複雜的。 如有需要，複雜密碼不可包含使用者帳戶名稱的一部分或完整名稱；長度至少必須是 6 個字元；須包含大寫、小寫、數字和非字母字元的組合。 |
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應補救容器安全性設定中的弱點|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。 |
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應關閉 App Service 的遠端偵錯|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。 |
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。 |
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|弱點評定應於您的 SQL 伺服器上啟用|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。 |
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|您的 Web 應用程式應使用最新的 TLS 版本|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。 |
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應使用網路安全性群組保護網際網路對應的虛擬機器|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。 |
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。 |
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|您的函式應用程式應使用最新的 TLS 版本|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。 |
|\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制項，並部署特定的 VM 擴充功能，以支援稽核需求|原則指派|應修復 SQL 資料庫的弱點|如需原則效果的相關資訊，請參閱[了解 Azure 原則效果](../../../policy/concepts/effects.md)。 |

## <a name="next-steps"></a>後續步驟

您已檢閱部署澳大利亞政府 ISM 受保護藍圖範例的步驟，接下來請瀏覽下列文章，以了解藍圖和控制項對應：

> [!div class="nextstepaction"]
> [ISM 受保護藍圖 - 概觀](./index.md)
> [ISM 受保護藍圖 - 控制項對應](./control-mapping.md)

有關藍圖及其使用方式的其他文件：

- 了解[藍圖生命週期](../../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../../how-to/update-existing-assignments.md)。