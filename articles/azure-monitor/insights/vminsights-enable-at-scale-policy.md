---
title: 使用 Azure 策略為 VM 啟用 Azure 監視器
description: 本文介紹如何使用 Azure 策略為多個 Azure 虛擬機器或虛擬機器縮放集啟用 VM 的 Azure 監視器。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 7069f2cc96b8876f5514acfa4ba49274b61be46f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282931"
---
# <a name="enable-azure-monitor-for-vms-by-using-azure-policy"></a>使用 Azure 策略為 VM 啟用 Azure 監視器

本文介紹如何使用 Azure 策略為 Azure 虛擬機器或虛擬機器縮放集啟用 Azure 監視器。 此過程結束時，您將成功配置啟用日誌分析和依賴項代理以及標識不符合的虛擬機器。

要發現、管理和啟用所有 Azure 虛擬機器或虛擬機器縮放集的 VM Azure 監視器，可以使用 Azure 策略或 Azure PowerShell。 Azure 策略是我們建議的方法，因為您可以管理原則定義以有效管理訂閱，以確保新預配 VM 的一致合規性和自動啟用。 以下策略定義：

* 部署 Log Analytics 代理程式和 Dependency Agent。
* 報告合規性結果。
* 修復不符合要求的 VM。

如果您有興趣使用 Azure PowerShell 或 Azure 資源管理器範本完成這些任務，請參閱使用[Azure PowerShell 或 Azure 資源管理器範本為 VM 啟用 Azure 監視器](vminsights-enable-at-scale-powershell.md)。

## <a name="prerequisites"></a>Prerequisites
在使用策略將 Azure VM 和虛擬機器縮放集裝到 VM 的 Azure 監視之前，必須在要用於存儲監視資料的工作區上啟用 VMInsights 解決方案。 此任務可以從"**其他載入"選項**選項卡上的 Azure 監視器中的"**入門"** 頁完成。 選擇 **"配置工作區**"，這將提示您選擇要配置的工作區。

![設定工作區](media/vminsights-enable-at-scale-policy/configure-workspace.png)

您還可以通過選擇 **"使用策略啟用"** 來配置工作區，然後選擇"**配置工作區**"工具列按鈕。  這將在選定的工作區上安裝 VMInsights 解決方案，使工作區能夠存儲使用策略啟用的 VM 和虛擬機器規模集發送的監視資料。 

![使用策略啟用](media/vminsights-enable-at-scale-policy/enable-using-policy.png)

## <a name="manage-policy-coverage-feature-overview"></a>管理原則覆蓋率功能概述

VM 策略覆蓋範圍的 Azure 監視器簡化了大規模發現、管理和啟用啟用 AZURE**監視器計畫**，其中包括前面提到的策略定義。 要訪問此功能，請從"為 VM 的 Azure 監視器**入門"** 選項卡中選擇**其他載入選項**。 選擇 **"管理原則覆蓋範圍**"以打開**VM 策略覆蓋範圍的 Azure 監視器**頁。

![來自 VM 的 Azure 監視器"入門"選項卡](./media/vminsights-enable-at-scale-policy/get-started-page.png)

在此處，您可以檢查和管理整個管理組和訂閱中計畫的覆蓋範圍。 您可以瞭解每個管理組和訂閱中有多少 VM 及其合規性狀態。

