---
title: 使用 Azure 原則啟用適用於 VM 的 Azure 監視器
description: 本文說明如何使用 Azure 原則來啟用多個 Azure 虛擬機器或虛擬機器擴展集的適用於 VM 的 Azure 監視器。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: 7d3c4e0f4bd34f996bb39426af39a692a6f79c5c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85507172"
---
# <a name="enable-azure-monitor-for-vms-by-using-azure-policy"></a>使用 Azure 原則啟用適用於 VM 的 Azure 監視器

本文說明如何使用 Azure 原則來啟用 Azure 虛擬機器、Azure 虛擬機器擴展集和 Azure Arc 機器的適用於 VM 的 Azure 監視器。 在此程式結束時，您將已成功設定啟用 Log Analytics 和相依性代理程式，並識別出不符合規範的虛擬機器。

若要探索、管理及啟用所有 Azure 虛擬機器或虛擬機器擴展集的適用於 VM 的 Azure 監視器，您可以使用 Azure 原則或 Azure PowerShell。 Azure 原則是我們建議的方法，因為您可以管理原則定義，以有效地控制您的訂用帳戶，以確保一致的合規性並自動啟用新布建的 Vm。 這些原則定義：

* 部署 Log Analytics 代理程式和 Dependency Agent。
* 報告合規性結果。
* 補救不相容的 Vm。

如果您想要使用 Azure PowerShell 或 Azure Resource Manager 範本來完成這些工作，請參閱[使用 Azure PowerShell 或 Azure Resource Manager 範本來啟用適用於 VM 的 Azure 監視器](vminsights-enable-at-scale-powershell.md)。

## <a name="prerequisites"></a>必要條件
在使用原則將您的 Azure Vm 和虛擬機器擴展集上架到適用于 Vm 的 Azure 監視之前，您必須在將用來儲存監視資料的工作區上啟用 VMInsights 解決方案。 此工作可從 [**其他上線選項**] 索引標籤上 Azure 監視器中的 [**開始**使用] 頁面完成。 選取 [**設定工作區**]，這會提示您選取要設定的工作區。

![設定工作區](media/vminsights-enable-at-scale-policy/configure-workspace.png)

您也可以選擇 [**使用原則啟用**]，然後選取 [**設定工作區**] 工具列按鈕來設定您的工作區。  這會在選取的工作區上安裝 VMInsights 解決方案，讓工作區能夠儲存您使用原則啟用的 Vm 和虛擬機器擴展集所傳送的監視資料。 

![使用原則啟用](media/vminsights-enable-at-scale-policy/enable-using-policy.png)

## <a name="manage-policy-coverage-feature-overview"></a>管理原則涵蓋範圍功能總覽

適用於 VM 的 Azure 監視器原則涵蓋範圍可簡化**啟用適用於 VM 的 Azure 監視器**計畫的大規模探索、管理和啟用，其中包含先前所述的原則定義。 若要存取這項功能，請從適用於 VM 的 Azure 監視器中的 [**開始**使用] 索引標籤選取**其他上線選項**。 選取 [**管理原則涵蓋範圍**] 以開啟 [**適用於 VM 的 Azure 監視器原則涵蓋範圍**] 頁面。

![從 Vm 開始 Azure 監視器索引標籤](./media/vminsights-enable-at-scale-policy/get-started-page.png)

從這裡，您可以在管理群組和訂用帳戶中檢查和管理方案的涵蓋範圍。 您可以瞭解每個管理群組和訂用帳戶中有多少個 Vm，以及其合規性狀態。

