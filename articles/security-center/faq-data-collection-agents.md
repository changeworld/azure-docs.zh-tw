---
title: Azure 安全中心常見問題 - 資料收集和代理
description: 有關 Azure 安全中心的數據收集、代理和工作區的常見問題,該產品可説明您預防、檢測和回應威脅
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 0dbad1a94479430426dae47df7ca3a3ecd9dc980
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436189"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>常見問題解答 - 關於資料收集、代理和工作區的問題

安全中心從 Azure 虛擬機器 (VM)、虛擬機器規模集、IaaS 容器和非 Azure 電腦(包括本地電腦)收集數據,以監視安全漏洞和威脅。 數據使用 Log Analytics 代理進行收集,該代理從電腦讀取各種與安全相關的配置和事件日誌,並將數據複製到工作區進行分析。


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>我向安全中心創建的工作區上的 Azure 監視器日誌收費嗎?

否。 安全中心創建的工作區(雖然為每個節點計費為 Azure 監視器日誌配置)不產生 Azure 監視器日誌費用。 資訊安全中心的計費一律根據您的資訊安全中心的安全性原則，以及工作區安裝的解決方案：

- **免費層** – 資訊安全中心在預設工作區啟用 'SecurityCenterFree' 解決方案。 您不會為免費套餐收費。

- **標準層** – 資訊安全中心在預設工作區啟用 'Security' 解決方案。

