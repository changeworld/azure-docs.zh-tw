---
title: Azure 資訊安全中心疑難排解指南 | Microsoft Docs
description: 本指南適用於需要解決 Azure 安全中心相關問題的 IT 專業人員、安全分析師和雲端管理員。
services: security-center
author: v-miegge
manager: dcscontentpm
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: b5a85f8ae1564d724b826c809261e56577f4783a
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435536"
---
# <a name="azure-security-center-troubleshooting-guide"></a>Azure 資訊安全中心疑難排解指南

本指南適用於組織目前採用 Azure 資訊安全中心，且需要針對資訊安全中心相關問題進行疑難排解的資訊技術 (IT) 專業人員、資訊安全性分析師和雲端系統管理員。

安全中心使用日誌分析代理收集和存儲數據。 若要深入了解，請參閱 [Azure 資訊安全中心平台移轉](security-center-platform-migration.md)。 本文中的資訊表示向日誌分析代理過渡後的安全中心功能。

## <a name="troubleshooting-guide"></a>疑難排解指南

本指南說明如何針對資訊安全中心相關問題進行疑難排解。

警示類型:

* 虛擬機器行為分析 (VMBA)
* 網路分析
* SQL Database 和 SQL 資料倉儲分析
* 內容相關的資訊

根據警示類型，客戶可使用下列資源收集必要的資訊來調查警示：

* Windows 中的虛擬機器 (VM) 事件檢視器內的安全性記錄
* Linux 中的 AuditD
* Azure 活動記錄，和攻擊資源的啟用診斷記錄。

客戶可以針對警示描述和相關資訊分享意見反應。 瀏覽至警示本身，選取 [這有幫助嗎]**** 按鈕並選取原因，然後輸入註解來說明意見反應。 我們會持續監視此意見反應通道，以改善我們的警示功能。

## <a name="audit-log"></a>稽核記錄檔

大多數在資訊安全中心進行的疑難排解作業會先查看失敗元件的[稽核記錄](../azure-monitor/platform/platform-logs-overview.md)。 透過稽核記錄，您可以判斷︰

* 已發生的作業
* 起始作業的人員
* 發生作業的時間
* 作業的狀態
* 其他可能協助您研究作業的屬性值

稽核記錄檔包含在您的資源上執行的所有寫入作業 (PUT、POST、DELETE)，但不包含讀取作業 (GET)。

## <a name="log-analytics-agent"></a>Log Analytics 代理程式

安全中心使用日誌分析代理(這是 Azure 監視器服務使用的代理)從 Azure 虛擬機收集安全數據。 啟用資料收集且代理程式已正確安裝在目標電腦之後，以下處理序應在執行中︰

* HealthService.exe

如果打開服務管理主控台 (service.msc),您還將看到日誌分析代理服務運行如下:

![服務](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig5.png)

要查看您擁有哪個版本的代理,請在 **「行程**」選項卡中打開**工作管理員**,找到**紀錄分析代理服務**,右鍵按下它並按下「**屬性**」 。。 在 [詳細資料]**** 索引標籤上，查看如下所示的檔案版本：

![檔案](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig6.png)

## <a name="log-analytics-agent-installation-scenarios"></a>紀錄分析代理安裝方案

在計算機上安裝日誌分析代理時,有兩種安裝方案可以產生不同的結果。 支援的案例如下：

* **資訊安全中心自動安裝的代理程式**：在此案例中，您能夠在兩個位置 (資訊安全中心和記錄搜尋) 檢視警示。 您將收到到資源所屬訂閱的安全策略中設定的電子郵件地址的電子郵件通知。

* **代理手動安裝在 Azure 中的 VM 上**:在這種情況下,如果您使用的是 2017 年 2 月之前手動下載和安裝的代理,則只有在篩選工作區所屬的訂閱時,才能在安全中心門戶中查看警報。 如果在資源所屬的訂閱上篩選,則看不到任何警報。 您將收到到工作區所屬訂閱的安全策略中配置的電子郵件地址的電子郵件通知。

> [!NOTE]
> 若要避免第二個案例中說明的行為，務必下載最新版的代理程式。

## <a name="monitoring-agent-health-issues"></a>監測代理健康問題<a name="mon-agent"></a>

[監視狀態]**** 可定義資訊安全中心無法成功監視為了自動佈建而初始化之 VM 和電腦的原因。 下表顯示 [監視狀態]**** 值、描述和解決步驟。

