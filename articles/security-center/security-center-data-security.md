---
title: Azure 資訊安全中心資料安全性 | Microsoft Docs
description: 本文件說明如何在 Azure 資訊安全中心管理和保護資料。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2020
ms.author: memildin
ms.openlocfilehash: dfa3f00e668488574abeb08964909a8972c8913f
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772942"
---
# <a name="azure-security-center-data-security"></a>Azure 資訊安全中心資料安全性
為了協助客戶預防、偵測和回應威脅，Azure 資訊安全中心會收集並處理安全性相關的資料，包括設定資訊、中繼資料、事件記錄檔等等。 Microsoft 從撰寫程式碼到運作服務均遵守嚴格的規範與安全性指導方針。

本文說明如何在 Azure 資訊安全中心管理和保護資料。

## <a name="data-sources"></a>資料來源
Azure 資訊安全中心會分析來自下列來源的資料，以掌握您的安全性狀態、識別弱點並提供建議防護功能，並偵測作用中的威脅︰

- Azure 服務：與 Azure 服務的資源提供者通訊，以使用您所部署之 Azure 服務的組態相關資訊。
- 網路流量：使用從 Microsoft 的基礎結構取樣的網路流量中繼資料，例如來源/目的地 IP/連接埠、封包大小和網路通訊協定。
- 合作夥伴解決方案：使用整合式合作夥伴解決方案 (例如防火牆和反惡意程式碼解決方案) 的安全性警示。
- 您的虛擬機器和伺服器：使用設定資訊及安全性事件的相關資訊，例如來自虛擬機器的 Windows 事件和稽核記錄、IIS 記錄及 syslog 訊息。 此外，建立警示時，Azure 資訊安全中心可能會產生受影響 VM 磁碟的快照集並從 VM 磁碟擷取與警示相關的機器構件 (例如登錄檔)，以供鑑識之用。


## <a name="data-protection"></a>資料保護
**資料隔離**：資料會以邏輯方式分開保存在服務的每個元件上。 每個組織加上標記的所有資料。 這項標記作業在整個資料生命週期持續發生，它會強制執行服務的每個層級。

**資料存取**：為了提供安全性建議及調查潛在的安全性威脅，Microsoft 人員可能會存取 Azure 服務所收集或分析的資訊，包含程序建立事件、VM 磁碟快照集和成品，但這可能無意中包含來自虛擬機器的客戶資料或個人資料。 我們會遵守 [Microsoft Online Services Terms 和隱私權聲明](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)，其中陳述 Microsoft 不會使用客戶資料或從中衍生資訊作為任何廣告或類似的商業用途。 我們會視需要只使用客戶資料為您提供 Azure 服務，包括與提供這些服務相容的用途。 您可保有客戶資料的所有權限。

**資料使用**：Microsoft 使用可見於多個租用戶的模式和威脅智慧來加強我們的防護和偵測功能；我們會根據[隱私權聲明](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx)中所述的隱私權承諾進行。

## <a name="data-location"></a>資料位置

**您的工作區**：下列地區各有指定的工作區，而從 Azure 虛擬機器收集的資料 (包括某些類型的警示資料) 會儲存在最近的工作區。

| VM 地區                              | 工作區地區 |
|-------------------------------------|---------------|
| 美國、巴西、南非 | 美國 |
| Canada                              | Canada        |
| 歐洲 (英國除外)   | 歐洲        |
| United Kingdom                      | United Kingdom |
| 亞洲 (印度、日本、韓國、中國除外)   | 亞太地區  |
| 南韓                              | 亞太地區  |
| 印度                               | 印度         |
| 日本                               | 日本         |
| 中國                               | 中國         |
| 澳大利亞                           | 澳大利亞     |


VM 磁碟快照集會儲存在與 VM 磁碟相同的同一個儲存體帳戶中。

針對在其他環境中執行的虛擬機器和伺服器 (例如內部部署)，您可以指定要儲存所收集資料的工作區和區域。

**Azure 資訊安全中心儲存體**：有關安全性警示 (包括夥伴警示) 的資訊會根據相關 Azure 資源的位置以區域性方式儲存，而安全性健康情況狀態和建議的相關資訊則會以客戶的位置作為基礎，集中儲存在美國和歐洲。 機器構件會集中儲存在與 VM 相同的區域。

## <a name="managing-data-collection-from-virtual-machines"></a>從虛擬機器管理資料收集

當您啟用 Azure 中的資訊安全中心時，已針對每個 Azure 訂用帳戶開啟資料收集。 您也可以在 Azure 資訊安全中心的 [安全性原則] 區段中開啟訂用帳戶的資料收集。 開啟資料收集時，Azure 資訊安全中心會在所有現有支援的 Azure 虛擬機器和任何新建立的虛擬機器上，佈建 Log Analytics 代理程式。
Log Analytics 代理程式會掃描各種安全性相關設定，並轉換成 [Windows 事件追蹤](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) 的追蹤事件。 此外，作業系統會在執行機器的過程中引發事件記錄檔事件。 這類資料的範例包括︰作業系統類型和版本、作業系統記錄 (Windows 事件記錄)、執行中程序、電腦名稱、IP 位址、已登入的使用者和租用戶識別碼。 Log Analytics 代理程式會讀取事件記錄項目和 ETW 追蹤，並複製到您的工作區進行分析。 Log Analytics 代理程式也會啟用程序建立事件和命令列稽核。

如果您是使用免費版的 Azure 安全性中心，也可以在安全性原則中，從虛擬機器停用資料收集。 標準層上的訂用帳戶需要資料收集。 即使已停用資料集合，仍然會啟用VM 磁碟快照集和構件集合。

## <a name="data-consumption"></a>資料耗用量

客戶可以取用來自不同資料流的資訊安全中心相關資料，如下所示：

* **Azure 活動**：所有安全性警示、已核准的資訊安全中心 [Just-In-Time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) 要求，以及[自適性應用程式控制](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)產生的所有警示。
* **Azure 監視器記錄**：所有安全性警示。


> [!NOTE]
> 您也可透過 REST API 取用安全性建議。 如需詳細資訊，請參閱[安全性資源提供者 REST API 參考](https://msdn.microsoft.com/library/mt704034(Azure.100).aspx)。

## <a name="see-also"></a>另請參閱
在本文件中，您已了解如何在 Azure 資訊安全中心管理和保護資料。 若要深入了解 Azure 資訊安全中心，請參閱：

* [Azure 資訊安全中心規劃和操作指南](security-center-planning-and-operations-guide.md) — 了解如何規劃及了解採用 Azure 資訊安全中心的設計考量。
* [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md) — 了解如何監視 Azure 資源的健全狀況。
* [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) — 了解如何管理與回應安全性警示。
* [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md) — 了解如何監視合作夥伴解決方案的健全狀況。
* [Azure 安全性部落格](https://blogs.msdn.com/b/azuresecurity/) — 尋找有關 Azure 安全性與相容性的部落格文章。
