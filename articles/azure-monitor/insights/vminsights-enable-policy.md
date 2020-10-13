---
title: 使用 Azure 原則啟用適用於 VM 的 Azure 監視器
description: 說明如何使用 Azure 原則來啟用多部 Azure 虛擬機器或虛擬機器擴展集的適用於 VM 的 Azure 監視器。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 240c96016304c009c36485869ac15f5f38076fb7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90088284"
---
# <a name="enable-azure-monitor-for-vms-by-using-azure-policy"></a>使用 Azure 原則啟用適用於 VM 的 Azure 監視器
本文說明如何啟用 Azure 虛擬機器的適用於 VM 的 Azure 監視器，或使用 Azure 原則以 Azure Arc (preview) 連線的混合式虛擬機器。 Azure 原則可讓您指派原則定義，以在您的 Azure 環境中安裝所需的代理程式以進行適用於 VM 的 Azure 監視器，並在每個虛擬機器建立時自動啟用 Vm 的監視功能。 適用於 VM 的 Azure 監視器提供一項功能，可讓您在環境中探索及補救不符合規範的 Vm。 使用此功能，而不是直接使用 Azure 原則。

如果您不熟悉 Azure 原則，請 [使用 Azure 原則，取得大規模部署 Azure 監視器](../deploy-scale.md)的簡介。

