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
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2020
ms.author: memildin
ms.openlocfilehash: d829ffb9d3a264052e3f688018acd7afa854578e
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018265"
---
# <a name="azure-security-center-data-security"></a>Azure 資訊安全中心資料安全性

為了協助客戶預防、偵測和回應威脅，Azure 資訊安全中心會收集並處理安全性相關的資料，包括設定資訊、中繼資料、事件記錄檔等等。 Microsoft 從撰寫程式碼到運作服務均遵守嚴格的規範與安全性指導方針。

本文說明如何在資訊安全中心管理和保護資料。

## <a name="data-sources"></a>資料來源
資訊安全中心會分析來自下列來源的資料，以掌握您的安全性狀態、識別弱點並提供建議防護功能，並偵測作用中的威脅︰

- **Azure 服務**：與 Azure 服務的資源提供者通訊，以使用您所部署之 Azure 服務的組態相關資訊。
- **網路流量**：使用從 Microsoft 的基礎結構取樣的網路流量中繼資料，例如來源/目的地 IP/連接埠、封包大小和網路通訊協定。
- **合作夥伴解決方案**：使用整合式合作夥伴解決方案 (例如防火牆和反惡意程式碼解決方案) 的安全性警示。
- **您的電腦**：使用設定詳細資料和安全性事件相關資訊，例如 Windows 事件和稽核記錄以及來自您電腦的 syslog 訊息。


## <a name="data-protection"></a>資料保護

### <a name="data-segregation"></a>資料隔離
資料會以邏輯方式分開保存在服務的每個元件上。 每個組織加上標記的所有資料。 這項標記作業在整個資料生命週期持續發生，它會強制執行服務的每個層級。

### <a name="data-access"></a>資料存取
為了提供安全性建議及調查潛在的安全性威脅，Microsoft 人員可能會存取 Azure 服務所收集或分析的資訊，包含程序建立事件和其他成品，但這可能無意中包含來自您電腦的客戶資料或個人資料。 

我們會遵守 [Microsoft Online Services 資料保護增補](https://www.microsoftvolumelicensing.com/Downloader.aspx?DocumentId=17880)，其中陳述 Microsoft 不會使用客戶資料或從中衍生資訊作為任何廣告或類似的商業用途。 我們會視需要只使用客戶資料為您提供 Azure 服務，包括與提供這些服務相容的用途。 您可保有客戶資料的所有權限。

### <a name="data-use"></a>資料使用
Microsoft 使用可見於多個租用戶的模式和威脅智慧來加強我們的防護和偵測功能；我們會根據[隱私權聲明](https://privacy.microsoft.com/privacystatement)中所述的隱私權承諾進行。

## <a name="manage-data-collection-from-machines"></a>從電腦管理資料收集
當您啟用 Azure 中的資訊安全中心時，已針對每個 Azure 訂用帳戶開啟資料收集。 您也可以在資訊安全中心為您的訂用帳戶啟用資料收集。 啟用資料收集時，資訊安全中心會在所有現有支援的 Azure 虛擬機器和任何新建立的虛擬機器上，佈建 Log Analytics 代理程式。

Log Analytics 代理程式會掃描各種安全性相關設定，並轉換成 [Windows 事件追蹤](https://docs.microsoft.com/windows/win32/etw/event-tracing-portal) (ETW) 的追蹤事件。 此外，作業系統會在執行機器的過程中引發事件記錄檔事件。 這類資料的範例包括︰作業系統類型和版本、作業系統記錄 (Windows 事件記錄)、執行中程序、電腦名稱、IP 位址、已登入的使用者和租用戶識別碼。 Log Analytics 代理程式會讀取事件記錄項目和 ETW 追蹤，並複製到您的工作區進行分析。 Log Analytics 代理程式也會啟用程序建立事件和命令列稽核。

如果您不是使用 Azure Defender，也可以在安全性原則中，從虛擬機器停用資料收集。 Azure Defender 所保護的訂用帳戶需要進行資料收集。 即使已停用資料集合，仍然會啟用VM 磁碟快照集和構件集合。

您可以指定從您的電腦收集的資料儲存所在的工作區和區域。 預設是將從您的電腦收集的資料儲存在最近的工作區，如下表所示：

| VM 地區                                      | 工作區地區  |
|---------------------------------------------|----------------|
| 美國、巴西、南非         | 美國  |
| Canada                                      | Canada         |
| 歐洲 (英國除外)           | 歐洲         |
| United Kingdom                              | United Kingdom |
| 亞洲 (印度、日本、韓國、中國除外) | 亞太地區   |
| 南韓                                       | 亞太地區   |
| 印度                                       | 印度          |
| 日本                                       | 日本          |
| 中國                                       | 中國          |
| 澳大利亞                                   | 澳大利亞      |
|                                             |                |

> [!NOTE]
> **適用於儲存體的 Azure Defender** 會根據相關 Azure 資源的位置在區域內儲存成品。 請在[適用於儲存體的 Azure Defender 簡介](defender-for-storage-introduction.md)中深入了解。


## <a name="data-consumption"></a>資料耗用量

客戶可以從下列資料流存取資訊安全中心相關資料：


| STREAM                                                                                | 資料類型                                                                                                                                                                                                          |
|---------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Azure 活動記錄](../azure-monitor/platform/activity-log.md)                       | 所有安全性警示、已核准的資訊安全中心 [Just-In-Time](security-center-just-in-time.md) 存取要求，以及[自適性應用程式控制](security-center-adaptive-application.md)產生的所有警示。|
| [Azure 監視器記錄](../azure-monitor/platform/data-platform.md)                      | 所有安全性警示。                                                                                                                                                                                                |
| [Azure Resource Graph](../governance/resource-graph/overview.md)                      | 安全性警示、安全性建議、弱點評量結果、安全分數資訊、合規性檢查的狀態等等。                                                                       |
| [Azure 資訊安全中心 REST API](https://docs.microsoft.com/rest/api/securitycenter/) | 安全性警示、安全性建議等等。                                                                                                                                                                |
|                                                                                       |                                                                                                                                                                                                                     |

## <a name="next-steps"></a>後續步驟

在本文件中，您已了解如何在 Azure 資訊安全中心管理和保護資料。 

若要深入了解 Azure 資訊安全中心，請參閱[什麼是 Azure 資訊安全中心？](security-center-introduction.md)
