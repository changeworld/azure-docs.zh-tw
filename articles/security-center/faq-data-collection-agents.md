---
title: Azure 資訊安全中心常見問題集 - 資料收集和代理程式
description: 關於 Azure 資訊安全中心的資料收集、代理程式和工作區的常見問題，這是一項可協助您預防、偵測及因應威脅的產品
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
ms.openlocfilehash: 0a368e54c940ff580b7e8f49dd108fafddde5b17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91441468"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>常見問題集 - 資料收集、代理程式和工作區的相關問題

資訊安全中心會從您的 Azure 虛擬機器 (VM)、虛擬機器擴展集、IaaS 容器和非 Azure 電腦 (包括內部部署機器) 收集資料，以監視是否有安全性弱點和威脅。 資料是使用 Log Analytics 代理程式收集而得，收集的方式是讀取機器的各種安全性相關組態和事件記錄，並將資料複製到工作區進行分析。


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>在資訊安全中心所建立的工作區上，我是否需支付 Azure 監視器記錄的費用？

否。 資訊安全中心建立的工作區雖然設定為以每個節點的 Azure 監視器記錄計費，但並不會產生 Azure 監視器記錄費用。 資訊安全中心的計費一律根據您的資訊安全中心的安全性原則，以及工作區安裝的解決方案：

- **Azure Defender 關閉** –資訊安全中心會在預設工作區啟用 ' SecurityCenterFree ' 解決方案。 如果 Azure Defender 處於關閉狀態，則不會向您收費。

- **Azure Defender on** –資訊安全中心會在預設工作區上啟用「安全性」解決方案。

