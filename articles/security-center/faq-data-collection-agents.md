---
title: Azure 資訊安全中心常見問題-資料收集和代理程式
description: 關於 Azure 資訊安全中心的資料收集、代理程式和工作區的常見問題，這是一種可協助您預防、偵測及回應威脅的產品
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
ms.openlocfilehash: 8317a13b9ef87679836f55627268deefa4500dce
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599427"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>常見問題-資料收集、代理程式和工作區的相關問題

資訊安全中心會從您的 Azure 虛擬機器（Vm）、虛擬機器擴展集、IaaS 容器和非 Azure 電腦（包括內部部署機器）收集資料，以監視是否有安全性弱點和威脅。 資料是使用 Microsoft Monitoring Agent 收集而得，收集的方式是讀取機器的各種安全性相關組態和事件記錄，並將資料複製到工作區進行分析。


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>在資訊安全中心所建立的工作區上，我是否需支付 Azure 監視器記錄的費用？

否。 資訊安全中心建立的工作區，而針對每個節點計費的 Azure 監視器記錄設定，則不會產生 Azure 監視器記錄費用。 資訊安全中心的計費一律根據您的資訊安全中心的安全性原則，以及工作區安裝的解決方案：

- **免費層** – 資訊安全中心在預設工作區啟用 'SecurityCenterFree' 解決方案。 免費層不會向您收費。

- **標準層** – 資訊安全中心在預設工作區啟用 'Security' 解決方案。