> [!NOTE]
> 若要使用 Azure 原則搭配 Azure 虛擬機器擴展集，或直接使用 Azure 原則來啟用 Azure 虛擬機器，請參閱 [使用 Azure 原則大規模部署 Azure 監視器](../deploy-scale.md#azure-monitor-for-vms)。

## <a name="prerequisites"></a>必要條件
- [建立並設定 Log Analytics 工作區](vminsights-configure-workspace.md)。
- 請參閱 [支援的作業系統](vminsights-enable-overview.md#supported-operating-systems) ，以確保支援您所啟用之虛擬機器或虛擬機器擴展集的作業系統。 


## <a name="azure-monitor-for-vms-initiative"></a>適用於 VM 的 Azure 監視器方案
適用於 VM 的 Azure 監視器提供內建原則定義，以在 Azure 虛擬機器上安裝 Log Analytics 代理程式和 Dependency agent。 此方案可 **讓適用於 VM 的 Azure 監視器** 包含這些原則定義。 將此方案指派給管理群組、訂用帳戶或資源群組，以在該範圍內的任何 Windows 或 Linux Azure 虛擬機器上自動安裝代理程式。

## <a name="open-policy-coverage-feature"></a>開啟原則涵蓋範圍功能
若要存取**適用於 VM 的 Azure 監視器原則涵蓋範圍**，請在 Azure 入口網站中的 [ **Azure 監視器**] 功能表中，移至**虛擬機器**。 選取 [**其他上線選項**]，然後在 [**啟用使用原則**] 下**啟用**。

[![從 Vm Azure 監視器開始索引標籤](./media/vminsights-enable-at-scale-policy/get-started-page.png)](./media/vminsights-enable-at-scale-policy/get-started-page.png#lightbox)

## <a name="create-new-assignment"></a>建立新的指派
如果您還沒有指派，請按一下 [ **指派原則**] 建立一個新的。

[![建立指派](media/vminsights-enable-at-scale-policy/create-assignment.png)](media/vminsights-enable-at-scale-policy/create-assignment.png#lightbox)

這是在 Azure 原則中指派方案的相同頁面，不同之處在于它會以您選取的範圍和 **啟用適用於 VM 的 Azure 監視器** 方案定義進行硬式編碼。 您可以選擇性地變更 **指派名稱** ，並加入 **描述**。 如果您想要提供排除給範圍，請選取 [ **排除** ]。 例如，您的範圍可以是管理群組，而且您可以在該管理群組中指定要從指派排除的訂用帳戶。

[![指派方案](media/vminsights-enable-at-scale-policy/assign-initiative.png)](media/vminsights-enable-at-scale-policy/assign-initiative.png#lightbox)

在 [ **參數** ] 頁面上，選取要供指派中的所有虛擬機器使用的 **Log Analytics 工作區** 。 如果您想要為不同的虛擬機器指定不同的工作區，則必須建立多個指派，各有各自的範圍。 

   > [!NOTE]
   > 如果工作區超出指派的範圍之外，請將 [Log Analytics 參與者]** 權限授與原則指派的主體識別碼。 如果您沒有這麼做，可能會看到部署失敗，例如 `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...`

[![工作區](media/vminsights-enable-at-scale-policy/assignment-workspace.png)](media/vminsights-enable-at-scale-policy/assignment-workspace.png#lightbox)

按一下 [ **審核 + 建立** ]，以先檢查指派的詳細資料，再按一下 [ **建立** ] 以建立指派。 此時請勿建立補救工作，因為您很可能需要多項補救工作才能啟用現有的虛擬機器。 請參閱下方的 [補救合規性結果](#remediate-compliance-results) 。

## <a name="review-compliance"></a>檢查合規性
一旦建立指派之後，您就可以在整個管理群組和訂用帳戶中，檢查並管理「 **啟用適用於 VM 的 Azure 監視器** 」方案的涵蓋範圍。 這會顯示每個管理群組或訂用帳戶中有多少部虛擬機器，以及其合規性狀態。

[![適用於 VM 的 Azure 監視器管理原則] 頁面](media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)](media/vminsights-enable-at-scale-policy/manage-policy-page-01.png#lightbox)


下表提供此視圖中資訊的描述。

| 函式 | 描述 | 
|----------|-------------| 
| **範圍** | 您擁有或繼承的管理群組和訂用帳戶，可以向下切入管理群組階層。|
| **角色** | 您在範圍中的角色，可能是讀者、擁有者或參與者。 如果您有訂用帳戶的存取權，但無法存取其所屬的管理群組，這將會是空白的。 此角色可決定您可以看到的資料，以及您可以在指派原則或方案 (擁有者) 、編輯它們或查看合規性方面執行的動作。 |
| **Vm 總計** | 該範圍中的 Vm 總數（不論其狀態為何）。 對於管理群組，這是在訂用帳戶或子管理群組底下嵌套的 Vm 總計。 |
| **指派涵蓋範圍** | 方案涵蓋的 Vm 百分比。 |
| **指派狀態** | **成功** -範圍中的所有 vm 都已部署 Log Analytics 和相依性代理程式。<br>**警告** -訂用帳戶不在管理群組下。<br>**未啟動** -已新增新的指派。<br>**鎖定** -您對管理群組沒有足夠的許可權。<br>**空白** -沒有 vm 存在或未指派原則。 |
| **符合規範的 Vm** | 符合規範的 Vm 數目，也就是已安裝 Log Analytics 代理程式和 Dependency agent 的 Vm 數目。 如果沒有任何指派、範圍中沒有任何 Vm，或沒有適當的許可權，這將會是空白的。 |
| **遵循** | 整體合規性號碼是符合規範的相異資源與所有相異資源總和的總和。 |
| **合規性狀態** | **相容** -範圍虛擬機器中的所有 vm 都已部署記錄分析和相依性代理程式，或受指派範圍內的任何新 vm 尚未進行評估。<br>**不相容** -有 vm 已經過評估，但未啟用，而且可能需要補救。<br>**未啟動** -已新增新的指派。<br>**鎖定** -您對管理群組沒有足夠的許可權。<br>**空白** -未指派任何原則。  |


當您指派方案時，在指派中選取的範圍可能是列出的範圍或其中的子集。 比方說，您可能已建立訂用帳戶的指派 (原則範圍) 而不是 (涵蓋範圍) 的管理群組。 在此情況下， **指派涵蓋** 範圍的值表示方案範圍中的 vm 除以涵蓋範圍範圍內的 vm。 在另一種情況下，您可能已從原則範圍中排除部分 Vm、資源群組或訂用帳戶。 如果值是空白的，表示原則或方案不存在，或者您沒有許可權。 在 [ **指派狀態**] 下提供資訊。


## <a name="remediate-compliance-results"></a>補救合規性結果
此計畫會在建立或修改時套用至虛擬機器，但不會套用至現有的 Vm。 如果您的指派未顯示100% 合規性，請建立補救工作以評估及啟用現有的 Vm，選取省略號 ( ... ) 來選取 [ **查看相容性** ]。

[![視圖合規性](media/vminsights-enable-at-scale-policy/view-compliance.png)](media/vminsights-enable-at-scale-policy/view-compliance.png#lightbox)

[ **相容性** ] 頁面會列出符合指定篩選準則的指派，以及它們是否符合規範。 按一下 [指派] 以查看其詳細資料。

[![Azure VM 的原則合規性](./media/vminsights-enable-at-scale-policy/policy-view-compliance.png)](./media/vminsights-enable-at-scale-policy/policy-view-compliance.png#lightbox)

**方案合規性**頁面會列出計畫中的原則定義，以及每個原則定義是否符合規範。

[![合規性詳細資料](media/vminsights-enable-at-scale-policy/compliance-details.png)](media/vminsights-enable-at-scale-policy/compliance-details.png#lightbox)

按一下原則定義以查看其詳細資料。 原則定義會顯示為不符合規範的案例包括下列各項：

* 未部署 Log Analytics 代理程式或 Dependency agent。 建立補救工作以減輕風險。
* 原則定義中未識別 VM 映射 (作業系統) 。 部署原則的準則僅包含從已知 Azure VM 映像部署的 VM。 請參閱文件以了解 VM OS 是否受到支援。
* Vm 未記錄至指定的 Log Analytics 工作區。 方案範圍中的某些 Vm 會連接到 Log Analytics 工作區，而不是原則指派中指定的工作區。

[![原則合規性詳細資料](media/vminsights-enable-at-scale-policy/policy-compliance-details.png)](media/vminsights-enable-at-scale-policy/policy-compliance-details.png#lightbox)

若要建立補救工作以減少合規性問題，請按一下 [ **建立補救**工作]。 

[![新增補救工作](media/vminsights-enable-at-scale-policy/new-remediation-task.png)](media/vminsights-enable-at-scale-policy/new-remediation-task.png#lightbox)

按一下 [ **補救** ] 以建立補救工作，然後進行 **補救** 以啟動。 您很可能需要建立多個補救工作，每個原則定義都有一個。 您無法為方案建立補救工作。

[![螢幕擷取畫面：顯示監視的原則補救窗格 |虛擬機器。](media/vminsights-enable-at-scale-policy/remediation.png)](media/vminsights-enable-at-scale-policy/remediation.png#lightbox)


補救工作完成後，您的 Vm 應該符合針對適用於 VM 的 Azure 監視器安裝和啟用的代理程式。 

## <a name="next-steps"></a>後續步驟

現在已為您的虛擬機器啟用監視，此資訊可透過適用於 VM 的 Azure 監視器進行分析。 

- 若要檢視探索到的應用程式相依性，請參閱[檢視適用於 VM 的 Azure 監視器對應](vminsights-maps.md)。 
- 若要找出 VM 效能的瓶頸和整體使用率，請參閱 [View AZURE VM performance](vminsights-performance.md)。 