![虛擬機器管理原則頁的 Azure 監視器](media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

此資訊可用於説明您規劃和執行來自一個中心位置的 VM Azure 監視器的治理方案。 雖然 Azure 策略在將策略或計畫分配給作用域時提供合規性視圖，但使用此新頁面，您可以發現策略或計畫未分配的位置並分配它。 所有操作（如分配、查看和編輯）都直接重定向到 Azure 策略。 **VM 策略覆蓋範圍的 Azure 監視器**是僅針對為 VM 啟用 Azure**監視器**的擴展和集成體驗。

在此頁面中，您還可以為 VM 的 Azure 監視器配置日誌分析工作區，其中包括：

- 安裝服務映射解決方案。
- 啟用性能圖表、活頁簿以及自訂日誌查詢和警報所使用的作業系統效能計數器。

![用於 VM 的 Azure 監視器配置工作區](media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

此選項與任何策略操作無關。 它可用於提供一種簡單的方法來滿足為 VM 啟用 Azure 監視器所需的[先決條件](vminsights-enable-overview.md)。  

### <a name="what-information-is-available-on-this-page"></a>此頁面上提供了哪些資訊？

下表提供了策略覆蓋率頁上提供的資訊的細目以及如何解釋它。

| 函式 | 描述 | 
|----------|-------------| 
| **Scope** | 具有或繼承存取權限的管理組和訂閱，並能夠向下切入管理組層次結構。|
| **角色** | 您的角色到範圍，可以是讀者，擁有者，或貢獻者。 在某些情況下，指示您可能有權訪問訂閱，但無法訪問它所屬的管理組，則可能顯示為空白。 其他列中的資訊因角色而異。 該角色是確定在分配策略或計畫（擁有者）、編輯策略或計畫或查看合規性方面可以看到哪些資料和執行操作的關鍵。 |
| **總 VM** | 該作用域下的 VM 數。 對於管理組，它是嵌套在訂閱或子管理組下的 VM 的總和。 |
| **分配範圍** | 策略或計畫涵蓋的 VM 百分比。 |
| **分配狀態** | 有關策略或計畫分配狀態的資訊。 |
| **合規 VM** | 在策略或計畫下符合的 VM 數。 對於為**VM 啟用 Azure 監視器**的計畫，這是同時具有日誌分析代理和依賴項代理的 VM 數。 在某些情況下，由於沒有分配、沒有 VM 或許可權不足，它可能顯示為空白。 資訊在**合規狀態**下提供。 |
| **合 規** | 總體符合性數是符合的不同資源的總和除以所有不同資源的總和。 |
| **合規性狀態** | 有關策略或計畫分配的合規性狀態的資訊。|

分配策略或計畫時，分配中選擇的範圍可以是列出的作用域或策略的子集。 例如，您可能為訂閱（策略範圍）而不是管理組（覆蓋範圍）創建了分配。 在這種情況下，**分配覆蓋範圍**的值指示策略或計畫範圍內的 VM 除以覆蓋範圍範圍內的 VM。 在另一種情況下，您可能將某些 VM、資源組或訂閱從策略作用域中排除。 如果該值為空，則表示策略或計畫不存在或您沒有許可權。 資訊在 **"分配狀態"** 下提供。

## <a name="enable-by-using-azure-policy"></a>使用 Azure 原則啟用

若要在您的租用戶中，使用 Azure 原則啟用適用於 VM 的 Azure 監視器：

- 將計畫分配給作用域：管理組、訂閱或資源組。
- 審核並修復合規性結果。

如需有關指派 Azure 原則的詳細資訊，請參閱 [Azure 原則概觀](../../governance/policy/overview.md#policy-assignment)，並在繼續進行之前，檢閱[管理群組概觀](../../governance/management-groups/overview.md)。

### <a name="policies-for-azure-vms"></a>Azure VM 的策略

下表列出了 Azure VM 的策略定義。

|名稱 |描述 |類型 |
|-----|------------|-----|
|啟用適用於 VM 的 Azure 監視器 |為指定作用域（管理組、訂閱或資源組）中的虛擬機器啟用 Azure 監視器。 請使用 Log Analytics 工作區作為參數。 |方案 |
|審核依賴項代理部署 = 未列出的 VM 映射 （OS） |如果清單中未定義 VM 映射 （OS）且未安裝代理，則將 VM 報告為不合規。 |原則 |
|稽核記錄分析代理部署 = 未列出的 VM 映射 （OS） |如果清單中未定義 VM 映射 （OS）且未安裝代理，則將 VM 報告為不合規。 |原則 |
|為 Linux VM 部署依賴項代理 |如果在清單中定義了 VM 映射 （OS） 且未安裝代理，則為 Linux VM 部署依賴項代理。 |原則 |
|為 Windows VM 部署依賴項代理 |如果在清單中定義了 VM 映射 （OS） 且未安裝代理，則為 Windows VM 部署依賴項代理。 |原則 |
|為 Linux VM 部署日誌分析代理 |如果清單中定義了 VM 映射 （OS） 且未安裝該代理，則為 Linux VM 部署日誌分析代理。 |原則 |
|為 Windows VM 部署日誌分析代理 |如果在清單中定義了 VM 映射 （OS） 且未安裝該代理，則為 Windows VM 部署日誌分析代理。 |原則 |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Azure 虛擬機器縮放集的策略

下表列出了 Azure 虛擬機器規模集的策略定義。

|名稱 |描述 |類型 |
|-----|------------|-----|
|為虛擬機器縮放集啟用 Azure 監視器 |為指定作用域（管理組、訂閱或資源組）中的虛擬機器縮放集啟用 Azure 監視器。 請使用 Log Analytics 工作區作為參數。 注意：如果規模設置升級策略設置為"手動"，則通過在設置上調用升級來應用擴展程式。 在 CLI 中，`az vmss update-instances`這是 。 |方案 |
|審核依賴項代理部署在虛擬機器規模集 + 未列出的 VM 映射 （OS） 中 |如果未在清單中定義 VM 映射 （OS） 且未安裝代理，則將虛擬機器規模設置為不合規。 |原則 |
|稽核記錄分析代理部署在虛擬機器規模集 + 未列出的 VM 映射 （OS） 中 |如果未在清單中定義 VM 映射 （OS） 且未安裝代理，則將虛擬機器規模設置為不合規。 |原則 |
|為 Linux 虛擬機器擴展集部署依賴項代理 |如果清單中定義了 VM 映射 （OS）並且未安裝代理，則為 Linux 虛擬機器擴展代理將部署依賴項代理。 |原則 |
|為 Windows 虛擬機器縮放集部署依賴項代理 |如果清單中定義了 VM 映射 （OS）並且未安裝代理，則為 Windows 虛擬機器擴展依賴項代理將集。 |原則 |
|為 Linux 虛擬機器規模集部署日誌分析代理 |如果清單中定義了 VM 映射 （OS） 且未安裝代理，則為 Linux 虛擬機器擴展集部署日誌分析代理。 |原則 |
|為 Windows 虛擬機器規模集部署日誌分析代理 |如果清單中定義了 VM 映射 （OS）並且未安裝代理，則為 Windows 虛擬機器擴展日誌分析代理將集。 |原則 |

獨立原則 (未包含在計畫中) 如下所述：

|名稱 |描述 |類型 |
|-----|------------|-----|
|審核 VM 日誌分析工作區 = 報告不匹配 |如果 VM 未登錄到策略或計畫分配中指定的日誌分析工作區，則將 VM 報告為不合規。 |原則 |

### <a name="assign-the-azure-monitor-initiative"></a>指派 Azure 監視器計畫

要從**VM 策略覆蓋範圍的 Azure 監視器**創建策略分配，請按照以下步驟操作。 若要了解如何完成這些步驟，請參閱 [從 Azure 入口網站建立原則指派](../../governance/policy/assign-policy-portal.md)。

分配策略或計畫時，分配中選擇的範圍可以是此處列出的範圍或策略的子集。 例如，您可能為訂閱（策略範圍）而不是管理組（覆蓋範圍）創建了分配。 在這種情況下，覆蓋率百分比將指示策略或計畫範圍內的 VM 除以覆蓋範圍內的 VM。 在另一種情況下，您可能將某些 VM、資源組或訂閱從策略作用域中排除。 如果為空，則表示策略或計畫不存在或您沒有許可權。 資訊在 **"分配狀態"** 下提供。

1. 登錄到 Azure[門戶](https://portal.azure.com)。

2. 在 Azure 門戶中，選擇 **"監視器**"。 

3. 在 **"見解"** 部分中選擇**虛擬機器**。
 
4. 選擇"**開始"** 選項卡。在頁面上，選擇 **"管理原則覆蓋範圍**"。

5. 從表中選擇管理組或訂閱。 通過選擇省略號 （...） 選擇**範圍**。在此示例中，作用域將策略分配限制為要強制執行的虛擬機器分組。

6. 在**Azure 策略分配**頁上，它預填充了為 VM**啟用 Azure 監視器**的計畫。 
    "**分配"名稱**框會自動使用計畫名稱填充，但您可以更改它。 您還可以添加可選說明。 "**按"分配**"框將根據登錄者自動填滿。 此為選用值。

7. (選擇性) 若要從範圍中移除一或多個資源，請選取 [排除項目]****。

8. 在支援之區域的 [Log Analytics 工作區]**** 下拉式清單中，選取一個工作區。

   > [!NOTE]
   > 如果工作區超出指派的範圍之外，請將 [Log Analytics 參與者]** 權限授與原則指派的主體識別碼。 如果不執行此操作，您可能會看到部署失敗，如`The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...`授予存取權限，請查看[如何手動設定託管標識](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity)。
   > 
   >  選擇 **"託管標識"** 核取方塊，因為正在分配的計畫包括具有*DeployIfNotExists*效果的策略。
    
9. 在 [管理身分識別位置]**** 下拉式清單中，選取適當的區域。

10. 選擇 **"分配**"。

創建分配後 **，VM 策略覆蓋範圍的 Azure 監視器**頁面將更新**分配覆蓋範圍**、**分配狀態**、**符合 VM**和**符合性狀態**以反映更改。 

以下矩陣映射了計畫的每個可能的合規性狀態。  

| 合規性狀態 | 描述 | 
|------------------|-------------|
| **相容** | 作用域中的所有 VM 都部署了日誌分析和依賴項代理。|
| **不符合要求** | 並非所有 VM 都部署了日誌分析和依賴項代理，可能需要進行修正。|
| **未啟動** | 添加了新分配。 |
| **鎖** | 您沒有足夠的許可權授予管理組。<sup>1</sup> | 
| **空白** | 未分配任何策略。 | 

<sup>1</sup>如果您無法訪問管理組，請要求擁有者提供存取權限。 或者，通過子管理組或訂閱查看合規性並管理分配。 

下表映射了計畫的每個可能分配狀態。

| 分配狀態 | 描述 | 
|------------------|-------------|
| **成功** | 作用域中的所有 VM 都部署了日誌分析和依賴項代理。|
| **警告** | 訂閱不在管理組下。|
| **未啟動** | 添加了新分配。 |
| **鎖** | 您沒有足夠的許可權授予管理組。<sup>1</sup> | 
| **空白** | 不存在 VM 或未分配策略。 | 
| **動作** | 分配策略或編輯分配。 | 

<sup>1</sup>如果您無法訪問管理組，請要求擁有者提供存取權限。 或者，通過子管理組或訂閱查看合規性並管理分配。

## <a name="review-and-remediate-the-compliance-results"></a>檢閱和補救合規性結果

以下示例適用于 Azure VM，但它也適用于虛擬機器縮放集。 要瞭解如何查看合規性結果，請參閱[識別不合規結果](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources)。 在**VM 策略覆蓋範圍的 Azure 監視器**頁上，從表中選擇管理組或訂閱。 通過選擇省略號 （...） 選擇 **"查看合規性**"。   

![Azure VM 的原則合規性](./media/vminsights-enable-at-scale-policy/policy-view-compliance.png)

根據計畫中包含的策略的結果，在以下情況下，VM 報告為不符合要求：

* 未部署日誌分析代理或依賴項代理。  
    此對於包含現有 VM 的範圍來說是典型案例。 要緩解它，通過在不合規的策略上[創建修正任務](../../governance/policy/how-to/remediate-resources.md)來部署所需的代理。  
    - 為 Linux VM 部署依賴項代理
    - 為 Windows VM 部署依賴項代理
    - 為 Linux VM 部署日誌分析代理
    - 為 Windows VM 部署日誌分析代理

* 在策略定義中未標識 VM 映射 （OS）。  
    部署原則的準則僅包含從已知 Azure VM 映像部署的 VM。 請參閱文件以了解 VM OS 是否受到支援。 如果不受支援，請複製部署原則，並加以更新/修改，使映像符合規範。  
    - 審核依賴項代理部署 = 未列出的 VM 映射 （OS）
    - 稽核記錄分析代理部署 = 未列出的 VM 映射 （OS）

* VM 未登入指定的 Log Analytics 工作區。  
    計畫範圍內的部分 VM 可以登入未在原則指派中指定的 Log Analytics 工作區。 此策略是一種工具，用於確定哪些 VM 正在向不合規工作區報告。  
    - 審核 VM 日誌分析工作區 = 報告不匹配

## <a name="edit-an-initiative-assignment"></a>編輯計畫分配

在將計畫分配給管理組或訂閱後，您可以隨時對其進行編輯以修改以下屬性：

- 分配名稱
- 描述
- 由
- Log Analytics 工作區
- 例外狀況

## <a name="next-steps"></a>後續步驟

現在，已為虛擬機器啟用了監視功能，此資訊可用於使用針對 VM 的 Azure 監視器進行分析。 

- 若要檢視探索到的應用程式相依性，請參閱[檢視適用於 VM 的 Azure 監視器對應](vminsights-maps.md)。 

- 要根據 VM 的性能識別瓶頸和總體利用率，請參閱查看[Azure VM 性能](vminsights-performance.md)。 