| 監視狀態 | 描述 | 解決步驟 |
|---|---|---|
| 擱置代理程式安裝 | 日誌分析代理安裝仍在運行。  安裝作業可能需要多達數小時的時間。 | 等到自動安裝完成為止。 |
| 電源為關閉狀態 | VM 已停止。  日誌分析代理只能安裝在正在運行的 VM 上。 | 重新啟動 VM。 |
| Azure VM 代理程式遺漏或無效 | 尚未安裝日誌分析代理。  資訊安全中心需要有效的 Azure VM 代理程式，才能安裝擴充功能。 | 在 VM 上安裝、重新安裝或升級 Azure VM 代理程式。 |
| VM 尚未處於安裝就緒的狀態  | 日誌分析代理尚未安裝,因為 VM 尚未準備好安裝。 因為 VM 代理程式或 VM 佈建有問題，所以 VM 尚未處於安裝就緒的狀態。 | 檢查 VM 的狀態。 返回入口網站中的 [虛擬機器]**** 並選取 VM，以取得狀態資訊。 |
|安裝失敗 - 一般錯誤 | 日誌分析代理已安裝,但因錯誤而失敗。 | [手動安裝擴充功能](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)或將擴充功能解除安裝，資訊安全中心就會嘗試再次安裝。 |
| 安裝失敗 - 已安裝本機代理程式 | 日誌分析代理安裝失敗。 安全中心標識了已在 VM 上安裝的本地代理(日誌分析或系統中心操作管理員)。 為了避免多宿主配置(其中 VM 報告到兩個單獨的工作區),日誌分析代理安裝已停止。 | 解決方式有兩種：[手動安裝擴充功能](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)並將它連線到您所需的工作區。 或者，將您所需的工作區設定為預設工作區，並啟用代理程式的自動佈建功能。  請參閱[啟用自動佈建](security-center-enable-data-collection.md)。 |
| 代理程式無法連線到工作區 | 日誌分析代理已安裝,但由於網路連接而失敗。  請檢查是否有網際網路存取權，或已針對代理程式設定有效的 HTTP Proxy。 | 請參閱「監視代理程式網路需求」。 |
| 代理程式已連線到遺漏或未知的工作區 | 安全中心確認安裝在 VM 上的日誌分析代理已連接到它無法訪問的工作區。 | 有兩種情況會發生這種情形。 工作區已遭刪除而不復存在。 重新安裝具有正確工作區的代理程式，或將代理程式解除安裝並允許資訊安全中心完成其自動佈建安裝。 第二種情況是工作區屬於資訊安全中心無權存取的訂用帳戶。 資訊安全中心需有訂用帳戶，才可允許 Microsoft 安全性資源提供者進行存取。 若要啟用這項功能，請向 Microsoft 安全性資源提供者註冊訂用帳戶。 此作業可經由 API、PowerShell、入口網站完成，而在資訊安全中心的 [概觀]**** 儀表板中直接篩選訂用帳戶亦可完成。 如需詳細資訊，請參閱[資源提供者和類型](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)。 |
| 代理程式沒有回應或缺少識別碼 | 即使已安裝代理程式，資訊安全中心仍無法從 VM 擷取已掃描的安全性資料。 | 代理程式並未回報任何資料，包括活動訊號。 代理程式可能已損毀，或有物件封鎖流量。 或者,代理正在報告數據,但缺少 Azure 資源 ID,因此無法將數據與 Azure VM 匹配。 若要對 Linux 進行疑難排解，請參閱[適用於 Linux 的 Log Analytics 代理程式疑難排解指南](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md#im-not-seeing-any-linux-data-in-the-oms-portal)。 若要對 Windows 進行疑難排解，請參閱[對 Windows 虛擬機器進行疑難排解](https://github.com/MicrosoftDocs/azure-docs/blob/8c53ac4371d482eda3d85819a4fb8dac09996a89/articles/log-analytics/log-analytics-azure-vm-extension.md#troubleshooting-windows-virtual-machines)。 |
| 未安裝代理程式 | 資料收集已啟用。 | 在安全策略中打開數據收集或手動安裝日誌分析代理。 |

## <a name="troubleshooting-monitoring-agent-network-requirements"></a>容錯排除監控代理網路要求<a name="mon-network-req"></a>

代理程式若要連線到資訊安全中心並向其註冊，就必須能夠存取網路資源，包括連接埠號碼和網域 URL。

* 對於 Proxy 伺服器，您需要確保代理程式設定中已設定了適當的 Proxy 伺服器資源。 如需有關[如何變更 Proxy 設定](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents)的詳細資訊，請參閱這篇文章。
* 對於限制網際網路存取的防火牆，您需要設定防火牆以允許存取 Log Analytics。 您不需要在代理程式設定中進行任何動作。

下表說明通訊所需資源。

| 代理程式資源 | 連接埠 | 略過 HTTPS 檢查 |
|---|---|---|
| *.ods.opinsights.azure.com | 443 | 是 |
| *.oms.opinsights.azure.com | 443 | 是 |
| *.blob.core.windows.net | 443 | 是 |
| *.azure-automation.net | 443 | 是 |

如果您遇到代理程式的登入問題，請務必閱讀[如何針對 Operations Management Suite 登入問題進行疑難排解](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues)一文。

## <a name="troubleshooting-endpoint-protection-not-working-properly"></a>針對端點保護無法正常運作的問題進行疑難排解

客體代理程式為 [Microsoft Antimalware](../security/fundamentals/antimalware.md) 擴充功能所做一切的父處理序。 當客體代理程式處理序失敗時，做為客體代理程式子處理序執行的 Microsoft Antimalware 可能也會失敗。  在類似案例中，建議您確認下列選項：

* 目標 VM 是否為自訂映像，而且 VM 的建立者從未安裝客體代理程式。
* 如果目標為 Linux VM 而不是 Windows VM，則在 Linux VM 上安裝反惡意程式碼擴充功能的 Windows 版本將會失敗。 Linux 客體代理程式對於 OS 版本和所需的套件具有特定需求，而且如果未符合這些需求，VM 代理程式也無法在該處運作。
* VM 是否是使用舊版客體代理程式所建立。 如果是，您應該注意，某些舊版代理程式可能不會將本身自動更新為較新版本，而這會導致此問題。 如果建立您自己的映像，請一律使用最新版的客體代理程式。
* 某些第三方系統管理軟體可能會停用客體代理程式，或封鎖特定檔案位置的存取。 如果您在 VM 上安裝了第三方軟體，請確定代理程式位於排除清單上。
* 某些防火牆設定或網路安全性群組 (NSG) 可能會封鎖進出客體代理程式的網路流量。
* 某些存取控制清單 (ACL) 可能會防止磁碟存取。
* 磁碟空間不足會封鎖客體代理程式而使其無法正確運作。

預設會停用 Microsoft Antimalware 使用者介面，如需如何視需要啟用此功能的詳細資訊，請閱讀[在 Azure Resource Manager VM 後續部署上啟用 Microsoft Antimalware 使用者介面 (英文)](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/09/enabling-microsoft-antimalware-user-interface-post-deployment/)。

## <a name="troubleshooting-problems-loading-the-dashboard"></a>針對載入儀表板的問題進行疑難排解

如果您遇到載入資訊安全中心儀表板的問題，請確定向資訊安全中心註冊訂用帳戶的使用者 (也就是第一位使用訂用帳戶開啟資訊安全中心的使用者) 以及想要開啟資料收集的使用者，應該是訂用帳戶的「擁有者」** 或「參與者」**。 另外，從那時起，訂用帳戶上具有「讀取者」** 身分的使用者就可以看到儀表板/警示/建議/原則。

## <a name="contacting-microsoft-support"></a>連絡 Microsoft 支援服務

使用本文提供的指導方針可識別一些問題，您也可能發現的其他問題則記載於資訊安全中心的公開 [論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter)。 不過，如果您需要進一步疑難排解，您可以使用 **Azure 入口網站**開啟新的支援要求，如下所示︰

![Microsoft 支援服務](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)

## <a name="see-also"></a>另請參閱

在本文件中，您已了解如何在「Azure 資訊安全中心」設定安全性原則。 若要深入了解「Azure 資訊安全中心」，請參閱下列主題：

* [Azure 安全中心規劃和操作指南](security-center-planning-and-operations-guide.md)– 瞭解如何規劃和了解採用 Azure 安全中心的設計注意事項。
* [Azure 安全中心中的安全執行狀況監視](security-center-monitoring.md)• 瞭解如何監視 Azure 資源的運行狀況
* [管理和回應 Azure 安全中心中的安全警報](security-center-managing-and-responding-alerts.md)- 瞭解如何管理和回應安全警報
* [瞭解 Azure 安全中心中的安全警報](security-center-alerts-type.md)
* [教學課程：回應安全性事件](tutorial-security-incident.md)
* [Azure 資訊安全中心的警示驗證](security-center-alert-validation.md)
* [Azure 資訊安全中心的電子郵件通知](security-center-provide-security-contact-details.md)
* [在 Azure 資訊安全中心處理安全性事件](security-center-incident.md)
* [在 Azure 資訊安全中心調查事件和警示](security-center-investigation.md)
* [Azure 資訊安全中心的偵測功能](security-center-detection-capabilities.md)
* [使用 Azure 安全中心監視合作夥伴解決方案](security-center-partner-solutions.md)– 瞭解如何監視合作夥伴解決方案的運行狀況。
* [Azure 安全中心常見問題](faq-general.md)解答 - 搜尋有關使用服務的常見問題
* [Azure 安全部落格](https://blogs.msdn.com/b/azuresecurity/)- 尋找有關 Azure 安全性和合規性的部落格文章