如需詳細資訊，請參閱[資訊安全中心價格](https://azure.microsoft.com/pricing/details/security-center/)。

> [!NOTE]
> 安全中心創建的工作區的日誌分析定價層不會影響安全中心計費。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-log-analytics-agent-installation"></a>哪些 VM 適合自動預配日誌分析代理安裝?

符合下列條件的 Windows 或 Linux IaaS 虛擬機器：

- 日誌分析代理擴展當前未安裝在 VM 上。
- 虛擬機器處於執行狀態。
- 安裝 Windows 或 Linux [Azure 虛擬機器代理](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)程式 。
- 虛擬機器沒有作為 Web 應用程式防火牆或新一代防火牆等設備使用。


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>我可以刪除資訊安全中心所建立的預設工作區嗎？

**建議您不要刪除預設工作區。** 資訊安全中心會使用預設工作區儲存您虛擬機器傳來的安全性資料。 如果您刪除了工作區，資訊安全中心無法收集此資料，特定安全性建議及警示就無法使用。

要復原,請刪除連接到已刪除工作區的 VM 上的日誌分析代理。 資訊安全中心會重新安裝代理程式，並建立新的預設工作區。

## <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>如何使用我現有的 Log Analytics 工作區？

您可以選取現有的 Log Analytics 工作區來儲存資訊安全中心收集的資料。 若要使用現有的 Log Analytics 工作區：

- 工作區必須與您選取的 Azure 訂用帳戶相關聯。
- 至少必須擁有讀取權限以存取工作區。

若要選取現有的 Log Analytics 工作區：

1. 在 [安全性原則 - 資料收集]**** 下，選取 [使用其他工作區]****。

    ![使用其他工作區][4]

1. 從下拉式功能表中，選取要儲存收集資料的工作區。

    > [!NOTE]
    > 在下拉式功能表中，只會顯示您可存取，而且在您 Azure 訂用帳戶中的工作區。

1. 選取 [儲存]  。 系統將詢問您是否要重新配置受監視的 VM。

    - 如果您希望新的工作區設定**僅套用在新的虛擬機器**，請選取 [否]****。 新的工作區設置僅適用於新的代理安裝;新發現的 VM 未安裝日誌分析代理。
    - 如果要在所有**VM 上應用**新的工作區設置,請選擇"**是**"。 此外，每個連線到資訊安全中心建立之工作區的虛擬機器會重新連線到新的目標工作區。

    > [!NOTE]
    > 如果選擇 **「是**」,則在所有 VM 都重新連接到新目標工作區之前,不要刪除安全中心創建的任何工作區。 如果過早刪除工作區，這項作業將會失敗。

    - 要取消操作,請選擇 **"取消**"。

## <a name="what-if-the-log-analytics-agent-was-already-installed-as-an-extension-on-the-vm"></a>如果日誌分析代理已作為擴展安裝在 VM 上,該怎麼辦?<a name="mmaextensioninstalled"></a>

當監視代理作為擴展安裝時,擴展配置只允許報告到單個工作區。 資訊安全中心不會覆寫既存的使用者工作區連線。 安全中心會將來自 VM 的安全數據存儲在已連接的工作區中,前提是已安裝「安全」或「安全中心免費」解決方案。 在此過程中,安全中心可能會將擴展版本升級到最新版本。

有關詳細資訊,請參閱[預先存在的代理安裝情況下的自動預配](security-center-enable-data-collection.md#preexisting)。



## <a name="what-if-a-log-analytics-agent-is-directly-installed-on-the-machine-but-not-as-an-extension-direct-agent"></a>如果日誌分析代理直接安裝在計算機上,而不是作為擴展(直接代理)安裝,該怎麼辦?<a name="directagentinstalled"></a>

如果日誌分析代理直接安裝在 VM 上(不作為 Azure 擴展),安全中心將安裝日誌分析代理擴展,並可能將日誌分析代理升級到最新版本。

已安裝的代理將繼續報告其已配置的工作區,此外,還會向安全中心配置的工作區報告(Windows 電腦上支援多宿主)。

如果配置的工作區是使用者工作區(不是安全中心的預設工作區),則需要在它上安裝"安全/安全中心"解決方案,以便安全中心從 VM 和報告到該工作區的電腦開始處理事件。

對於 Linux 計算機,尚未支援代理多宿主 - 因此,如果檢測到現有代理安裝,則不會發生自動預配,並且不會更改電腦的配置。

對於在 2019 年 3 月 17 日之前連接到安全中心訂閱的現有電腦,當檢測到現有代理時,將不會安裝日誌分析代理擴展,也不會影響電腦。 有關這些計算機,請參閱"解決計算機上監視代理運行狀況問題"的建議,以解決這些機器上的代理安裝問題

有關詳細資訊,請參閱下[一節如果我的 VM 上已安裝系統中心操作管理器或 OMS 直接代理會發生什麼情況?](#scomomsinstalled)

## <a name="what-if-a-system-center-operations-manager-agent-is-already-installed-on-my-vm"></a>如果我的 VM 上已安裝系統中心操作管理器代理,該怎麼辦?<a name="scomomsinstalled"></a>

安全中心將並行安裝日誌分析代理擴展到現有的系統中心操作管理器代理。 現有代理將繼續正常向系統中心操作管理器伺服器報告。 請注意,操作管理器代理和日誌分析代理共用公共運行時庫,這些庫將在此過程中更新為最新版本。 注意 - 如果安裝了操作管理器代理的版本 2012,請不要打開自動預配(當操作管理器伺服器也是版本 2012 時,可管理性功能可能會丟失)。


## <a name="what-is-the-impact-of-removing-these-extensions"></a>移除這些擴充功能會有什麼影響？

如果您移除了 Microsoft Monitoring 擴充功能，資訊安全中心會無法收集虛擬機器送出的安全性資料，且特定安全性建議及警示將無法使用。 24 小時內，資訊安全中心會判定虛擬機器缺少了擴充功能，並重新安裝。


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>我要如何避免自動安裝代理程式和建立工作區？

您可以在安全性原則中關閉訂用帳戶的自動佈建，但不建議您這麼做。 關閉自動佈建會限制資訊安全中心的建議和警示。 若要停用自動佈建：

1. 如果您的訂用帳戶設定為標準層，請開啟該訂用帳戶的安全性原則，並選取**免費**層。

   ![定價層][1]

1. 接下來,通過在 **「安全策略和數據收集**」頁上選擇 **「關閉**」來關閉自動預配。
   ![資料收集][2]


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>我應該要退出自動安裝代理程式和建立工作區嗎？

> [!NOTE]
> 如果您選擇退出自動佈建，請務必檢閱[退出的影響為何？](#what-are-the-implications-of-opting-out-of-automatic-provisioning)和[退出時的建議步驟](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning)區段。

如果您符合下列條件，您可以選擇退出自動佈建：

- 資訊安全中心的自動代理程式安裝適用於整個訂用帳戶。 您無法將自動安裝套用到虛擬機器的子集。 如果日誌分析代理無法安裝關鍵 VM,則應退出宣告自動預配。
- 日誌分析代理擴展的安裝將更新代理的版本。 這適用於直接代理和系統中心操作管理器代理(在後者中,操作管理器和日誌分析代理共用公共運行時庫 - 將在此過程中更新)。 如果已安裝的操作管理器代理版本為 2012 版本並升級,則當操作管理器伺服器也是版本 2012 時,可管理性功能可能會丟失。 如果已安裝的操作管理器代理是版本 2012,請考慮選擇退出自動預配。
- 如果訂閱外部有自定義工作區(集中式工作區),則應選擇退出自動預配。 您可以手動安裝日誌分析代理擴展,並在沒有安全中心覆蓋連接的情況下將其連接到工作區。
- 如果您不想在每個訂用帳戶上建立多個工作區，而且訂用帳戶中有您自己的自訂工作區，則您有兩個選項：

   1. 您可以退出自動佈建。 移轉之後，依循[我可以如何使用現有 Log Analytics 工作區？](#how-can-i-use-my-existing-log-analytics-workspace)中的說明來設定預設工作區的設定

   1. 或者,您可以允許完成遷移、在 VM 上安裝日誌分析代理以及連接到創建的工作區的 VM。 接著，藉由選擇重新設定已安裝的代理程式，在設定預設工作區的設定上，選取您的自訂工作區。 如需詳細資訊，請參閱[如何使用我現有的 Log Analytics 工作區？](#how-can-i-use-my-existing-log-analytics-workspace)


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>退出自動佈建有什麼影響？

遷移完成後,安全中心無法從 VM 收集安全數據,並且某些安全建議和警報不可用。 如果退出退出,請手動安裝日誌分析代理。 請參閱[退出時的建議步驟](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning)。


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>退出自動佈建時的建議步驟是什麼？

手動安裝日誌分析代理擴展,以便安全中心可以從 VM 收集安全數據並提供建議和警報。 關於安裝的指引，請參閱 [Windows 虛擬機器的代理程式安裝](../virtual-machines/extensions/oms-windows.md)或 [Linux 虛擬機器的代理程式安裝](../virtual-machines/extensions/oms-linux.md)。

您可以將代理程式連線到任何現有的自訂工作區，或資訊安全中心建立的工作區。 如果自定義工作區未啟用"安全"或"安全中心免費"解決方案,則需要應用解決方案。 要應用,請選擇自定義工作區或訂閱,並通過 **「安全策略和定價層**」頁面應用定價層。

   ![定價層][1]

資訊安全中心會根據選取的定價層，在工作區上啟用正確的解決方案。


## <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>我要如何移除資訊安全中心安裝的 OMS 擴充功能？<a name="remove-oms"></a>

您可以手動刪除日誌分析代理。 關閉資料收集會限制資訊安全中心的建議和警示，所以不建議您這麼做。

> [!NOTE]
> 如果已啟用資料收集，資訊安全中心將會在您移除代理程式後重新安裝程式。  您必須停用資料收集，再手動移除代理程式。 若要了解如何停用資料收集，請參閱「我要如何避免自動安裝代理程式和建立工作區？」。

若要手動移除代理程式：

1.    在入口網站中，開啟**Log Analytics**。

1.    在紀錄分析頁面上,選擇工作區:

1.    選擇您不想監視的 VM,然後選擇 **「斷開連接**」 。

   ![移除代理程式][3]

> [!NOTE]
> 如果 Linux VM 已具有非擴展 OMS 代理,則刪除擴展程式也會刪除代理,您必須重新安裝它。


## <a name="how-do-i-disable-data-collection"></a>我要如何停用資料收集？

預設會關閉自動佈建。 您可以在安全性原則中關閉這項設定，隨時停用資源的自動佈建。 強烈建議進行自動預配,以便獲取有關系統更新、操作系統漏洞和終結點保護的安全警報和建議。

若要停用資料收集，請[登入 Azure 入口網站](https://portal.azure.com)，選取 [瀏覽]****，選取 [資訊安全中心]****，然後選取 [選取原則]****。 選取您想要停用自動佈建的訂用帳戶。 當您選取訂用帳戶時，[安全性原則 - 資料收集]**** 隨即開啟。 在 [自動佈建]**** 之下，選取 [關閉]****。


## <a name="how-do-i-enable-data-collection"></a>我要如何啟用資料收集？

您可以在安全性原則中為您的 Azure 訂用帳戶啟用資料收集。 啟用資料收集。 [登入 Azure 入口網站](https://portal.azure.com)，選取 [瀏覽]****，選取 [資訊安全中心]****，然後選取 [安全性原則]****。 選取您想要啟用自動佈建的訂用帳戶。 當您選取訂用帳戶時，[安全性原則 - 資料收集]**** 隨即開啟。 在 [自動佈建]**** 之下，選取 [開啟]****。


## <a name="what-happens-when-data-collection-is-enabled"></a>啟用資料收集時會發生什麼情況？

啟用自動預配後,安全中心會在所有支援的 Azure VM 和創建的任何新 VM 上預配日誌分析代理。 建議自動預配,但也提供手動代理安裝。 [瞭解如何安裝紀錄分析代理延伸](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)。 

代理程式支援處理序建立事件 4688，及事件 4688 中的 *CommandLine* 欄位。 在 VM 上創建的新進程由 EventLog 記錄,並由安全中心的檢測服務進行監視。 有關每個新流程記錄的詳細資訊,請參閱[4688 中的描述欄位](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields)。 代理程式也會收集在 VM 上建立的 4688 事件並將它們儲存在搜尋中。

代理程式也會啟用[自適性應用程式控制](security-center-adaptive-application.md)的資料收集，資訊安全中心會在稽核模式中設定本機 AppLocker 原則以允許所有應用程式。 此策略將導致 AppLocker 生成事件,然後由安全中心收集和利用這些事件。 請務必注意，在已經設定 AppLocker 原則的機器上不會設定此原則。 

如果客戶提供[安全性連絡資訊](security-center-provide-security-contact-details.md)，當資訊安全中心在 VM 上偵測到可疑的活動時，客戶就會收到通知電子郵件。 警報在安全中心的安全警報儀錶板中也可見。


## <a name="will-security-center-work-using-an-oms-gateway"></a>安全中心是否會使用 OMS 閘道工作?

是。 Azure 安全中心利用 Azure 監視器使用日誌分析代理從 Azure VM 和伺服器收集數據。
要收集數據,每個 VM 和伺服器都必須使用 HTTPS 連接到 Internet。 連接可以是直接的,使用代理,或透過[OMS 閘道](../azure-monitor/platform/gateway.md)。


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Monitoring Agent 是否會影響伺服器的效能？

代理程式只耗用少量的系統資源，對效能的影響應該很小。 如需有關效能影響和代理程式與擴充的詳細資訊，請參閱[規劃和操作指南](security-center-planning-and-operations-guide.md#data-collection-and-storage)。


## <a name="where-is-my-data-stored"></a>我的資料會儲存在何處？

從這個代理程式收集的資料會儲存在與您的訂用帳戶相關聯的現有 Log Analytics 工作區或新的工作區中。 如需詳細資訊，請參閱[資料安全性](security-center-data-security.md)。


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png
