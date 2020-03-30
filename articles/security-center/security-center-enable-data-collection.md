---
title: Azure 資訊安全中心的資料收集 | Microsoft Docs
description: 本文介紹如何安裝日誌分析代理並設置日誌分析工作區，用於存儲收集的資料。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 5236fd89ed99ca14bb7fc24e40ef79e12e5177d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245495"
---
# <a name="data-collection-in-azure-security-center"></a>Azure 資訊安全中心的資料收集
安全中心從 Azure 虛擬機器 （VM）、虛擬機器規模集、IaaS 容器和非 Azure（包括本地）電腦收集資料，以監視安全性漏洞和威脅。 使用日誌分析代理收集資料，該代理從電腦讀取各種與安全相關的配置和事件日誌，並將資料複製到工作區進行分析。 這類資料的範例包括︰作業系統類型和版本、作業系統記錄 (Windows 事件記錄)、執行中程序、電腦名稱、IP 位址和已登入的使用者。 日誌分析代理還會將崩潰轉儲檔案複製到工作區。

需要收集資料來提供對缺少的更新、配置錯誤的作業系統安全設置、端點保護狀態以及運行狀況和威脅防護的可見度。 

本文介紹如何安裝日誌分析代理並設置日誌分析工作區，用於存儲收集的資料。 需要執行這兩項作業，才能啟用資料收集。 

