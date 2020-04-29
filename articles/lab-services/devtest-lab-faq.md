---
title: Azure DevTest Labs 常見問題集 | Microsoft Docs
description: 本文提供一些關於 Azure DevTest Labs 的常見問題（FAQ）的解答。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: afe83109-b89f-4f18-bddd-b8b4a30f11b4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: de99e9b1e4adceaf08beaf8ad3b5ea114b31a586
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79270780"
---
# <a name="azure-devtest-labs-faq"></a>Azure DevTest Labs 常見問題集
獲得一些關於 Azure DevTest Labs 最常見問題的解答。

## <a name="blog-post"></a>Blog 文章
我們的 DevTest Labs 小組 blog 已于2019年3月20日淘汰。 

### <a name="where-can-i-track-feature-updates-from-now-on"></a>我可以從何處追蹤功能更新？
從現在開始，我們將在 Azure blog 和 Azure 更新上張貼功能更新和資訊性的 blog 文章。 這些 blog 文章也會在需要的地方連結到我們的檔。

訂閱[DevTest Labs Azure Blog](https://azure.microsoft.com/blog/tag/azure-devtest-labs/)和[DevTest Labs azure 更新](https://azure.microsoft.com/updates/?product=devtest-lab)，以隨時掌握 DevTest Labs 的新功能。

### <a name="what-happens-to-the-existing-blog-posts"></a>現有的 blog 文章會發生什麼事？
我們目前正致力於將現有的 blog 文章（不包括中斷更新）遷移至我們的[DevTest Labs 檔](devtest-lab-overview.md)。 當 MSDN blog 已淘汰時，系統會將其重新導向至 DevTest Labs 的檔總覽。 一旦重新導向之後，您就可以在 [篩選依據] 標題中搜尋您要尋找的文章。 我們尚未遷移所有貼文，但必須在本月月底完成。 


### <a name="where-do-i-see-outage-updates"></a>我可以在何處看到中斷更新？
我們將使用我們從現在開始的 Twitter 控制碼來張貼中斷更新。 請在 Twitter 上追蹤我們，以取得中斷和已知錯誤的最新更新。

### <a name="twitter"></a>Twitter
我們的 Twitter 控制碼：[@azlabservices](https://twitter.com/azlabservices)

## <a name="general"></a>一般
### <a name="what-if-my-question-isnt-answered-here"></a>如果這裡沒有解答我的問題該怎麼辦？
如果此處未列出您的問題，請讓我們知道，讓我們可以協助您找到答案。

- 將問題張貼在此常見問題集尾端。
- 若要觸及更多讀者，可將問題張貼在 [Azure DevTest Labs MSDN 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs)。 與 Azure DevTest Labs 小組和社群的其他成員交流。
- 若要提出功能要求，請將您的要求和想法提交到 [Azure DevTest Labs 使用者心聲](https://feedback.azure.com/forums/320373-azure-devtest-labs)。

### <a name="what-is-a-microsoft-account"></a>什麼是 Microsoft 帳戶？
Microsoft 帳戶是您使用 Microsoft 裝置和服務來執行幾乎所有作業時所使用的帳戶。 這是您用來登入 Skype、Outlook.com、OneDrive、Windows phone、Azure 和 Xbox Live 的電子郵件地址和密碼。 單一帳戶意味著不論您移到哪個裝置，檔案、相片、連絡人及設定都會跟著您移動。

> [!NOTE]
> Microsoft 帳戶以前稱為「Windows Live ID」。

### <a name="why-should-i-use-azure-devtest-labs"></a>為何應使用 Azure DevTest Labs？
Azure DevTest Labs 可讓您的小組節省時間和金錢。 開發人員可以使用數種不同的基底物件建立自己的環境。 他們也可以使用構件快速部署和設定應用程式。 透過使用自訂映像和公式，您可以將虛擬機器 (VM) 儲存為範本，並輕易地在小組中予以重現。 DevTest Labs 還提供數個可設定的原則，以供實驗室系統管理員用來減少浪費並管理小組的環境。 這些原則包括自動關機、成本臨界值、每一使用者的 VM 數上限和 VM 大小上限。 如需更深入的 DevTest Labs 說明，請參閱[概觀](devtest-lab-overview.md)或觀看[簡介影片](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs)。

### <a name="what-does-worry-free-self-service-mean"></a>「零煩惱的自助方式」是什麼意思？
零煩惱的自助方式表示開發人員和測試人員可以視需要建立自己的環境。 系統管理員的安全性是知道 DevTest Labs 可以協助設定適當的存取權、將浪費降到最低，以及控制成本。 系統管理員可以指定允許的 VM 大小、VM 數目上限，以及何時啟動和關閉 VM。 DevTest Labs 也可讓成本監視和警示設定動作變得簡單，以便協助您隨時注意實驗室資源的使用情形。

### <a name="how-can-i-use-devtest-labs"></a>如何使用 DevTest Labs？
每當您需要開發或測試環境，而且想要快速重現它們，或使用節省成本的原則進行管理時，DevTest Labs 就很有用。
以下是我們的客戶使用 DevTest Labs 的一些案例︰

- 集中在一個地方管理開發和測試環境。 使用原則來降低成本，並建立自訂映像讓整個小組共用建置。
- 使用自訂映像開發應用程式，以儲存整個開發階段的磁碟狀態。
- 依進度追蹤成本。
- 建立大量測試環境以進行品質保證測試。
- 使用構件和公式在各種環境輕鬆地設定和重現應用程式。
- 散發 VM 以進行黑客松 (Hackathon，共同作業的開發或測試工作)，然後在活動結束時輕鬆地解除佈建。

### <a name="how-am-i-billed-for-devtest-labs"></a>DevTest Labs 如何收費？
DevTest Labs 是免費的服務。 在 DevTest Labs 建立實驗室和設定原則、範本與構件都是免費的。 您只需要針對在實驗室內使用到的 Azure 資源 (例如 VM、儲存體帳戶和虛擬網路) 支付費用。 如需實驗室資源成本的詳細資訊，請參閱 [Azure DevTest Labs 定價](https://azure.microsoft.com/pricing/details/devtest-lab/)。

## <a name="security"></a>安全性

### <a name="what-are-the-different-security-levels-in-devtest-labs"></a>DevTest Labs 中有哪些不同的安全性層級？
安全性存取權是由角色型存取控制 (RBAC) 所決定。 若要了解存取權的運作方式，了解 RBAC 所定義的權限、角色和範圍之間的差異將有所幫助。

- **權限**：權限是針對特定動作所定義的存取權。 例如，權限可以是對所有 VM 的讀取權限。
- **角色**：角色是一組可以分組並指派給使用者的權限。 例如，具有訂用帳戶擁有者角色的使用者可存取訂用帳戶內的所有資源。
- **範圍**：範圍是 Azure 資源階層中的層級。 例如，範圍可以是一個資源群組、單一實驗室或整個訂用帳戶。

在 DevTest Labs 的範圍內有兩種角色類型可以定義使用者的權限︰

- **實驗室擁有者**：實驗室擁有者擁有實驗室內所有資源的存取權。 實驗室擁有者可以修改原則、讀取和寫入任何 VM、變更虛擬網路等等。
- **實驗室使用者**：實驗室使用者可以檢視所有實驗室資源，例如 VM、原則和虛擬網路。 不過，實驗室使用者無法修改原則或任何由其他使用者所建立的 Vm。

您也可以在 DevTest Labs 中建立自訂角色。 若要了解如何在 DevTest Labs 建立自訂角色，請參閱[將特定實驗室原則的權限授與使用者](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)。

因為範圍是階層形式，當使用者擁有特定範圍的權限時，就會自動獲得該範圍的每個較低層級範圍的權限。 例如，如果有使用者指派給訂用帳戶擁有者角色，該使用者就可以存取訂用帳戶中的所有資源。 這些資源包括 Vm、虛擬網路和實驗室。 訂用帳戶擁有者會自動繼承實驗室擁有者角色。 不過，若情形顛倒過來就不成立。 實驗室擁有者可存取實驗室，而實驗室是比訂用帳戶層級還低的範圍。 因此，實驗室擁有者看不到 Vm、虛擬網路或實驗室以外的任何其他資源。

### <a name="how-do-i-define-role-based-access-control-for-my-devtest-labs-environments-to-ensure-that-it-can-govern-while-developerstest-can-do-their-work"></a>如何為我的 DevTest Labs 環境定義角色型存取控制，以確保 IT 可以治理，而開發人員/測試同時還能執行其工作？
有一個廣泛的模式，但詳細資料取決於您的組織。

中央 IT 應該只擁有所需的專案，並讓專案和應用程式小組擁有所需的控制層級。 一般而言，這表示中央 IT 擁有訂用帳戶並會處理核心 IT 函式，例如網路設定。 這組適用於訂用帳戶的**擁有者**應該很小。 這些擁有者可以在需要時提名其他擁有者，或套用訂用帳戶層級原則，例如「沒有公用 IP」。

可能有使用者子集需要跨訂用帳戶進行存取，例如第 1 層或第 2 層支援。 在此情況下，我們建議您為這些使用者授與**參與者**存取權，讓他們可以管理資源，但不提供使用者存取權或調整原則。

DevTest Labs 資源應該由接近專案/應用程式小組的擁有者所擁有。 這是因為他們瞭解電腦的需求，以及所需的軟體。 在大多數組織中，此 DevTest Labs 資源的擁有者通常是專案/開發潛在客戶。 此擁有者可以管理實驗室環境內的使用者和原則，而且可以管理 DevTest Labs 環境中的所有 VM。

專案/應用程式小組成員應新增至**DevTest Labs 使用者**角色。 這些使用者可以建立虛擬機器 (使用實驗室和訂用帳戶層級原則來內建)。 他們也可以管理自己的虛擬機器。 他們無法管理屬於其他使用者的虛擬機器。

如需詳細資訊，請參閱[Azure 企業 scaffold-規定的訂用帳戶治理檔](/azure/architecture/cloud-adoption/appendix/azure-scaffold)。


### <a name="how-do-i-create-a-role-to-allow-users-to-do-a-specific-task"></a>如何? 建立可讓使用者執行特定工作的角色嗎？
如需如何建立自訂角色並指派權限給該角色的完整文章，請參閱[將特定實驗室原則的權限授與使用者](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)。 以下指令碼範例會建立「DevTest Labs 進階使用者」**** 角色，其具有啟動和停止實驗室中所有 VM 的權限︰


```powershell
$policyRoleDef = Get-AzRoleDefinition "DevTest Labs User"
$policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
$policyRoleDef.Id = $null
$policyRoleDef.Name = "DevTest Labs Advanced User"
$policyRoleDef.IsCustom = $true
$policyRoleDef.AssignableScopes.Clear()
$policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
$policyRoleDef = New-AzRoleDefinition -Role $policyRoleDef  
```

### <a name="how-can-an-organization-ensure-corporate-security-policies-are-in-place"></a>組織如何確保公司安全性原則已就緒？
組織可能藉由執行下列動作來達到此目的：

- 開發和發佈完善的安全性原則。 該原則會闡述與使用軟體、雲端資產相關聯的可接受使用規則。 它也會定義哪些明顯違反了該原則。
- 開發自訂映像、自訂成品及部署程序，允許在使用 Active Directory 定義的安全性領域內進行協調流程。 此方法會強制執行公司界限，並設定一組常用的環境控制項。 這些控制項會以開發人員在開發期間可考量的環境為依據，並遵循安全開發生命週期作為其整體程序的一部分。 目標也是提供不太嚴格的環境，可能會妨礙開發，而是一組合理的控制項。 位於包含實驗室虛擬機器之組織單位 (OU) 的群組原則，可能是在生產環境中找到的總計群組原則子集。 或者，它們可以是一組額外的集合，可正確地降低任何已識別出的風險。

### <a name="how-can-an-organization-ensure-data-integrity-to-ensure-that-remoting-developers-cant-remove-code-or-introduce-malware-or-unapproved-software"></a>組織如何確保資料完整性，以確保遠端開發人員無法移除程式碼，或無法引進惡意程式碼或未經准的軟體？
有數個控制層，可用來為從遠端在 DevTest Labs 中共同作業的外部顧問、約聘人員或員工降低此威脅。

如先前所述，第一個步驟必須起草並定義一個可接受的使用原則，清晰地概述有人違反原則時的後果。

遠端存取的第一層控制是透過未直接連線至實驗室的 VPN 連線來套用遠端存取原則。

第二個控制層是套用一組群組原則物件，以避免透過遠端桌面進行複製與貼上。 您可以實作網路原則，不允許環境的輸出服務，例如離開該環境的 FTP 和 RDP 服務。 使用者定義的路由可以強制所有的 Azure 網路流量回到內部部署，但路由無法將所有可能允許上傳資料的 Url 加以考慮，除非透過 proxy 進行控制以掃描內容和會話。 公用 IP 可能會在支援 DevTest Labs 的虛擬網路內受到限制，不允許對外部網路資源進行橋接。

最後，必須在整個組織中套用相同類型的限制，這會考慮可能會接受內容張貼的所有可能方法的卸載式媒體或外部 Url。 請洽詢您的安全性專業人員來檢閱並實作安全性原則。 如需其他建議，請參閱 [Microsoft 網路安全性](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs)。

## <a name="lab-configuration"></a>實驗室組態

### <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>如何從 Resource Manager 範本建立實驗室？
我們提供一個[實驗室 Azure Resource Manager 範本的 GitHub 存放庫](https://azure.microsoft.com/resources/templates/101-dtl-create-lab)，您可以依原狀部署，或是加以修改來為您的實驗室建立自訂範本。 每個範本都有一個連結，可將實驗室部署在您自己的 Azure 訂用帳戶中。 或者，您可以自訂範本並[使用 PowerShell 或 Azure CLI 來進行部署](../azure-resource-manager/templates/deploy-powershell.md)。


### <a name="can-i-have-all-virtual-machines-to-be-created-in-a-common-resource-group-instead-having-each-machine-in-its-own-resource-group"></a>我是否可以在通用資源群組中建立所有虛擬機器，而不是讓每一部電腦都位於自己的資源群組中？
是的，身為實驗室擁有者，您可以讓實驗室處理您的資源群組配置，或在您指定的通用資源群組中建立所有虛擬機器。

個別的資源群組案例：
-   DevTest Labs 會為您啟動的每個公用/私人 IP 虛擬機器建立新的資源群組
-   DevTest Labs 會針對屬於相同大小的共用 IP 機器建立資源群組。

常見的資源群組案例：
-   所有虛擬機器都會在您指定的一般資源群組中啟動。 深入瞭解[實驗室的資源群組配置](https://aka.ms/RGControl)。

### <a name="how-do-i-maintain-a-naming-convention-across-my-devtest-labs-environment"></a>如何在 DevTest Labs 環境中維護命名慣例？
您可能想要將目前的企業命名慣例擴充到 Azure 作業，並使其在整個 DevTest Labs 環境中保持一致。 部署 DevTest Labs 時，我們建議您具備特定的起始原則。 您可以透過中央指令碼和 JSON 範本來部署這些原則，以強制保持一致性。 命名原則可透過在訂用帳戶層級套用的 Azure 原則來實作。 如需適用於 Azure 原則的 JSON 範例，請參閱 [Azure 原則範例](../governance/policy/samples/index.md)。

### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>在相同的訂用帳戶下可建立幾個實驗室？
每個訂用帳戶可以建立的實驗室數目並無特定限制， 但每個訂用帳戶可使用的資源數量則有限制。 您可以閱讀 [Azure 訂用帳戶的限制和配額](../azure-resource-manager/management/azure-subscription-service-limits.md)和[如何增加這些限制值](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests)。


### <a name="how-many-vms-can-i-create-per-lab"></a>每個實驗室可以建立幾個 VM？
每個實驗室可以建立的 VM 數目並無特定限制， 不過，每個訂用帳戶可使用的資源 (VM 核心、公用 IP 位址等) 則有限制。 您可以閱讀 [Azure 訂用帳戶的限制和配額](../azure-resource-manager/management/azure-subscription-service-limits.md)和[如何增加這些限制值](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests)。

### <a name="how-do-i-determine-the-ratio-of-users-per-lab-and-the-overall-number-of-labs-that-are-needed-across-an-organization"></a>如何? 決定每個實驗室的使用者與整個組織所需的實驗室整體數目的比率嗎？
我們建議將與相同開發專案相關聯的業務單位和開發群組關聯至同一個實驗室。 這樣就能將相同類型的原則、映像及關機原則套用到這兩個群組。

您可能也需要考慮地理界限。 例如，北美地區（美國）的開發人員可能會使用在東部美國2中布建的實驗室。 此外，可能會將位於德克薩斯州達拉斯和科羅拉多州丹佛的開發人員重新導向來使用位於美國中南部的資源。 如果要與外部協力廠商共同合作，則可能會將他們指派到不是由內部開發人員所使用的實驗室。

您也可以將實驗室用於 Azure DevOps Projects 內的特定專案。 然後，透過指定的 Azure Active Directory 群組套用安全性，以允許您存取這兩組資源。 指派到實驗室的虛擬網路可以是另一個要合併使用者的界限。

### <a name="how-can-we-prevent-the-deletion-of-resources-within-a-lab"></a>如何防止刪除實驗室內的資源？
我們建議您在實驗室層級設定適當的權限，這樣一來，就只有授權的使用者可以刪除資源或變更實驗室原則。 開發人員應該放置於 **DevTest Labs 使用者**群組內。 首席開發人員或基礎結構負責人都應該是 **DevTest Labs 擁有者**。 我們建議您只有兩個實驗室擁有者。 此原則會擴充到程式碼存放庫以避免損毀。 實驗室使用者具有使用資源的許可權，但無法更新實驗室原則。 請參閱下列文章，其中列出每個內建群組在實驗室中所擁有的角色和權限：[在 Azure DevTest Labs 中新增擁有者和使用者](devtest-lab-add-devtest-user.md)。

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>如何共用我實驗室的直接連結？

1. 在[Azure 入口網站](https://portal.azure.com)中，移至實驗室。
2. 從瀏覽器複製**實驗室 URL** ，然後與您的實驗室使用者共用。

> [!NOTE]
> 實驗室使用者是具有 Microsoft 帳戶的外部使用者，但不屬於組織的 Active Directory 執行個體成員，該使用者在嘗試存取共用的連結時可能會看到錯誤訊息。 如果外部使用者看到錯誤訊息，請要求使用者先在 Azure 入口網站右上角選取其名稱。 然後，使用者就可以在功能表的 [目錄] 區段中，選取實驗室所在的目錄。

## <a name="virtual-machines"></a>虛擬機器

### <a name="why-cant-i-see-vms-on-the-virtual-machines-page-that-i-see-in-devtest-labs"></a>為什麼我在 DevTest Labs 中看到的 [虛擬機器] 頁面上看不到 Vm？
當您在 DevTest Labs 中建立 VM 時，您會獲得存取該 VM 的許可權。 您可以在 [實驗室] 頁面和 [**虛擬機器**] 頁面上查看 VM。 指派給**DevTest Labs 擁有**者角色的使用者可以在實驗室的 [**所有虛擬機器**] 頁面上看到所有在實驗室中建立的 vm。 不過，具有**DevTest Labs 使用者**角色的使用者不會自動獲得其他使用者所建立之 VM 資源的讀取權限。 因此，這些 Vm 不會顯示在 [**虛擬機器**] 頁面上。


### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>如何從相同範本一次建立多個 VM？
有兩個選項可供您透過相同範本同時建立多個 VM：

- 您可以使用 [Azure DevOps 工作擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)進行以下作業。
- 您可以在建立 VM 時[產生 Resource Manager 範本](devtest-lab-add-vm.md#save-azure-resource-manager-template)，並[從 Windows PowerShell 部署 Resource Manager 範本](../azure-resource-manager/templates/deploy-powershell.md)。
- 您也可以指定要在虛擬機器建立期間建立的多個機器實例。 若要深入瞭解如何建立虛擬機器的多個實例，請參閱[建立實驗室虛擬機器](devtest-lab-add-vm.md)上的檔。

### <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>如何將現有 Azure VM 移到 DevTest Labs 實驗室？
若要將現有 VM 複製到 DevTest Labs：

1.  使用 [Windows PowerShell 指令碼](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/Scripts/CopyVirtualMachines/CopyAzVHDFromVMToLab.ps1)來複製現有 VM 的 VHD 檔案。
2.  在您的 DevTest Labs 實驗室內建立[自訂映射](devtest-lab-create-template.md)。
3.  在實驗室中從自訂映像建立 VM。

### <a name="can-i-attach-multiple-disks-to-my-vms"></a>是否可以在 VM 連接多個磁碟？

是，您可以將多個磁碟連結至 VM。

### <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>如果我想要使用 Windows 作業系統映像進行測試，是否應購買 MSDN 訂用帳戶？
若要在 Azure 中使用 Windows 用戶端 OS 映射（Windows 7 或更新版本）進行開發或測試，請採取下列其中一個步驟：

- [購買 MSDN 訂閱](https://www.visualstudio.com/products/how-to-buy-vs)。
- 如果您有 Enterprise 合約，請以 [Enterprise 開發/測試供應項目](https://azure.microsoft.com/offers/ms-azr-0148p)建立 Azure 訂用帳戶。

如需每個 MSDN 供應項目之 Azure 點數的詳細資訊，請參閱 [Visual Studio 訂閱者的每月 Azure 點數](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。


### <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>如何自動進行刪除實驗室中所有 VM 的程序？
身為實驗室擁有者，您可以在 Azure 入口網站中刪除實驗室中的 Vm。 您也可以使用 PowerShell 指令碼刪除實驗室中的所有 VM。 在下列範例中，于 [**要變更**批註的值] 底下修改參數值。 您可以從 Azure 入口網站中的 [實驗室] 窗格抓取 subscriptionId、labResourceGroup 和 labName 值。

```powershell
# Delete all the VMs in a lab.

# Values to change:
$subscriptionId = "<Enter Azure subscription ID here>"
$labResourceGroup = "<Enter lab's resource group here>"
$labName = "<Enter lab name here>"

# Sign in to your Azure account.
Connect-AzAccount

# Select the Azure subscription that has the lab. This step is optional
# if you have only one subscription.
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab that has the VMs that you want to delete.
$lab = Get-AzResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Get the VMs from that lab.
$labVMs = Get-AzResource | Where-Object {
          $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
          $_.Name -like "$($lab.Name)/*"}

# Delete the VMs.
foreach($labVM in $labVMs)
{
    Remove-AzResource -ResourceId $labVM.ResourceId -Force
}
```

## <a name="environments"></a>環境

### <a name="how-can-i-use-resource-manager-templates-in-my-devtest-labs-environment"></a>如何在 DevTest Labs 環境中使用 Resource Manager 範本？
您可以使用[DevTest Labs 中的環境功能](devtest-lab-test-env.md)一文所述的步驟，將您的 Resource Manager 範本部署到 DevTest Labs 環境中。 基本上，您會將 Resource Manager 範本簽入到 Git 存放庫 (Azure Repos 或 GitHub)，並將[適用於範本的私人存放庫](devtest-lab-test-env.md)新增到實驗室。 如果您使用 DevTest Labs 來裝載開發機器，但如果您要建立可代表生產的預備環境，則此案例可能會很有用。

也值得注意的是，[每個實驗室] 或 [每個使用者] 選項的虛擬機器數目，只會限制在實驗室本身原本建立的機器數目，而不限於任何環境（Resource Manager 範本）。

## <a name="custom-images"></a>自訂映像

### <a name="how-can-i-set-up-an-easily-repeatable-process-to-bring-my-custom-organizational-images-into-a-devtest-labs-environment"></a>如何設定可輕鬆重複執行的程序，以便將自訂的組織映像帶入 DevTest Labs 環境？
請參閱[映射 Factory 模式的](https://sec.ch9.ms/ch9/8e8a/9ea0b8d4-b803-4f23-bca4-4808d9368e8a/dtlimagefactory_mid.mp4)這段影片。 此案例是一個進階案例，而提供的指令碼只是範例指令碼。 如果不需要任何變更，則您需要管理和維護環境中所使用的指令碼。

如需建立映射 factory 的詳細資訊，請參閱[在 Azure DevTest Labs 中建立自訂映射 factory](image-factory-create.md)。

### <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>自訂映像和公式有何差異？
自訂映射是受控映射。 公式則是可以透過其他設定來加以設定，然後儲存並重現的映像。 如果您想要使用相同的不可變基底映像快速建立幾個環境，自訂映像可能較合適。 如果您想要使用最新版本來重現 VM 的組態、將 VM 的組態重現為虛擬網路或子網路的一部分，或是將 VM 的組態重現為特定大小的 VM，則公式可能較合適。 如需更深入的說明，請參閱[比較 DevTest Labs 中的自訂映像和公式](devtest-lab-comparing-vm-base-image-types.md)。

### <a name="when-should-i-use-a-formula-vs-custom-image"></a>何時應使用公式，而何時應使用自訂映像？
一般而言，此案例中的決定因素是成本與重複使用。 如果您有許多使用者/實驗室需要在基底映射上有大量軟體的映射，您可以藉由建立自訂映射來降低成本。 這表示映射會建立一次。 它會減少虛擬機器的安裝時間，以及因為虛擬機器在安裝期間執行而產生的成本。

不過，另一個要注意的因素是對軟體套件進行變更的頻率。 如果您執行每日組建且要求軟體位於您使用者的虛擬機器上，請考慮使用公式，而非自訂映像。

如需更深入的說明，請參閱比較 DevTest Labs 中的[自訂映射和公式](devtest-lab-comparing-vm-base-image-types.md)。

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>如何自動執行上傳 VHD 檔案的程序以建立自訂映像？

若要自動上傳 VHD 檔案以建立自訂映像，您有兩個選擇：

- 使用 [AzCopy](../storage/common/storage-use-azcopy-v10.md) 將 VHD 檔案複製或上傳到與實驗室相關聯的儲存體帳戶。
- 使用[Azure 儲存體總管](../vs-azure-tools-storage-manage-with-storage-explorer.md)。 儲存體總管是在 Windows、OSX 和 Linux 上執行的獨立應用程式。

若要尋找與實驗室相關聯的目的地儲存體帳戶︰

1.  登入 [Azure 入口網站](https://portal.azure.com)。
2.  在左功能表上選取 [資源群組]****。
3.  尋找並選取與您的實驗室相關聯的資源群組。
4.  在 [概觀]**** 底下，選取其中一個儲存體帳戶。
5.  選取 [ **blob**]。
6.  在清單中尋找要上傳的項目。 如果不存在任何項目，請返回步驟 4，然後嘗試另一個儲存體帳戶。
7.  在 AzCopy 命令中使用 [URL]**** 作為目的地。

何時應使用 Azure Marketplace 映像，而何時應使用我自己自訂的組織映像？

### <a name="when-should-i-use-an-azure-marketplace-image-vs-my-own-custom-organizational-image"></a>何時應使用 Azure Marketplace 映像，而何時應使用我自己自訂的組織映像？
除非您具有特定考量或組織需求，否則預設應該使用 Azure Marketplace。 一些常見範例包括：

- 複雜的軟體安裝，需要包含應用程式作為基礎映像的一部分。
- 安裝和設定應用程式可能需要數小時的時間，這不會有效率地使用計算時間來加入 Azure Marketplace 映射。
- 開發人員和測試人員需要快速存取虛擬機器，並且想要將安裝新虛擬機器的時間縮至最短。
- 所有機器都必須具備的合規性或法規條件 (例如安全性原則)。
- 使用自訂映射應該不會被視為輕量。 它們會帶來額外的複雜性，因為您現在必須管理這些基礎基底映射的 VHD 檔案。 您也需要使用軟體更新來定期修補那些基本映像。 這些更新包括新的作業系統 (OS) 更新，以及軟體套件本身所需的任何更新或設定變更。

## <a name="artifacts"></a>構件

### <a name="what-are-artifacts"></a>何謂構件？
構件是可用來在 VM 中部署最新版本或開發工具的可自訂項目。 在建立 VM 時，請將構件連結至 VM。 VM 佈建好之後，構件就會部署並設定 VM。 在我們的[公用 GitHub 存放庫](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)中可取得各種預先存在的構件。 您也可以[撰寫您自己的構件](devtest-lab-artifact-author.md)。

### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>在建立 VM 時，我的構件失敗了。 我該如何進行疑難排解？
若要了解如何取得失敗構件的記錄，請參閱[如何診斷 DevTest Labs 中的構件失敗](devtest-lab-troubleshoot-artifact-failure.md)。

### <a name="when-should-an-organization-use-a-public-artifact-repository-vs-private-artifact-repository-in-devtest-labs"></a>組織在 DevTest Labs 中何時應使用公用成品存放庫，而何時應使用私人成品存放庫？
[公用成品存放庫](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) \(英文\) 提供一組最常用的初始軟體套件。 它有助於快速部署，而不必投入時間重現常見的開發人員工具和增益集。您可以選擇部署自己的私人存放庫。 您可以平行方式各使用一個公用和私人存放庫。 您也可以選擇停用公用存放庫。 部署私人存放庫的準則應透過下列問題和考量來衍生：

- 組織是否需要使公司授權的軟體成為其 DevTest Labs 供應項目的一部分？ 如果答案是肯定的，則應該建立私人存放庫。
- 組織是否要開發自訂軟體來提供特定作業，且需將其當作整體佈建程序的一部分？ 如果答案是肯定的，則應該部署私人存放庫。
- 如果組織的治理原則需要隔離，而且外部存放庫不是由組織進行直接設定管理，則應部署私人成品儲存機制。 在此程序的過程中，可複製公用存放庫的初始複本，並與私人存放庫整合在一起。 接著，可以停用公用存放庫，如此一來，組織內的所有人都不能再存取該存放庫。 此方法強制組織內的所有使用者只能具有組織所核准的單一存放庫，並將設定漂移降到最低。


### <a name="should-an-organization-plan-for-a-single-repository-or-allow-multiple-repositories"></a>組織應該規劃單一存放庫，還是允許多個存放庫？
作為貴組織整體治理和組態管理策略的一部分，我們建議您使用集中式存放庫。 當您使用多個存放庫時，它們可能會在經過一段時間之後成為非受控軟體的定址接收器。 使用中央存放庫，多個小組就能針對他們的專案，從此存放庫中取用成品。 它會強制執行標準化、安全性、簡化管理，並排除重複的工作。 在集中化過程中，下列動作為適用於長期管理和持續性的建議做法：

- 將 Azure Repos 與 Azure 訂用帳戶用來進行驗證和授權的相同 Azure Active Directory 租用戶產生關聯。
- 在集中管理的`All DevTest Labs Developers` Azure Active Directory 中，建立名為的群組。 任何有助於成品開發的開發人員都應該放置於此群組中。
- 相同的 Azure Active Directory 群組可用來提供存取 Azure Repos 存放庫及實驗室。
- 在 Azure Repos 中，應該使用分支或派生，將開發中的存放庫與主要生產環境存放庫分隔開來。 只會在適當的程式碼檢閱之後，使用 Pull 要求來將內容新增至主要分支。 當程式碼檢閱者核准變更之後，負責維護主要分支的首席開發人員就會合併更新的程式碼。

## <a name="cicd-integration"></a>CI/CD 整合

### <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>DevTest Labs 是否會與我的 CI/CD 工具鏈整合？
如果您使用 Azure DevOps，您可以使用[DevTest labs 工作延伸](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)模組，將 DevTest Labs 中的發行管線自動化。 您可以使用此擴充功能執行的一些工作包括：

- 自動建立並部署 VM。 您也可以使用 Azure 檔案複製或 PowerShell Azure DevOps Services 工作來以最新的組建設定虛擬機器。
- 自動在測試之後擷取 VM 的狀態，以利在相同 VM 重現錯誤以便進行進一步的調查。
- 當不再需要時，請在發行管線結束時刪除 VM。

下列部落格文章提供關於使用 Azure DevOps Services 擴充功能的指引和資訊︰

- [DevTest Labs 和 Azure DevOps 擴充功能](integrate-environments-devops-pipeline.md)
- [透過 Azure DevOps Services 在現有 DevTest Labs 實驗室中部署新的 VM](https://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
- [使用 Azure DevOps Services 發行管理來持續部署至 DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

如需其他的持續整合 (CI)/持續傳遞 (CD) 工具鏈，可藉由使用 [Azure PowerShell Cmdlet](../azure-resource-manager/templates/deploy-powershell.md) 和 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/) 部署 [Azure Resource Manager 範本](https://azure.microsoft.com/resources/templates/) 來實現相同的案例。 您也可以使用[適用於 DevTest Labs 的 REST API](https://aka.ms/dtlrestapis) 來與您的工具鏈整合。

## <a name="networking"></a>網路功能

### <a name="when-should-i-create-a-new-virtual-network-for-my-devtest-labs-environment-vs-using-an-existing-virtual-network"></a>何時應該針對 DevTest Labs 環境建立新的虛擬網路，而何時該使用現有的虛擬網路？
如果您的 Vm 需要與現有的基礎結構互動，請考慮在 DevTest Labs 環境內使用現有的虛擬網路。 如果您使用 ExpressRoute，您可能會想要將 Vnet/子網的數量降到最低，這樣您就不會分割指派給訂用帳戶中使用的 IP 位址空間。

請考慮在這裡使用 VNet 對等互連模式（[中樞輪輻模型](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)）。 這種方法可讓訂用帳戶之間進行 vnet/子網通訊。 否則，每個 DevTest Labs 環境都可能有它自己的虛擬網路。

每個訂用帳戶的虛擬網路數目有[限制](../azure-resource-manager/management/azure-subscription-service-limits.md)。 雖然可將此限制提升到 100，但預設數目是 50。

### <a name="when-should-i-use-a-shared-ip-vs-public-ip-vs-private-ip"></a>何時應使用共用 IP、公用 IP 及私人 IP？

如果您使用站對站 VPN 或 Express Route，請考慮使用私人 IP，如此一來，您的機器就可透過透過內部網路存取，但無法透過公用網際網路存取。

> [!NOTE]
> 實驗室擁有者可以變更這個子網路原則，以確保沒有人會不小心為其 VM 建立公用 IP 位址。 訂用帳戶擁有者應該建立訂用帳戶原則，以防止建立公用 IP。

使用共用的公用 IP 時，實驗室中的虛擬機器會共用公用 IP 位址。 當您需要避免違反指定訂用帳戶的公用 IP 位址限制時，這個方法相當有幫助。

### <a name="how-do-i-ensure-that-development-and-test-virtual-machines-are-unable-to-reach-the-public-internet-are-there-any-recommended-patterns-to-set-up-network-configuration"></a>如何確保開發和測試虛擬機器無法連線到公用網際網路？ 是否有任何可用來設定網路設定的建議模式？

是。 有兩個需要考慮的層面：輸入和輸出流量。

- **輸入流量**–如果虛擬機器沒有公用 IP 位址，則網際網路無法連線。 常見的方法是確保已設定訂用帳戶層級原則，讓任何使用者都無法建立公用 IP 位址。
- **輸出流量**–如果您想要防止虛擬機器直接存取公用網際網路，並強制流量通過公司防火牆，您可以使用強制路由，透過 express ROUTE 或 VPN 將流量路由傳送到內部部署。

> [!NOTE]
> 如果您的 proxy 伺服器會封鎖沒有 proxy 設定的流量，請不要忘記將例外狀況新增至實驗室的成品儲存體帳戶。

您也可以針對虛擬機器或子網路使用網路安全性群組。 這個步驟會新增額外的保護層來允許 / 封鎖流量。

## <a name="troubleshooting"></a>疑難排解

### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>為何我現有的虛擬網路未能正確儲存？
其中一個可能原因是您的虛擬網路名稱包含句點。 若是如此，請嘗試移除句點，或將句點取代為連字號。 然後，再試著儲存虛擬網路一次。

### <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>為何我從 PowerShell 佈建 VM 時遇到「找不到父資源」錯誤？
當某資源為另一個資源的父資源時，父資源必須在建立子資源之前就存在。 如果父資源不存在，您會看到**ParentResourceNotFound**訊息。 如果您未在父資源上指定相依性，子資源可能會在父資源之前進行部署。

VM 是資源群組中實驗室下的子資源。 當您使用 Resource Manager 範本透過 PowerShell 部署 VM 時，在 PowerShell 指令碼中提供的資源群組名稱應該是實驗室的資源群組名稱。 如需詳細資訊，請參閱[對常見的 Azure 部署錯誤進行疑難排解](../azure-resource-manager/templates/common-deployment-errors.md)。

### <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>當 VM 部署失敗時，我可以在哪裡找到更多錯誤資訊？
VM 部署錯誤會擷取至活動記錄中。 您可以在實驗室 VM 頁面的 [資源] 功能表上，于 [ **Audit logs** ] 或 [**虛擬機器診斷**] 底下找到實驗室 vm 活動記錄（在您從 [我的虛擬機器] 清單中選取 VM 之後，該頁面就會出現）。

系統有時候會在 VM 部署開始之前就發生部署錯誤。 舉例來說，如果您超過隨 VM 一起建立之資源的訂用帳戶限制時。 在此情況下，系統會將錯誤詳細資料擷取到實驗室層級的活動記錄中。 活動記錄位於 [組態和原則]**** 設定底部。 如需在 Azure 中使用活動記錄的詳細資訊，請參閱[檢視活動記錄以稽核對資源的動作](../azure-resource-manager/management/view-activity-logs.md)。

### <a name="why-do-i-get-location-is-not-available-for-resource-type-error-when-trying-to-create-a-lab"></a>當嘗試建立實驗室時，為什麼會收到「無法提供資源類型的位置」錯誤？
當您嘗試建立實驗室時，您可能會看到類似下列的錯誤訊息：

```
The provided location 'australiacentral' is not available for resource type 'Microsoft.KeyVault/vaults'. List of available regions for the resource type is 'northcentralus,eastus,northeurope,westeurope,eastasia,southeastasia,eastus2,centralus,southcentralus,westus,japaneast,japanwest,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia,canadacentral,canadaeast,uksouth,ukwest,westcentralus,westus2,koreacentral,koreasouth,francecentral,southafricanorth
```

您可以採取下列其中一個步驟來解決這個錯誤：

#### <a name="option-1"></a>選項 1
在 [[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/)] 頁面上，檢查 Azure 區域中資源類型的可用性。 如果資源類型在特定區域中無法使用，DevTest Labs 就不支援在該區域中建立實驗室。 建立實驗室時，請選取另一個區域。

#### <a name="option-2"></a>選項 2
如果您的區域中有可用的資源類型，請檢查它是否已向您的訂用帳戶註冊。 它可以在訂用帳戶擁有者層級完成，如[這篇文章](../azure-resource-manager/management/resource-providers-and-types.md)所示。