如需詳細資訊，請參閱[資訊安全中心價格](https://azure.microsoft.com/pricing/details/security-center/)。

> [!NOTE]
> 資訊安全中心所建立工作區的 Log Analytics 定價層不會影響資訊安全中心的收費。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-is-the-log-analytics-agent"></a>什麼是 Log Analytics 代理程式？

若要監視安全性弱點和威脅，Azure 資訊安全中心取決於 [Log Analytics 代理](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent) 程式-這是 Azure 監視器服務所使用的相同代理程式。 

代理程式有時也稱為 Microsoft Monitoring Agent (或「MMA」 ) 。 

代理程式會從連線的機器收集各種安全性相關設定詳細資料和事件記錄檔，然後將資料複製到您的 Log Analytics 工作區，以供進一步分析。 這類資料的範例包括︰作業系統類型和版本、作業系統記錄 (Windows 事件記錄)、執行中程序、電腦名稱、IP 位址和已登入的使用者。

確定您的電腦正在執行代理程式支援的其中一個作業系統，如下列頁面所述：

* [Windows 版 Log Analytics 代理程式支援的作業系統](../azure-monitor/platform/agents-overview.md#supported-operating-systems)

* [Linux 版 Log Analytics 代理程式支援的作業系統](../azure-monitor/platform/agents-overview.md#supported-operating-systems)

深入瞭解 [Log Analytics 代理程式所收集的資料](security-center-enable-data-collection.md)。




## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-log-analytics-agent-installation"></a>要自動佈建 Log Analytics 代理程式安裝的 VM 需符合哪些麼條件？

符合下列條件的 Windows 或 Linux IaaS 虛擬機器：

- 虛擬機器目前尚未安裝 Log Analytics 代理程式延伸模組。
- 虛擬機器處於執行狀態。
- 已安裝 Windows 或 Linux [Azure 虛擬機器代理程式](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)。
- 虛擬機器沒有作為 Web 應用程式防火牆或新一代防火牆等設備使用。


## <a name="where-is-the-default-log-analytics-workspace-created"></a>預設的 Log Analytics 工作區會在何處建立？

預設工作區的位置取決於您的 Azure 區域：

- 位於美國和巴西的 VM，工作區位置為美國
- 位於加拿大的 VM，工作區位置為加拿大
- 位於歐洲的 VM，工作區位置為歐洲
- 位於英國的 VM，工作區位置為英國
- 位於東亞和東南亞的 VM，工作區位置為亞洲
- 位於韓國的 VM，工作區位置為韓國
- 位於印度的 VM，工作區位置為印度
- 位於日本的 VM，工作區位置為日本
- 位於中國的 VM，工作區位置為中國
- 位於澳大利亞的 VM，工作區位置為澳大利亞


## <a name="what-data-is-collected-by-the-log-analytics-agent"></a>Log Analytics 代理程式會收集哪些資料？

如需代理程式所監視之應用程式和服務的完整清單，請參閱 [Azure 監視器所監視的內容](https://docs.microsoft.com/azure/azure-monitor/monitor-reference#azure-services)。

> [!IMPORTANT]
> 請注意，對於某些服務（例如 Azure 防火牆），如果您已啟用記錄功能，並選擇多對話資源來記錄 (例如，將記錄檔設定為 *詳細* 資訊) 您可能會看到對 log Analytics 工作區儲存體需求的重大影響。 


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>我可以刪除資訊安全中心所建立的預設工作區嗎？

**建議您不要刪除預設工作區。** 資訊安全中心會使用預設工作區儲存您虛擬機器傳來的安全性資料。 如果您刪除了工作區，資訊安全中心無法收集此資料，特定安全性建議及警示就無法使用。

若要復原，請將連線至已刪除工作區之 VM 上的 Log Analytics 代理程式移除。 資訊安全中心會重新安裝代理程式，並建立新的預設工作區。

## <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>如何使用我現有的 Log Analytics 工作區？

您可以選取現有的 Log Analytics 工作區來儲存資訊安全中心收集的資料。 若要使用現有的 Log Analytics 工作區：

- 工作區必須與您選取的 Azure 訂用帳戶相關聯。
- 至少必須擁有讀取權限以存取工作區。

若要選取現有的 Log Analytics 工作區：

1. 在 [安全性原則 - 資料收集] 下，選取 [使用其他工作區]。

    ![使用其他工作區][4]

1. 從下拉式功能表中，選取要儲存收集資料的工作區。

    > [!NOTE]
    > 在下拉式功能表中，只會顯示您可存取，而且在您 Azure 訂用帳戶中的工作區。

1. 選取 [儲存]。 系統會詢問您是否要重新設定受監視的 VM。

    - 如果您希望新的工作區設定**僅套用在新的虛擬機器**，請選取 [否]。 新的工作區設定只會套用在新安裝的代理程式，以及新探索到的未安裝 Log Analytics 代理程式的 VM。
    - 如果您希望新的工作區設定**套用在所有虛擬機器**，請選取 [是]。 此外，每個連線到資訊安全中心建立之工作區的虛擬機器會重新連線到新的目標工作區。

    > [!NOTE]
    > 如果您選取 [是]，在所有 VM 均重新連線至新的目標工作區之前，請不要刪除資訊安全中心所建立的任何工作區。 如果過早刪除工作區，這項作業將會失敗。

    - 若要取消作業，請選取 [取消]。

## <a name="what-if-the-log-analytics-agent-was-already-installed-as-an-extension-on-the-vm"></a>如果已在 VM 上將 Log Analytics 代理程式安裝為延伸模組，將會如何？<a name="mmaextensioninstalled"></a>

當監視代理程式安裝為延伸模組時，延伸模組設定僅允許報告至單一工作區。 資訊安全中心不會覆寫既存的使用者工作區連線。 如果已連線的工作區中已安裝 "Security" 或 "SecurityCenterFree" 解決方案，資訊安全中心就會將 VM 中的安全性資料儲存到該工作區。 資訊安全中心可在此程序中將延伸模組版本升級至最新版本。

如需詳細資訊，請參閱[在預先存在的代理程式安裝情況下自動佈建](security-center-enable-data-collection.md#preexisting)。



## <a name="what-if-a-log-analytics-agent-is-directly-installed-on-the-machine-but-not-as-an-extension-direct-agent"></a>如果將 Log Analytics 代理程式直接安裝在機器上，而不是安裝為延伸模組 (直接代理程式)，將會如何？<a name="directagentinstalled"></a>

如果 Log Analytics 代理程式直接安裝在 VM 上 (而不是安裝為 Azure 延伸模組)，資訊安全中心將會安裝 Log Analytics 代理程式延伸模組，並且可將 Log Analytics 代理程式升級至最新版本。

安裝的代理程式會繼續向其已設定的工作區報告，此外也會向資訊安全中心所設定的工作區報告 (Windows 機器上支援多路連接)。

如果已設定的工作區是使用者工作區 (而不是資訊安全中心的預設工作區)，您就將必須在其上安裝 "Security/"SecurityCenterFree" 解決方案，資訊安全中心才能開始處理向該工作區報告的 VM 和電腦所產生的事件。

對於 Linux 機器尚不支援代理程式多路連接 - 因此，如果偵測到現有的代理程式安裝，將不會進行自動佈建，且不會改變機器的設定。

對於在 2019 年 3 月 17 日之前上線到資訊安全中心之訂用帳戶中的現有機器，在偵測到現有的代理程式時，將不會安裝 Log Analytics 代理程式延伸模組，且機器將不受影響。 針對這類機器，請參閱「請解決機器上的 Monitoring Agent 健康情況問題」的建議，以解決這些機器上的代理程式安裝問題

如需詳細資訊，請參閱下一節：[如果我的 VM 上已安裝 System Center Operations Manager 或 OMS 直接代理程式，將會如何？](#scomomsinstalled)

## <a name="what-if-a-system-center-operations-manager-agent-is-already-installed-on-my-vm"></a>如果我的 VM 上已安裝 System Center Operations Manager 代理程式，將會如何？<a name="scomomsinstalled"></a>

資訊安全性中心會將 Log Analytics 代理程式延伸模組與現有的 System Center Operations Manager 代理程式並存安裝。 現有的代理程式會繼續正常地向 System Center Operations Manager 伺服器報告。 請注意，Operations Manager 代理程式和 Log Analytics 代理程式會共用相同的執行階段程式庫，而該程式庫會在此程序中更新至最新版本。 注意 - 如果已安裝 Operations Manager 代理程式 2012 版，請勿開啟自動佈建 (如果 Operations Manager 伺服器也是2012 版，管理功能可能會遺失)。


## <a name="what-is-the-impact-of-removing-these-extensions"></a>移除這些擴充功能會有什麼影響？

如果您移除了 Microsoft Monitoring 擴充功能，資訊安全中心會無法收集虛擬機器送出的安全性資料，且特定安全性建議及警示將無法使用。 24 小時內，資訊安全中心會判定虛擬機器缺少了擴充功能，並重新安裝。


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>我要如何避免自動安裝代理程式和建立工作區？

您可以在安全性原則中關閉訂用帳戶的自動佈建，但不建議您這麼做。 關閉自動佈建會限制資訊安全中心的建議和警示。 若要停用自動佈建：

1. 如果您的訂用帳戶已啟用 Azure Defender，請開啟該訂用帳戶的安全性原則，並選取 [ **關閉 Azure defender**]。

    :::image type="content" source="./media/security-center-platform-migration-faq/pricing-tier.png" alt-text="啟用或停用 Azure Defender":::

1. 接下來，請選取 [安全性原則 – 資料收集] 頁面上的 [關閉]，來停用自動佈建。
   ![資料收集][2]


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>我應該要退出自動安裝代理程式和建立工作區嗎？

> [!NOTE]
> 如果您選擇退出自動佈建，請務必檢閱[退出的影響為何？](#what-are-the-implications-of-opting-out-of-automatic-provisioning)和[退出時的建議步驟](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning)區段。

如果您符合下列條件，您可以選擇退出自動佈建：

- 資訊安全中心的自動代理程式安裝適用於整個訂用帳戶。 您無法將自動安裝套用到虛擬機器的子集。 如果有重要 VM 無法安裝 Log Analytics 代理程式，您即應選擇退出自動佈建。
- Log Analytics 代理程式延伸模組的安裝程序會更新代理程式的版本。 這適用於直接代理程式和 System Center Operations Manager 代理程式 (在後者中，Operations Manager 和 Log Analytics 代理程式會共用相同的執行階段程式庫，而該程式庫會在此程序中更新)。 如果已安裝的 Operations Manager 代理程式是 2012 版，而且已升級，當 Operations Manager 伺服器也是 2012 版時，管理功能可能會遺失。 如果已安裝的 Operations Manager 代理程式是 2012 版，請考慮退出自動佈建。
- 如果您在訂用帳戶 (集中式工作區) 之外還有自訂工作區，則您應該退出自動佈建。 您可以手動安裝 Log Analytics 代理程式延伸模組，然後將其連線至您的工作區，而不是由資訊安全中心覆寫連線。
- 如果您不想在每個訂用帳戶上建立多個工作區，而且訂用帳戶中有您自己的自訂工作區，則您有兩個選項：

   1. 您可以退出自動佈建。 移轉之後，依循[我可以如何使用現有 Log Analytics 工作區？](#how-can-i-use-my-existing-log-analytics-workspace)中的說明來設定預設工作區的設定

   1. 或者，您可以讓移轉程序完成，讓 Log Analytics 代理程式安裝在 VM 上，然後讓 VM 連線至已建立的工作區。 接著，藉由選擇重新設定已安裝的代理程式，在設定預設工作區的設定上，選取您的自訂工作區。 如需詳細資訊，請參閱[如何使用我現有的 Log Analytics 工作區？](#how-can-i-use-my-existing-log-analytics-workspace)


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>退出自動佈建有什麼影響？

移轉完成後，資訊安全中心將無法收集 VM 送出的安全性資料，且特定安全性建議及警示將無法使用。 如果您選擇退出，請手動安裝 Log Analytics 代理程式。 請參閱[退出時的建議步驟](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning)。


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>退出自動佈建時的建議步驟是什麼？

請手動安裝 Log Analytics 代理程式延伸模組，讓資訊安全中心能夠收集您的 VM 所傳來的安全性資料，並提供建議和警示。 關於安裝的指引，請參閱 [Windows 虛擬機器的代理程式安裝](../virtual-machines/extensions/oms-windows.md)或 [Linux 虛擬機器的代理程式安裝](../virtual-machines/extensions/oms-linux.md)。

您可以將代理程式連線到任何現有的自訂工作區，或資訊安全中心建立的工作區。 如果自訂工作區並沒有啟用的 'Security' 或 'SecurityCenterFree' 解決方案，則您必須套用解決方案。 若要套用，請選取自訂工作區或訂用帳戶，並透過 [安全性原則 - 定價層] 頁面來套用定價層。

:::image type="content" source="./media/security-center-platform-migration-faq/pricing-tier.png" alt-text="啟用或停用 Azure Defender":::

資訊安全中心會根據選取的定價層，在工作區上啟用正確的解決方案。


## <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>我要如何移除資訊安全中心安裝的 OMS 擴充功能？<a name="remove-oms"></a>

您可以手動移除 Log Analytics 代理程式。 關閉資料收集會限制資訊安全中心的建議和警示，所以不建議您這麼做。

> [!NOTE]
> 如果已啟用資料收集，資訊安全中心將會在您移除代理程式後重新安裝程式。  您必須停用資料收集，再手動移除代理程式。 若要了解如何停用資料收集，請參閱「我要如何避免自動安裝代理程式和建立工作區？」。

若要手動移除代理程式：

1.    在入口網站中，開啟**Log Analytics**。

1.    在 [Log Analytics] 頁面上，選取工作區：

1.    選取您不想監視的 VM，然後選取 [中斷連線]。

   ![移除代理程式][3]

> [!NOTE]
> 如果 Linux VM 已有非延伸模組的 OMS 代理程式，移除該延伸模組的同時也會移除代理程式，且您將必須重新加以安裝。


## <a name="how-do-i-disable-data-collection"></a>我要如何停用資料收集？

強烈建議進行自動佈建，以便取得安全性警示，以及系統更新、作業系統弱點和端點保護的建議。 預設會停用自動布建。

如果您已啟用，但現在想要停用它：

1. 在 [Azure 入口網站](https://portal.azure.com)中，開啟 [ **安全性中心** ]，然後選取 [ **安全性原則**]。

1. 選取您要停用自動布建的訂用帳戶。

    **安全性原則-資料收集** 隨即開啟。

1. 在 [自動佈建] 之下，選取 [關閉]。


## <a name="how-do-i-enable-data-collection"></a>我要如何啟用資料收集？

您可以在安全性原則中為您的 Azure 訂用帳戶啟用資料收集。 啟用資料收集。 [登入 Azure 入口網站](https://portal.azure.com)，選取 [瀏覽]，選取 [資訊安全中心]，然後選取 [安全性原則]。 選取您想要啟用自動佈建的訂用帳戶。 當您選取訂用帳戶時，[安全性原則 - 資料收集] 隨即開啟。 在 [自動佈建] 之下，選取 [開啟]。


## <a name="what-happens-when-data-collection-is-enabled"></a>啟用資料收集時會發生什麼情況？

啟用自動佈建時，資訊安全中心會在所有支援的 Azure 虛擬機器和任何新建立的虛擬機器上佈建 Log Analytics 代理程式。 建議使用自動佈建，但是手動代理程式安裝也可行。 [了解如何安裝 Log Analytics 代理程式延伸模組](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)。 

代理程式支援處理序建立事件 4688，及事件 4688 中的 *CommandLine* 欄位。 EventLog 會記錄在 VM 上建立的新處理序，並由資訊安全中心的偵測服務監視。 若要進一步了解為每個新程序記錄的詳細資料，請參閱 [4688 中的描述欄位](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields)。 代理程式也會收集在 VM 上建立的 4688 事件並將它們儲存在搜尋中。

代理程式也會啟用[自適性應用程式控制](security-center-adaptive-application.md)的資料收集，資訊安全中心會在稽核模式中設定本機 AppLocker 原則以允許所有應用程式。 此原則會導致 AppLocker 產生事件，然後由資訊安全中心收集並利用。 請務必注意，在已經設定 AppLocker 原則的機器上不會設定此原則。 

如果客戶提供[安全性連絡資訊](security-center-provide-security-contact-details.md)，當資訊安全中心在 VM 上偵測到可疑的活動時，客戶就會收到通知電子郵件。 資訊安全中心的安全性警示儀表板也會顯示警示。


## <a name="will-security-center-work-using-an-oms-gateway"></a>資訊安全中心是否會使用 OMS 閘道來運作？

是。 Azure 資訊安全中心使用 Log Analytics 代理程式，利用 Azure 監視器從 Azure VM 和伺服器收集資料。
為了收集資料，每個 VM 和伺服器都必須使用 HTTPS 連線至網際網路。 此連線可以是直接連線，或是使用 Proxy或透過 [OMS 閘道](../azure-monitor/platform/gateway.md)的連線。


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Monitoring Agent 是否會影響伺服器的效能？

代理程式只耗用少量的系統資源，對效能的影響應該很小。 如需有關效能影響和代理程式與擴充的詳細資訊，請參閱[規劃和操作指南](security-center-planning-and-operations-guide.md#data-collection-and-storage)。




<!--Image references-->
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png