> [!NOTE]
> - 資料收集僅用於計算資源（VM、虛擬機器規模集、IaaS 容器和非 Azure 電腦）。 即使您未佈建代理程式，也可受惠於 Azure 資訊安全中心；不過，您的安全性有限，而且不支援以上所列的功能。  
> - 如需支援的平台清單，請參閱 [Azure 資訊安全中心支援的平台](security-center-os-coverage.md)。
> - 在日誌分析中存儲資料（無論是使用新的工作區還是現有工作區）都可能導致資料存儲產生額外費用。 有關詳細資訊，請參閱[定價頁](https://azure.microsoft.com/pricing/details/security-center/)。

## <a name="enable-automatic-provisioning-of-the-log-analytics-agent"></a>啟用日誌分析代理的自動預配<a name="auto-provision-mma"></a>

要從電腦收集資料，應安裝日誌分析代理。 代理安裝可以自動完成（建議），也可以手動安裝代理。  

>[!NOTE]
> 預設會關閉自動佈建。 若要將資訊安全中心設定為預設安裝自動佈建，請將它設定為 [開啟]****。
>

啟用自動預配時，安全中心會在所有受支援的 Azure VM 和創建的任何新 VM 上預配日誌分析代理。 強烈建議使用自動佈建，但是手動代理程式安裝也可行。 [瞭解如何安裝日誌分析代理擴展](#manual-agent)。



要啟用日誌分析代理的自動預配：
1. 在 [資訊安全中心] 主功能表下，選取 [定價和設定]****。
2. 按一下適用的訂閱

   ![選取訂閱][7]

3. 選擇**資料收集**。
4. 在 [自動佈建]**** 下，選取 [開啟]**** 啟用自動佈建。
5. 選取 [儲存]****。

   ![啟用自動佈建][1]

>[!NOTE]
> - 如需佈建預先存在的安裝的相關指示，請參閱[在預先存在的代理程式安裝情況下自動佈建](#preexisting)。
> - 有關手動預配的說明，請參閱[手動安裝日誌分析代理擴展](#manual-agent)。
> - 如需有關關閉自動佈建的指示，請參閱[關閉自動佈建](#offprovisioning)。
> - 如需有關如何使用 PowerShell 上架資訊安全中心的說明，請參閱[使用 PowerShell 自動化上架 Azure 資訊安全中心](security-center-powershell-onboarding.md)。
>

## <a name="workspace-configuration"></a>工作區設定
資訊安全中心收集的資料會儲存在 Log Analytics 工作區。 您可以選擇將從 Azure VM 收集而得的資料儲存於資訊安全中心建立的工作區中，或儲存於您建立的現有工作區中。 

工作區是依據訂用帳戶進行設定，而且許多訂用帳戶可能會使用相同的工作區。

### <a name="using-a-workspace-created-by-security-center"></a>使用資訊安全中心所建立的工作區

資訊安全中心可以自動建立用來儲存資料的預設工作區。 

若要選取資訊安全中心所建立的工作區：

1. 在 [預設工作區組態]**** 下，選取 [使用資訊安全中心建立的工作區]。
   ![選取定價層][10] 

1. 按一下 [儲存]****。<br>
    資訊安全中心會在此地理位置建立新的資源群組和預設工作區，並將代理程式連線到該工作區。 工作區和資源群組的命名慣例如下：<br>
   **工作區：DefaultWorkspace-[subscription-ID]-[geo]<br> 資源群組：DefaultResourceGroup-[geo]**

   如果訂用帳戶中包含多個地理位置上的虛擬機器，則資訊安全中心會建立多個工作區。 建立多個工作區是為了遵守資料隱私權規則。
1. 資訊安全中心會依據為訂用帳戶設定的定價層，在工作區上自動啟用資訊安全中心解決方案。 

> [!NOTE]
> 資訊安全中心所建立工作區的 Log Analytics 定價層不會影響資訊安全中心的收費。 資訊安全中心的計費一律根據您的資訊安全中心的安全性原則，以及工作區安裝的解決方案。 針對免費層，資訊安全中心在預設工作區啟用 SecurityCenterFree** 解決方案。 對於標準層，資訊安全中心在預設工作區啟用 Security** 解決方案。
> 在日誌分析中存儲資料可能會為數據存儲產生額外費用。 有關詳細資訊，請參閱[定價頁](https://azure.microsoft.com/pricing/details/security-center/)。

有關現有日誌分析帳戶的詳細資訊，請參閱[現有日誌分析客戶](./faq-azure-monitor-logs.md)。

### <a name="using-an-existing-workspace"></a>使用現有工作區

如果您已有現有的日誌分析工作區，則可能需要使用相同的工作區。

若要使用現有的 Log Analytics 工作區，您必須具備工作區的讀取和寫入權限。

> [!NOTE]
> 在現有工作區上啟用的解決方案將套用至所連線的 Azure VM。 若為付費解決方案，這可能導致額外的費用。 對於資料隱私權考量，請確定您選取的工作區位於正確的地理區域中。
> 將資料存儲在日誌分析中可能會為數據存儲產生額外費用。 有關詳細資訊，請參閱[定價頁](https://azure.microsoft.com/pricing/details/security-center/)。

若要選取現有的 Log Analytics 工作區：

1. 在 [預設工作區組態]**** 下，選取 [使用其他工作區]****。

   ![選取現有工作區][2]

2. 從下拉式功能表中，選取要儲存收集資料的工作區。

   > [!NOTE]
   > 在下拉式功能表中，所有訂用帳戶的所有工作區均可供使用。 如需詳細資訊，請參閱[跨訂用帳戶工作區選取](security-center-enable-data-collection.md#cross-subscription-workspace-selection)。 您必須具有此工作區的存取權。
   >
   >

3. 選取 [儲存]****。
4. 選取 [儲存]**** 之後，系統會詢問您是否要重新設定先前連線到預設工作區的受監控虛擬機器。

   - 如果希望新工作區設置僅應用於新 VM，請選擇 **"否**"。 新的工作區設置僅適用于新的代理安裝;新發現的 VM 未安裝日誌分析代理。
   - 如果您希望新的工作區設定套用在所有虛擬機器，請選取 [是]****。 此外，每個連線到資訊安全中心建立之工作區的虛擬機器會重新連線到新的目標工作區。

   > [!NOTE]
   > 如果您選取 [是]，您必須刪除資訊安全中心建立的工作區，直到所有虛擬機器已重新連線至新的目標工作區。 如果過早刪除工作區，這項作業將會失敗。
   >
   >

   - 選取 [取消]**** 以取消作業。

     ![選取現有工作區][3]

5. 選擇要設置日誌分析代理所需的工作區的定價層。 <br>若要使用現有的工作區，請設定工作區的定價層。 這會在工作區上安裝資訊安全中心解決方案 (如果不存在)。

    a.  在安全中心主功能表中，選擇**定價&設置**。
     
    b.  選取您要連線代理程式所需的工作區。
        ![選取工作區][7] c. 設定定價層。
        ![選取定價層][9]
   
   >[!NOTE]
   >如果工作區已經啟用 **Security** 或 **SecurityCenterFree** 解決方案，將會自動設定定價。 

## <a name="cross-subscription-workspace-selection"></a>跨訂用帳戶工作區選取
當您選取用來儲存資料的工作區時，所有訂用帳戶的所有工作區均可供使用。 跨訂用帳戶工作區選取可讓您從執行於不同訂用帳戶的虛擬機器收集資料，並將它儲存在您選擇的工作區中。 如果您在組織中使用集中式工作區，而且想要將它用於安全性資料收集，此選項會很有用。 如需有關如何管理工作區的詳細資訊，請參閱[管理工作區存取](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access)。


## <a name="data-collection-tier"></a>資料收集層
在 Azure 資訊安全中心中選取 [資料收集層]，只會影響 Log Analytics 工作區中安全性事件的儲存空間。 日誌分析代理仍將收集和分析 Azure 安全中心威脅保護所需的安全事件，而不管您選擇在日誌分析工作區中存儲哪一層安全事件（如果有）。 選擇在工作區中儲存安全性事件，將允許在工作區中調查、搜尋和稽核這些事件。 
> [!NOTE]
> 將資料存儲在日誌分析中可能會為數據存儲產生額外費用。 有關詳細資訊，請參閱[定價頁](https://azure.microsoft.com/pricing/details/security-center/)。
> 
> 對於訂用帳戶和工作區，您可以從儲存在您工作區的四組事件中選擇合適的篩選原則： 

- **無** – 停用安全性事件的儲存空間。 這是預設值。
- **最小** – 適合想要將事件數量最小化的客戶，是較小的一組事件。
- **一般** – 這組事件能滿足大部分客戶的需求，而能讓這些客戶使用完整的稽核記錄。
- **所有事件** – 適合想要確定已儲存所有事件的客戶。


> [!NOTE]
> 只能資訊安全中心的標準層會提供這些安全性事件集。 若要深入了解資訊安全中心的定價層，請參閱[價格](security-center-pricing.md)。
這些集合都是設計用來處理一般情況。 實作之前，請務必評估哪一個適合您的需求。
>
>

為了判定屬於**一般**和**最小**事件集合的事件，我們致力於與客戶合作並配合業界標準，以了解每個事件及其使用方式的未篩選頻率。 此流程中採取下列指導方針：

- **最小** - 確保這個集合所涵蓋的僅有可能代表發生成功入侵和重大事件的事件，數量很少。 例如，此集包含使用者成功和失敗的登錄（事件標識 4624， 4625），但它不包含對審核很重要但對檢測沒有意義且具有相對較高的卷的登出。 這個集合的大部分資料量是登入事件和流程建立事件 (事件識別碼 4688)。
- **一般** - 提供此集合中的完整使用者稽核記錄。 例如，此集包含使用者登錄和使用者登出（事件 ID 4634）。 納入了各種稽核動作，例如安全性群組變更、主要網域控制站 Kerberos 作業，以及業界組織建議的其他事件。

「一般」集合包含的事件相當少，因為選擇一般集合而非其他事件的主要動機在於降低數量，且不篩選出特定事件。

以下是對於每個集合的安全性和 App Locker 事件識別碼的完整分析：

| 資料層 | 收集的事件指標 |
| --- | --- |
| 最小 | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| 通用 | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - 如果您使用群組原則物件 (GPO)，則建議您啟用稽核原則程序建立事件 4688 和事件 4688 內的 [CommandLine]** 欄位。 如需更多程序建立事件 4688 的相關資訊，請參閱資訊安全中心的[常見問題集](faq-data-collection-agents.md#what-happens-when-data-collection-is-enabled)。 如需更多稽核原則的相關資訊，請參閱[稽核原則建議](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations)。
> -  若要啟用[自適性應用程式控制](security-center-adaptive-application.md)的資料收集，資訊安全中心會在稽核模式中設定本機 AppLocker 原則以允許所有的應用程式。 這會導致 AppLocker 產生事件，然後由資訊安全中心收集並利用。 請務必注意，在已經設定 AppLocker 原則的機器上不會設定此原則。 
> - 若要收集 Windows 篩選平台[事件識別碼 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156)，您必須啟用[稽核篩選平台連線](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (Auditpol /set /subcategory:"Filtering Platform Connection" /Success:Enable)
>

若要選擇篩選原則：
1. 在 **"資料收集"** 頁上，在 **"安全事件**"下選擇篩選策略。
2. 選取 [儲存]****。

   ![選擇篩選原則][5]

### <a name="automatic-provisioning-in-cases-of-a-pre-existing-agent-installation"></a>預先存在的代理安裝情況下自動預配<a name="preexisting"></a> 

下列使用案例會指定在已經安裝代理程式或擴充功能的情況下，自動佈建的運作方式。 

- 日誌分析代理安裝在電腦上，但不作為擴展（直接代理）<br>
如果日誌分析代理直接安裝在 VM 上（不作為 Azure 擴展），安全中心將安裝日誌分析代理擴展，並可能將日誌分析代理升級到最新版本。
已安裝的代理將繼續報告其已配置的工作區，此外還將向安全中心配置的工作區報告（Windows 電腦上支援多宿主）。
如果配置的工作區是使用者工作區（不是安全中心的預設工作區），則需要在它上安裝"安全/安全自由"解決方案，以便安全中心從 VM 和報告到該工作區的電腦開始處理事件。<br>
<br>
對於 Linux 電腦，尚未支援代理多宿主 - 因此，如果檢測到現有代理安裝，則不會發生自動預配，並且不會更改電腦的配置。
<br>
對於在 2019-03-17 之前已添加到安全中心訂閱的現有電腦，當檢測到現有代理時，將不會安裝日誌分析代理擴展，也不會影響電腦。 對於這些電腦，請參閱"解決電腦上監視代理運行狀況問題"的建議，以解決這些電腦上代理安裝問題。

  
- 系統中心操作管理器代理安裝在電腦上<br>
安全中心將並行安裝日誌分析代理擴展到現有操作管理器。 現有操作管理器代理將繼續正常向操作管理器伺服器報告。 請注意，操作管理器代理和日誌分析代理共用公共運行時庫，這些庫將在此過程中更新為最新版本。
注意 - 如果安裝了操作管理器代理版本 2012，**請勿**打開自動預配。<br>

- 預先存在的 VM 擴充功能已存在<br>
    - 當監視代理作為擴展安裝時，擴展配置只允許報告到單個工作區。 資訊安全中心不會覆寫既存的使用者工作區連線。 安全中心將在已連接的工作區中存儲來自 VM 的安全資料，前提是已安裝"安全"或"安全自由"解決方案。 在此過程中，安全中心可能會將擴展版本升級到最新版本。  
    - 若要查看現有擴充功能將資料傳送到哪個工作區，請執行測試以[驗證與 Azure 資訊安全中心的連線](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/)。 或者，您可以打開日誌分析工作區、選擇工作區、選擇 VM 以及查看日誌分析代理連接。 
    - 如果環境在用戶端工作站上安裝日誌分析代理並報告到現有的日誌分析工作區，請查看[Azure 安全中心支援的作業系統](security-center-os-coverage.md)清單，以確保支援作業系統。 有關詳細資訊，請參閱[現有日誌分析客戶](./faq-azure-monitor-logs.md)。
 
### <a name="turn-off-automatic-provisioning"></a>關閉自動佈建 <a name="offprovisioning"></a>
您可以在安全性原則中關閉從資源自動佈建，隨時關閉這項設定。 


1. 返回 [資訊安全中心] 主功能表，並選取 [安全性原則]。
2. 按一下要禁用自動預配的訂閱行中的 **"編輯設置**"。
3. 在 [安全性原則 - 資料收集]**** 刀鋒視窗的 [自動佈建]**** 下，選取 [關閉]****。
4. 選取 [儲存]****。

   ![停用自動佈建][6]

自動佈建停用 (關閉) 後，就不會顯示預設的工作區組態區段。

如果您關閉先前開啟的自動佈建：
-   代理程式就不會佈建在新的 VM 上。
-   資訊安全中心會停止從預設工作區收集資料。
 
> [!NOTE]
>  禁用自動預配不會從預配代理的 Azure VM 中刪除日誌分析代理。 如需移除 OMS 擴充功能的相關資訊，請參閱[我要如何移除資訊安全中心安裝的 OMS 擴充功能](faq-data-collection-agents.md#remove-oms)。
>
    
## <a name="manual-agent-provisioning"></a>手動佈建代理程式 <a name="manual-agent"></a>
 
有幾種方法可以手動安裝日誌分析代理。 手動安裝時，請務必停用自動佈建。

### <a name="operations-management-suite-vm-extension-deployment"></a>Operations Management Suite VM 擴充功能部署 

您可以手動安裝日誌分析代理，以便安全中心可以從 VM 收集安全資料並提供建議和警報。
1. 選取自動佈建 – 關閉。
2. 創建工作區並設置要設置日誌分析代理的工作區的定價層：

   a.  在 [資訊安全中心] 主功能表中，選取 [安全性原則]****。
     
   b.  選取您要在其中連線代理程式的工作區。 請確定工作區位於您在資訊安全中心使用的相同訂用帳戶中，而且您具備工作區的讀取/寫入權限。
       ![選取工作區][8]
3. 設定定價層。
   ![選取定價層][9] 
   >[!NOTE]
   >如果工作區已經啟用 **Security** 或 **SecurityCenterFree** 解決方案，將會自動設定定價。 
   > 

4. 如果您想要使用 Resource Manager 範本在新的 VM 上部署代理程式，請安裝 OMS 虛擬機器擴充功能：

   a.  [安裝適用於 Windows 的 OMS 虛擬機器擴充功能](../virtual-machines/extensions/oms-windows.md)
    
   b.  [安裝適用於 Linux 的 OMS 虛擬機器擴充功能](../virtual-machines/extensions/oms-linux.md)
5. 若要在現有的 VM 上部署擴充功能，請遵循[收集有關 Azure 虛擬機器的資料](../azure-monitor/learn/quick-collect-azurevm.md)中的指示。

   > [!NOTE]
   > **收集事件和效能資料**一節是選擇性的。
   >
6. 若要使用 PowerShell 來部署擴充功能，請使用下列 PowerShell 範例︰
   
   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
   
   1. 移至 **Log Analytics** ，然後按一下 [進階設定]****。
    
      ![設定記錄分析][11]

   2. 複製 [工作區識別碼]**** 和 [主索引鍵]**** 的值。
  
      ![複製值][12]

   3. 在公用組態和私用組態中填入下列值：
     
           $PublicConf = @{
               "workspaceId"= "<WorkspaceID value>"
           }
 
           $PrivateConf = @{
               "workspaceKey"= "<Primary key value>"
           }

      - 在 Windows VM 上安裝時：
        
            Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "MicrosoftMonitoringAgent" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "MicrosoftMonitoringAgent" -TypeHandlerVersion '1.0' -Location $vm.Location -settings $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True 
    
      - 在 Linux VM 上安裝時：
        
            Set-AzVMExtension -ResourceGroupName $vm1.ResourceGroupName -VMName $vm1.Name -Name "OmsAgentForLinux" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "OmsAgentForLinux" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True`

> [!NOTE]
> 如需有關如何使用 PowerShell 上架資訊安全中心的說明，請參閱[使用 PowerShell 自動化上架 Azure 資訊安全中心](security-center-powershell-onboarding.md)。

## <a name="troubleshooting"></a>疑難排解

-   若要找出自動佈建安裝問題，請參閱[監視代理程式健康情況問題](security-center-troubleshooting-guide.md#mon-agent)。

-  若要識別監視代理程式網路需求，請參閱[針對監視代理程式網路需求進行疑難排解](security-center-troubleshooting-guide.md#mon-network-req)。
-   要確定手動入職問題，請參閱[如何排除操作管理套件入職問題](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues)。

- 要識別未受監視的 VM 和電腦問題，請處理：

    如果機器未執行 Microsoft Monitoring Agent 擴充功能，VM 或電腦就不會由資訊安全中心監視。 電腦可能已安裝本地代理，例如 OMS 直接代理或系統中心操作管理器代理。 具有這些代理程式的機器會識別為未受監視，因為資訊安全中心無法完整支援這些代理程式。 若要能完整享有資訊安全中心的所有功能，則需要 Microsoft Monitoring Agent 擴充功能。

    有關安全中心無法成功監視 VM 和為自動預配而初始化的電腦的原因的詳細資訊，請參閱[監視代理運行狀況問題](security-center-troubleshooting-guide.md#mon-agent)。


## <a name="next-steps"></a>後續步驟
本文說明資料收集和自動佈建如何在資訊安全中心運作。 如要深入了解資訊安全中心，請參閱下列主題：

* [Azure 資訊安全中心常見問題集](faq-general.md)-- 尋找有關使用服務的常見問題。
* [Azure 資訊安全中心的安全性健康狀態監視](security-center-monitoring.md)--了解如何監視 Azure 資源的健康狀態。



<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png
[7]: ./media/security-center-enable-data-collection/select-subscription.png
[8]: ./media/security-center-enable-data-collection/manual-provision.png
[9]: ./media/security-center-enable-data-collection/pricing-tier.png
[10]: ./media/security-center-enable-data-collection/workspace-selection.png
[11]: ./media/security-center-enable-data-collection/log-analytics.png
[12]: ./media/security-center-enable-data-collection/log-analytics2.png