![適用於 VM 的 Azure 監視器管理原則] 頁面](media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

這項資訊有助於您規劃和執行從一個集中位置適用於 VM 的 Azure 監視器的治理案例。 雖然 Azure 原則在將原則或計畫指派給範圍時提供合規性查看，但使用這個新頁面時，您可以探索未指派原則或計畫的位置，並將其指派給它。 所有動作（例如 [指派]、[視圖] 和 [編輯重新導向]）都會直接 Azure 原則。 [**適用於 VM 的 Azure 監視器原則涵蓋範圍**] 頁面是一種擴充且整合的體驗，僅適用于**啟用適用於 VM 的 Azure 監視器**的方案。

在此頁面中，您也可以設定適用於 VM 的 Azure 監視器的 Log Analytics 工作區，以安裝*VMInsights*解決方案。

![適用於 VM 的 Azure 監視器設定工作區](media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

此選項與任何原則動作無關。 它可用來提供簡單的方法，以滿足啟用適用於 VM 的 Azure 監視器所需的[必要條件](vminsights-enable-overview.md)。  

### <a name="what-information-is-available-on-this-page"></a>此頁面提供哪些資訊？

下表提供 [原則涵蓋範圍] 頁面上所呈現資訊的明細，以及如何加以解讀。

| 函式 | 說明 | 
|----------|-------------| 
| **範圍** | 您擁有或繼承的管理群組和訂用帳戶，並能夠向下切入管理群組階層。|
| **角色** | 您對範圍的角色，可能是讀取者、擁有者或參與者。 在某些情況下，它可能會顯示為空白，表示您可能有訂用帳戶的存取權，而不是它所屬的管理群組。 其他資料行中的資訊會根據您的角色而有所不同。 角色是決定您可以查看哪些資料的關鍵，以及您可以在指派原則或計畫（擁有者）、編輯它們或查看合規性方面執行的動作。 |
| **Vm 總計** | 該範圍下的 Vm 數目。 針對管理群組，它是在 [訂用帳戶] 或 [子管理] 群組底下嵌套的 Vm 總和。 |
| **指派涵蓋範圍** | 原則或方案所涵蓋的 Vm 百分比。 |
| **指派狀態** | 原則或方案指派狀態的資訊。 |
| **相容的 Vm** | 原則或方案底下符合規範的 Vm 數目。 針對**啟用適用於 VM 的 Azure 監視器**的方案，這是具有 Log Analytics 代理程式和相依性代理程式的 vm 數目。 在某些情況下，因為沒有指派、沒有 Vm 或許可權不足，所以可能會顯示為空白。 在**合規性狀態**下提供資訊。 |
| **遵循** | 整體合規性數目是符合規範的相異資源總和除以所有不同資源的總和。 |
| **合規性狀態** | 原則或方案指派的合規性狀態相關資訊。|

當您指派原則或方案時，在指派中選取的範圍可能是所列的範圍或其子集。 例如，您可能已建立訂用帳戶的指派（原則範圍），而不是管理群組（涵蓋範圍範圍）。 在此情況下，**指派涵蓋**範圍的值會指出原則或方案範圍中的 vm 除以涵蓋範圍內的 vm。 在另一種情況下，您可能已排除部分 Vm、資源群組或來自原則範圍的訂用帳戶。 如果此值為空白，表示原則或方案不存在，或您沒有許可權。 [**指派狀態**] 底下會提供資訊。

## <a name="enable-by-using-azure-policy"></a>使用 Azure 原則啟用

若要在您的租用戶中，使用 Azure 原則啟用適用於 VM 的 Azure 監視器：

- 將方案指派給範圍：管理群組、訂用帳戶或資源群組。
- 檢查並補救合規性結果。

如需有關指派 Azure 原則的詳細資訊，請參閱 [Azure 原則概觀](../../governance/policy/overview.md#assignments)，並在繼續進行之前，檢閱[管理群組概觀](../../governance/management-groups/overview.md)。

### <a name="policies-for-azure-vms"></a>Azure Vm 的原則

下表列出 Azure VM 的原則定義。

|Name |描述 |類型 |
|-----|------------|-----|
|啟用適用於 VM 的 Azure 監視器 |針對指定範圍（管理群組、訂用帳戶或資源群組）中的虛擬機器啟用 Azure 監視器。 請使用 Log Analytics 工作區作為參數。 |方案 |
|Audit Dependency agent 部署-未列出的 VM 映射（OS） |如果 VM 映射（OS）未在清單中定義且未安裝代理程式，則將 Vm 報告為不相容。 |原則 |
|Audit Log Analytics 代理程式部署–未列出的 VM 映射（OS） |如果 VM 映射（OS）未在清單中定義且未安裝代理程式，則將 Vm 報告為不相容。 |原則 |
|部署適用於 Linux VM 的 Dependency Agent |如果 VM 映射（OS）是在清單中定義且未安裝代理程式，請部署適用于 Linux Vm 的 Dependency agent。 |原則 |
|部署適用於 Windows VM 的 Dependency Agent |如果 VM 映射（OS）是在清單中定義且未安裝代理程式，請部署適用于 Windows Vm 的 Dependency agent。 |原則 |
|部署適用於 Linux VM 的 Log Analytics 代理程式 |如果 VM 映射（OS）是在清單中定義且未安裝代理程式，請部署適用于 Linux Vm 的 Log Analytics 代理程式。 |原則 |
|部署適用於 Windows VM 的 Log Analytics 代理程式 |如果 VM 映射（OS）是在清單中定義且未安裝代理程式，請部署適用于 Windows Vm 的 Log Analytics 代理程式。 |原則 |


### <a name="policies-for-hybrid-azure-arc-machines"></a>混合式 Azure Arc 機器的原則

下表列出混合式 Azure Arc 機器的原則定義。

|Name |描述 |類型 |
|-----|------------|-----|
| [預覽]： Log Analytics 代理程式應該安裝在您的 Linux Azure Arc 機器上 |如果 VM 映射（OS）是在清單中定義且未安裝代理程式，請將混合式 Azure Arc 機器報告為 Linux Vm 不相容。 |原則 |
| [預覽]： Log Analytics 代理程式應該安裝在您的 Windows Azure Arc 機器上 |如果 VM 映射（OS）是在清單中定義且未安裝代理程式，請將混合式 Azure Arc 機器回報為 Windows Vm 不符合規範。 |原則 |
| [預覽]：將 Dependency agent 部署至混合式 Linux Azure Arc 機器 |如果 VM 映射（OS）是在清單中定義且未安裝代理程式，請部署適用于 Linux 混合式 Azure Arc 機器的 Dependency agent。 |原則 |
| [預覽]：將 Dependency agent 部署至混合式 Windows Azure Arc 機器 |如果 VM 映射（OS）是在清單中定義且未安裝代理程式，請部署適用于 Windows 混合式 Azure Arc 機器的 Dependency agent。 |原則 |
| [預覽]：將 Log Analytics 代理程式部署至 Linux Azure Arc 機器 |如果 VM 映射（OS）是在清單中定義且未安裝代理程式，請部署適用于 Linux 混合式 Azure Arc 機器的 Log Analytics 代理程式。 |原則 |
| [預覽]：將 Log Analytics 代理程式部署至 Windows Azure Arc 機器 |如果 VM 映射（OS）是在清單中定義且未安裝代理程式，請部署適用于 Windows 混合式 Azure Arc 機器的 Log Analytics 代理程式。 |原則 |


### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Azure 虛擬機器擴展集的原則

下表列出 Azure 虛擬機器擴展集的原則定義。

|Name |描述 |類型 |
|-----|------------|-----|
|啟用虛擬機器擴展集的 Azure 監視器 |針對指定範圍（管理群組、訂用帳戶或資源群組）中的虛擬機器擴展集啟用 Azure 監視器。 請使用 Log Analytics 工作區作為參數。 注意：如果您的擴展集升級原則設定為 [手動]，請透過對其呼叫升級，將擴充功能套用至集合中的所有 Vm。 在 CLI 中，這是 `az vmss update-instances` 。 |方案 |
|在虛擬機器擴展集中進行 Audit Dependency agent 部署-未列出 VM 映射（OS） |如果 VM 映射（OS）未在清單中定義且未安裝代理程式，則將虛擬機器擴展集報告為不相容。 |原則 |
|在虛擬機器擴展集中進行 Audit Log Analytics 代理程式部署-未列出 VM 映射（OS） |如果 VM 映射（OS）未在清單中定義且未安裝代理程式，則將虛擬機器擴展集報告為不相容。 |原則 |
|為 Linux 虛擬機器擴展集部署 Dependency Agent |如果 VM 映射（OS）是在清單中定義且未安裝代理程式，請部署適用于 Linux 虛擬機器擴展集的 Dependency agent。 |原則 |
|為 Windows 虛擬機器擴展集部署 Dependency Agent |如果 VM 映射（OS）是在清單中定義且未安裝代理程式，請部署 Windows 虛擬機器擴展集的 Dependency agent。 |原則 |
|為 Linux 虛擬機器擴展集部署 Log Analytics 代理程式 |如果 VM 映射（OS）是在清單中定義且未安裝代理程式，請部署適用于 Linux 虛擬機器擴展集的 Log Analytics 代理程式。 |原則 |
|為 Windows 虛擬機器擴展集部署 Log Analytics 代理程式 |如果 VM 映射（OS）是在清單中定義且未安裝代理程式，請部署適用于 Windows 虛擬機器擴展集的 Log Analytics 代理程式。 |原則 |

獨立原則 (未包含在計畫中) 如下所述：

|Name |描述 |類型 |
|-----|------------|-----|
|適用于 VM 的 Audit Log Analytics 工作區-報告不相符 |如果 Vm 未記錄至原則或方案指派中指定的 Log Analytics 工作區，請將它們報告為不相容。 |原則 |

### <a name="assign-the-azure-monitor-initiative"></a>指派 Azure 監視器計畫

若要從**適用於 VM 的 Azure 監視器原則涵蓋範圍**] 頁面建立原則指派，請遵循下列步驟。 若要了解如何完成這些步驟，請參閱 [從 Azure 入口網站建立原則指派](../../governance/policy/assign-policy-portal.md)。

當您指派原則或方案時，在指派中選取的範圍可能是此處所列的範圍或其子集。 例如，您可能已為訂用帳戶（原則範圍）建立指派，而不是管理群組（涵蓋範圍範圍）。 在此情況下，涵蓋範圍百分比會指出原則或方案範圍中的 Vm 除以涵蓋範圍內的 Vm。 在另一種情況下，您可能已排除部分 Vm 或資源群組，或來自原則範圍的訂用帳戶。 如果是空的，表示原則或方案不存在，或者您沒有許可權。 [**指派狀態**] 底下會提供資訊。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在 Azure 入口網站中，選取 [監視]。 

3. 選擇 [**深入**解析] 區段中的 [**虛擬機器**]。
 
4. 選取 [**開始**使用] 索引標籤。在頁面上，選取 [**管理原則涵蓋範圍**]。

5. 從資料表中選取管理群組或訂用帳戶。 選取 [**範圍**]，方法是選取省略號（...）。在此範例中，範圍會將原則指派限制為要強制執行的虛擬機器群組。

6. 在 [ **Azure 原則指派**] 頁面上，它會預先填入方案**啟用適用於 VM 的 Azure 監視器**。 
    [**指派名稱**] 方塊會自動填入方案名稱，但您可以變更它。 您也可以新增選擇性的描述。 [**指派者**] 方塊會根據登入的使用者自動填入。 這是選擇性的值。

7. (選擇性) 若要從範圍中移除一或多個資源，請選取 [排除項目]****。

8. 在支援之區域的 [Log Analytics 工作區]**** 下拉式清單中，選取一個工作區。

   > [!NOTE]
   > 如果工作區超出指派的範圍之外，請將 [Log Analytics 參與者]** 權限授與原則指派的主體識別碼。 如果您沒有這麼做，您可能會看到部署失敗，例如 `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` 授與存取權、查看[如何手動設定受控識別](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity)。
   > 
   >  [**受控識別**] 核取方塊已選取，因為所指派的計畫包含具有*deployIfNotExists*效果的原則。
    
9. 在 [管理身分識別位置]**** 下拉式清單中，選取適當的區域。

10. 選取 [**指派**]。

建立指派之後，**適用於 VM 的 Azure 監視器原則涵蓋範圍**頁面會更新**指派涵蓋範圍**、**指派狀態**、**相容的 vm**和**合規性狀態**，以反映變更。 

下列矩陣會對應計畫的每個可能的合規性狀態。  

| 合規性狀態 | Description | 
|------------------|-------------|
| **相容** | 範圍中的所有 Vm 都已部署 Log Analytics 和 Dependency agent。|
| **不符合規範** | 並非範圍中的所有 Vm 都已部署 Log Analytics 和相依性代理程式，而且可能需要進行補救。|
| **未啟動** | 已新增新的指派。 |
| **鎖定** | 您沒有管理群組的足夠許可權。<sup>1</sup> | 
| **空白** | 未指派任何原則。 | 

<sup>1</sup>如果您沒有管理群組的存取權，請要求擁有者提供存取權。 或者，透過子管理群組或訂用帳戶來查看合規性和管理指派。 

下表對應計畫的每個可能指派狀態。

| 指派狀態 | Description | 
|------------------|-------------|
| 「成功」 | 範圍中的所有 Vm 都已部署 Log Analytics 和 Dependency agent。|
| **警告** | 訂用帳戶不在管理群組底下。|
| **未啟動** | 已新增新的指派。 |
| **鎖定** | 您沒有管理群組的足夠許可權。<sup>1</sup> | 
| **空白** | 不存在任何 Vm，或未指派原則。 | 
| **動作** | 指派原則或編輯指派。 | 

<sup>1</sup>如果您沒有管理群組的存取權，請要求擁有者提供存取權。 或者，透過子管理群組或訂用帳戶來查看合規性和管理指派。

## <a name="review-and-remediate-the-compliance-results"></a>檢閱和補救合規性結果

下列範例是針對 Azure VM，但也適用于虛擬機器擴展集。 若要瞭解如何檢查相容性結果，請參閱[識別不符合規範的結果](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources)。 在 [**適用於 VM 的 Azure 監視器原則涵蓋範圍**] 頁面上，從資料表中選取 [管理群組] 或 [訂用帳戶]。 藉由選取省略號（...）來選取 [**查看相容性**]。   

![Azure VM 的原則合規性](./media/vminsights-enable-at-scale-policy/policy-view-compliance.png)

根據方案中包含的原則結果，Vm 在下列案例中會回報為不相容：

* 未部署 Log Analytics 代理程式或相依性代理程式。  
    此對於包含現有 VM 的範圍來說是典型案例。 若要減輕此問題，請在不符合規範的原則上[建立補救](../../governance/policy/how-to/remediate-resources.md)工作，以部署必要的代理程式。  
    - 部署適用於 Linux VM 的 Dependency Agent
    - 部署適用於 Windows VM 的 Dependency Agent
    - 部署適用於 Linux VM 的 Log Analytics 代理程式
    - 部署適用於 Windows VM 的 Log Analytics 代理程式

* 在原則定義中找不到 VM 映射（OS）。  
    部署原則的準則僅包含從已知 Azure VM 映像部署的 VM。 請參閱文件以了解 VM OS 是否受到支援。 如果不受支援，請複製部署原則，並加以更新/修改，使映像符合規範。  
    - Audit Dependency agent 部署-未列出的 VM 映射（OS）
    - Audit Log Analytics 代理程式部署–未列出的 VM 映射（OS）

* VM 未登入指定的 Log Analytics 工作區。  
    計畫範圍內的部分 VM 可以登入未在原則指派中指定的 Log Analytics 工作區。 此原則是一種工具，用來識別哪些 Vm 會回報給不相容的工作區。  
    - 適用于 VM 的 Audit Log Analytics 工作區-報告不相符

## <a name="edit-an-initiative-assignment"></a>編輯方案指派

將計畫指派給管理群組或訂用帳戶之後，您可以隨時進行編輯，以修改下列屬性：

- 指派名稱
- Description
- 指派者
- Log Analytics 工作區
- 例外狀況

## <a name="next-steps"></a>後續步驟

現在已針對您的虛擬機器啟用監視，此資訊可透過適用於 VM 的 Azure 監視器進行分析。 

- 若要檢視探索到的應用程式相依性，請參閱[檢視適用於 VM 的 Azure 監視器對應](vminsights-maps.md)。 

- 若要找出 VM 效能的瓶頸和整體使用率，請參閱[查看 AZURE VM 效能](vminsights-performance.md)。 