如需詳細資訊，請參閱[資訊安全中心價格](https://azure.microsoft.com/pricing/details/security-center/)。

> [!NOTE]
> 資訊安全中心所建立之工作區的 log analytics 定價層不會影響資訊安全中心計費。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>安裝 Microsoft Monitoring Agent 自動佈建的 VM 需要什麼資格？

符合下列條件的 Windows 或 Linux IaaS 虛擬機器：

- 虛擬機器目前尚未安裝 Microsoft Monitoring Agent 擴充功能。
- 虛擬機器處於執行狀態。
- 已安裝 Windows 或 Linux [Azure 虛擬機器代理程式](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)。
- 虛擬機器沒有作為 Web 應用程式防火牆或新一代防火牆等設備使用。


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>我可以刪除資訊安全中心所建立的預設工作區嗎？

**建議您不要刪除預設工作區。** 資訊安全中心會使用預設工作區儲存您虛擬機器傳來的安全性資料。 如果您刪除了工作區，資訊安全中心無法收集此資料，特定安全性建議及警示就無法使用。

若要復原，請將連線到刪除工作區之虛擬機器上的 Microsoft Monitoring Agent 移除。 資訊安全中心會重新安裝代理程式，並建立新的預設工作區。

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

1. 選取 [儲存]。 系統會詢問您是否要重新設定受監視的 Vm。

    - 如果您希望新的工作區設定**僅套用在新的虛擬機器**，請選取 [否]。 新的工作區設定只會套用在新安裝的代理程式，以及新探索到的未安裝 Microsoft Monitoring Agent 之虛擬機器。
    - 如果您希望新的工作區設定**套用在所有虛擬機器**，請選取 [是]。 此外，每個連線到資訊安全中心建立之工作區的虛擬機器會重新連線到新的目標工作區。

    > [!NOTE]
    > 如果您選取 **[是]** ，則在所有 vm 都已重新連接到新的目標工作區之前，請勿刪除資訊安全中心建立的任何工作區。 如果過早刪除工作區，這項作業將會失敗。

    - 若要取消作業，請選取 [**取消**]。

## 如果 Microsoft Monitoring Agent 已安裝為 VM 上的擴充功能，該怎麼辦？<a name="mmaextensioninstalled"></a>

當監視代理程式安裝為擴充功能時，延伸模組設定只允許報告至單一工作區。 資訊安全中心不會覆寫既存的使用者工作區連線。 如果已安裝「安全性」或「SecurityCenterFree」解決方案，資訊安全中心會將 VM 中的安全性資料儲存在已連線的工作區中。 資訊安全中心可以將延伸模組版本升級為此程式中的最新版本。

如需詳細資訊，請參閱[在預先存在的代理程式安裝情況下自動布建](security-center-enable-data-collection.md#preexisting)。



## 如果 Microsoft Monitoring Agent 直接安裝在電腦上，而不是延伸模組（直接代理程式），該怎麼辦？<a name="directagentinstalled"></a>

如果 Microsoft Monitoring Agent 直接安裝在 VM （而不是 Azure 擴充功能）上，資訊安全中心會安裝 Microsoft Monitoring Agent 延伸模組，而且可能會將 Microsoft Monitoring Agent 升級至最新版本。

安裝的代理程式會繼續向其已設定的工作區報告，此外也會向資訊安全中心中設定的工作區報告（Windows 電腦上支援多路連接）。

如果設定的工作區是使用者工作區（而不是資訊安全中心的預設工作區），您將需要在其上安裝 "Security/" SecurityCenterFree "解決方案，資訊安全中心才能開始處理向該工作區報告的 Vm 和電腦的事件。

針對 Linux 電腦，尚不支援代理程式多路連接-因此，如果偵測到現有的代理程式安裝，將不會進行自動布建，且不會改變電腦的設定。

對於在 17 2019 年3月之前資訊安全中心的訂用帳戶上的現有機器上架，當偵測到現有的代理程式時，將不會安裝 Microsoft Monitoring Agent 延伸模組，而且電腦也不會受到影響。 如需這些電腦的相關資訊，請參閱「解決電腦上的監視代理程式健康情況問題」的建議，以解決這些電腦上的代理程式安裝問題

如需詳細資訊，請參閱下一節[如果我的 VM 上已安裝 System Center Operations Manager 或 OMS 直接代理程式，會發生什麼情況？](#scomomsinstalled)

## 如果我的 VM 上已安裝 System Center Operations Manager 代理程式，該怎麼辦？<a name="scomomsinstalled"></a>

[安全性中心] 會將 Microsoft Monitoring Agent 延伸模組並存安裝到現有的 System Center Operations Manager 代理程式。 現有的代理程式會繼續正常地向 System Center Operations Manager 伺服器報告。 請注意，Operations Manager 代理程式和 Microsoft Monitoring Agent 共用一般執行時間程式庫，這會在此程式期間更新為最新版本。 注意-如果已安裝 Operations Manager 代理程式的2012版，請勿開啟自動布建（當 Operations Manager 伺服器也是2012版）時，管理功能可能會遺失。


## <a name="what-is-the-impact-of-removing-these-extensions"></a>移除這些擴充功能會有什麼影響？

如果您移除了 Microsoft Monitoring 擴充功能，資訊安全中心會無法收集虛擬機器送出的安全性資料，且特定安全性建議及警示將無法使用。 24 小時內，資訊安全中心會判定虛擬機器缺少了擴充功能，並重新安裝。


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>我要如何避免自動安裝代理程式和建立工作區？

您可以在安全性原則中關閉訂用帳戶的自動佈建，但不建議您這麼做。 關閉自動佈建會限制資訊安全中心的建議和警示。 若要停用自動佈建：

1. 如果您的訂用帳戶設定為標準層，請開啟該訂用帳戶的安全性原則，並選取**免費**層。

   ![定價層][1]

1. 接下來，在 [**安全性原則-資料收集**] 頁面上選取 [**關閉**] 來關閉自動布建。
   ![資料收集][2]


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>我應該要退出自動安裝代理程式和建立工作區嗎？

> [!NOTE]
> 如果您選擇退出自動佈建，請務必檢閱[退出的影響為何？](#what-are-the-implications-of-opting-out-of-automatic-provisioning)和[退出時的建議步驟](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning)區段。

如果您符合下列條件，您可以選擇退出自動佈建：

- 資訊安全中心的自動代理程式安裝適用於整個訂用帳戶。 您無法將自動安裝套用到虛擬機器的子集。 如果有重要虛擬機器無法使用 Microsoft Monitoring Agent 安裝，您應該退出自動佈建。
- 安裝 Microsoft Monitoring Agent （MMA）擴充功能會更新代理程式的版本。 這適用于直接代理程式和 System Center Operations Manager 代理程式（在後者中，Operations Manager 和 MMA 共用通用執行時間程式庫，會在進程中更新）。 如果安裝的 Operations Manager 代理程式是2012版，而且已升級，當 Operations Manager 伺服器也是2012版時，管理功能可能會遺失。 如果已安裝的 Operations Manager 代理程式為2012版，請考慮退出宣告自動布建。
- 如果您有訂用帳戶外部的自訂工作區（集中式工作區），則您應該選擇不進行自動布建。 您可以手動安裝 Microsoft Monitoring Agent 擴充功能，然後將其連線至您的工作區，而不是由資訊安全中心覆寫連線。
- 如果您不想在每個訂用帳戶上建立多個工作區，而且訂用帳戶中有您自己的自訂工作區，則您有兩個選項：

   1. 您可以退出自動佈建。 移轉之後，依循[我可以如何使用現有 Log Analytics 工作區？](#how-can-i-use-my-existing-log-analytics-workspace)中的說明來設定預設工作區的設定

   1. 或者，您可以讓移轉程序完成，讓 Microsoft Monitoring Agent 安裝在虛擬機器上，然後讓虛擬機器連線至已建立的工作區。 接著，藉由選擇重新設定已安裝的代理程式，在設定預設工作區的設定上，選取您的自訂工作區。 如需詳細資訊，請參閱[如何使用我現有的 Log Analytics 工作區？](#how-can-i-use-my-existing-log-analytics-workspace)


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>退出自動佈建有什麼影響？

當遷移完成時，資訊安全中心無法從 VM 收集安全性資料，而且某些安全性建議和警示無法使用。 如果您退出宣告，請手動安裝 Microsoft Monitoring Agent。 請參閱[退出時的建議步驟](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning)。


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>退出自動佈建時的建議步驟是什麼？

手動安裝 Microsoft Monitoring Agent 擴充功能，讓資訊安全中心可以從您的 Vm 收集安全性資料，並提供建議和警示。 關於安裝的指引，請參閱 [Windows 虛擬機器的代理程式安裝](../virtual-machines/extensions/oms-windows.md)或 [Linux 虛擬機器的代理程式安裝](../virtual-machines/extensions/oms-linux.md)。

您可以將代理程式連線到任何現有的自訂工作區，或資訊安全中心建立的工作區。 如果自訂工作區並沒有啟用的 'Security' 或 'SecurityCenterFree' 解決方案，則您必須套用解決方案。 若要套用，請選取自訂工作區或訂用帳戶，並透過 [**安全性原則-定價層**] 頁面來套用定價層。

   ![定價層][1]

資訊安全中心會根據選取的定價層，在工作區上啟用正確的解決方案。


## 我要如何移除資訊安全中心安裝的 OMS 擴充功能？<a name="remove-oms"></a>

您可以手動移除 Microsoft Monitoring Agent。 關閉資料收集會限制資訊安全中心的建議和警示，所以不建議您這麼做。

> [!NOTE]
> 如果已啟用資料收集，資訊安全中心將會在您移除代理程式後重新安裝程式。  您必須停用資料收集，再手動移除代理程式。 若要了解如何停用資料收集，請參閱「我要如何避免自動安裝代理程式和建立工作區？」。

若要手動移除代理程式：

1.  在入口網站中，開啟**Log Analytics**。

1.  在 [Log Analytics] 頁面上，選取工作區：

1.  選取您不想要監視的 Vm，然後選取 **[中斷連線]** 。

   ![移除代理程式][3]

> [!NOTE]
> 如果 Linux VM 已經有非擴充功能的 OMS 代理程式，移除延伸模組也會移除代理程式，因此您必須重新安裝它。


## <a name="how-do-i-disable-data-collection"></a>我要如何停用資料收集？

預設會關閉自動佈建。 您可以在安全性原則中關閉這項設定，隨時停用資源的自動佈建。 強烈建議使用自動布建，以取得有關系統更新、作業系統弱點和 endpoint protection 的安全性警示和建議。

若要停用資料收集，請[登入 Azure 入口網站](https://portal.azure.com)，選取 [瀏覽]，選取 [資訊安全中心]，然後選取 [選取原則]。 選取您想要停用自動佈建的訂用帳戶。 當您選取訂用帳戶時，[安全性原則 - 資料收集] 隨即開啟。 在 [自動佈建] 之下，選取 [關閉]。


## <a name="how-do-i-enable-data-collection"></a>我要如何啟用資料收集？

您可以在安全性原則中為您的 Azure 訂用帳戶啟用資料收集。 啟用資料收集。 [登入 Azure 入口網站](https://portal.azure.com)，選取 [瀏覽]，選取 [資訊安全中心]，然後選取 [安全性原則]。 選取您想要啟用自動佈建的訂用帳戶。 當您選取訂用帳戶時，[安全性原則 - 資料收集] 隨即開啟。 在 [自動佈建] 之下，選取 [開啟]。


## <a name="what-happens-when-data-collection-is-enabled"></a>啟用資料收集時會發生什麼情況？

啟用自動佈建時，資訊安全中心會在所有支援的 Azure VM 和任何新建立的 VM 上佈建 Microsoft Monitoring Agent。 建議使用自動布建，但也提供手動代理程式安裝。 [深入了解如何安裝 Microsoft Monitoring Agent 擴充功能](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)。 

代理程式支援處理序建立事件 4688，及事件 4688 中的 *CommandLine* 欄位。 EventLog 會記錄在 VM 上建立的新處理序，並由資訊安全中心的偵測服務監視。 如需每個新程式所記錄之詳細資料的詳細資訊，請參閱[4688 中的描述欄位](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields)。 代理程式也會收集在 VM 上建立的 4688 事件並將它們儲存在搜尋中。

代理程式也會啟用[自適性應用程式控制](security-center-adaptive-application.md)的資料收集，資訊安全中心會在稽核模式中設定本機 AppLocker 原則以允許所有應用程式。 此原則會導致 AppLocker 產生事件，然後資訊安全中心加以收集和利用。 請務必注意，在已經設定 AppLocker 原則的機器上不會設定此原則。 

如果客戶提供[安全性連絡資訊](security-center-provide-security-contact-details.md)，當資訊安全中心在 VM 上偵測到可疑的活動時，客戶就會收到通知電子郵件。 資訊安全中心的安全性警示儀表板也會顯示警示。


## <a name="will-security-center-work-using-an-oms-gateway"></a>資訊安全中心使用 OMS 閘道來進行工作嗎？

是。 Azure 資訊安全中心利用 Azure 監視器，使用 Microsoft Monitoring Agent 從 Azure Vm 和伺服器收集資料。
若要收集資料，每部 VM 和伺服器都必須使用 HTTPS 連接到網際網路。 連接可以是 direct、使用 proxy 或透過[OMS 閘道](../azure-monitor/platform/gateway.md)。


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Monitoring Agent 是否會影響伺服器的效能？

代理程式只耗用少量的系統資源，對效能的影響應該很小。 如需有關效能影響和代理程式與擴充的詳細資訊，請參閱[規劃和操作指南](security-center-planning-and-operations-guide.md#data-collection-and-storage)。


## <a name="where-is-my-data-stored"></a>我的資料會儲存在何處？

從這個代理程式收集的資料會儲存在與您的訂用帳戶相關聯的現有 Log Analytics 工作區或新的工作區中。 如需詳細資訊，請參閱[資料安全性](security-center-data-security.md)。


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png