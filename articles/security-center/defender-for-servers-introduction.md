---
title: 適用於伺服器的 Azure Defender - 優點和功能
description: 了解適用於伺服器的 Azure Defender 有何優點和功能。
author: memildin
ms.author: memildin
ms.date: 9/23/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: b7457e258cf8a9936e9a63ab9eec9edc0b54d3eb
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797729"
---
# <a name="introduction-to-azure-defender-for-servers"></a>適用於伺服器的 Azure Defender 簡介

適用於伺服器的 Azure Defender 可為您的 Windows 和 Linux 機器新增威脅偵測和進階防禦功能。

針對 Windows，Azure Defender 可與 Azure 服務整合，以監視及保護您的 Windows 機器。 資訊安全中心會以便於使用的格式，提供來自這些服務的警示和補救建議。

針對 Linux，Azure Defender 會使用 **auditd** (最常見的 Linux 稽核架構之一)，從 Linux 機器收集稽核記錄。 auditd 位於主線核心中。 


## <a name="what-are-the-benefits-of-azure-defender-for-servers"></a>適用於伺服器的 Azure Defender 有哪些優點？

適用於伺服器的 Azure Defender 所提供的威脅偵測和保護功能包括：

- **適用於端點的 Microsoft Defender 整合授權 (僅限 Windows)** - 適用於伺服器的 Azure Defender 包括 [適用於端點的 Microsoft Defender](https://www.microsoft.com/microsoft-365/security/endpoint-defender)。 兩者搭配運作下，可提供完整的端點偵測和回應 (EDR) 功能。 [深入了解](security-center-wdatp.md)。

    適用於端點的 Microsoft Defender 偵測到威脅時會觸發警示。 警示會顯示在資訊安全中心中。 在資訊安全中心，您也可以切換至適用於端點的 Microsoft Defender 主控台並執行詳細的調查，以找出攻擊的範圍。 深入了解適用於端點的 Microsoft Defender。

    > [!IMPORTANT]
    > 在使用資訊安全中心的 Windows 伺服器上，會自動啟用 **適用於端點的 Microsoft Defender** 感應器。

- **VM 的弱點評量掃描** - Azure 資訊安全中心隨附的弱點掃描器由 Qualys 提供技術支援。 

    Qualys 的掃描器是用來即時識別 Azure 虛擬機器中弱點的頂級工具之一。 您不需要 Qualys 授權或 Qualys 帳戶，一切都可以在資訊安全中心內流暢進行。 [深入了解](deploy-vulnerability-assessment-vm.md)。

- **Just-In-Time (JIT) 虛擬機器 (VM) 存取** - 威脅執行者會利用開啟的管理連接埠 (如 RDP 或 SSH) 主動尋找可存取的機器。 您所有的虛擬機器都是攻擊的潛在目標。 VM 一旦遭到入侵，即會成為進入點，據以進一步攻擊您的環境中其他的資源。

    啟用適用於伺服器的 Azure Defender 時，您可以使用 Just-In-Time VM 存取來鎖定 VM 的輸入流量，進而降低暴露於攻擊的風險，同時視需要輕鬆地連線至 VM。 [深入了解](just-in-time-explained.md)。

- **檔案完整性監視 (FIM)** - 檔案完整性監視 (FIM) 也稱為變更監視，會檢查作業系統、應用程式軟體等檔案和登錄中是否有可能表示攻擊的變更。 它使用比較方法來判斷檔案的目前狀態是否不同於上次掃描的檔案。 您可以利用這項比較，來判斷對您的檔案所做的修改為有效或可疑。

    啟用適用於伺服器的 Azure Defender 時，您可以使用 FIM 來驗證 Windows 檔案、Windows 登錄和 Linux 檔案的完整性。 [深入了解](security-center-file-integrity-monitoring.md)。

- **自適性應用程式控制 (AAC)** - 自適性應用程式控制是智慧型且自動化的解決方案，可為您的機器定義已知安全應用程式的允許清單。

    已啟用並設定自適性應用程式控制時，如果有任何您未定義於安全清單中的應用程式執行，您就會收到安全性警示。 [深入了解](security-center-adaptive-application.md)。

- **自適性網路強化 (ANH)**  - 套用網路安全性群組 (NSG) 以篩選資源的出入流量，從而改善網路安全性態勢。 但在某些情況下，透過 NSG 傳輸的實際流量仍有可能包含在已定義的 NSG 規則中。 在這些情況下，您可以根據實際的流量模式強化 NSG 規則，以進一步改善安全性態勢。

    自適性網路強化會提供進一步強化 NSG 規則的建議。 此功能會使用將實際流量、已知的信任設定、威脅情報和其他危害指標等因素納入考量的機器學習演算法，然後提供建議，而僅允許來自特定 IP/連接埠元組的流量。 [深入了解](security-center-adaptive-network-hardening.md)。

- **Docker 主機強化** - Azure 資訊安全中心可識別 IaaS Linux VM 上裝載的非受控容器，或其他執行 Docker 容器的 Linux 機器。 資訊安全中心會持續評估這些容器的設定。 然後與網際網路安全性 (CIS) Docker 基準測試中心進行比較。 資訊安全中心包含 CIS Docker 基準測試的整個規則集，並會在您的容器無法滿足任何控制項時發出警示。 [深入了解](harden-docker-hosts.md)。

- **無檔案攻擊偵測 (僅限 Windows)** - 無檔案攻擊會將惡意承載插入記憶體中，以規避磁碟型掃描技術的偵測。 攻擊者承載會保存在遭入侵之程序的記憶體中，並執行各式各樣的惡意活動。

  透過無檔案攻擊偵測，自動化記憶體鑑識技術可識別無檔案攻擊的工具組、技術和行為。 此解決方案會在執行階段定期掃描您的機器，並直接從程序的記憶體中擷取深入解析。 特定深入解析包括下列各項的識別： 

  - 知名的工具組和密碼編譯採礦軟體 

  - Shellcode 是一小段程式碼，通常用來作為惡意探索軟體弱點的承載。

  - 在程序記憶體中插入惡意可執行檔

  無檔案攻擊偵測會產生詳細的安全性警示，內含描述與其他程序中繼資料 (例如網路活動)。 這可以加速警示分級、相互關聯和下游回應時間。 此方法可補強以事件為基礎的 EDR 解決方案，並提供更大的偵測涵蓋範圍。

  如需無檔案攻擊偵測警示的詳細資訊，請參閱[警示的參考資料表](alerts-reference.md#alerts-windows)。

- **Linux auditd 警示與 Log Analytics 代理程式的整合 (僅限 Linux)** - auditd 系統由核心層級子系統所組成，負責監視系統呼叫。 此系統會依指定的規則集篩選這些呼叫，並將其訊息寫入至通訊端。 資訊安全中心會整合 Log Analytics 代理程式內的 auditd 套件所包含的功能。 這項整合可讓您在所有支援的 Linux 發行版本中收集 auditd 事件，而不需要任何必要條件。

    auditd 記錄在收集之後，會使用適用於 Linux 的 Log Analytics 代理程式進行擴充，並彙總至事件中。 資訊安全中心會持續新增新的分析，使用 Linux 信號偵測雲端和內部部署 Linux 機器上的惡意行為。 與 Windows 功能類似，這些分析的執行範圍遍及可疑的程序、可疑的登入嘗試、核心模組載入和其他活動。 出現這些活動時，表示機器可能受到攻擊或已遭入侵。  

    如需 Linux 警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-linux)。


## <a name="simulating-alerts"></a>模擬警示

您可以下載下列其中一個劇本以模擬警示：

- 若為 Windows：[Azure 資訊安全中心劇本：安全性警示](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Security%20Alerts%20Playbook_v2.pdf)

- 若為 Linux：[Azure 資訊安全中心劇本：Linux 偵測](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Linux%20Detections_v2.pdf)。




## <a name="next-steps"></a>後續步驟

在本文中，您已了解適用於伺服器的 Azure Defender。 

如需相關內容，請參閱下列文章： 

- 無論是由資訊安全中心產生警示，還是由不同的安全性產品資訊安全中心接收的警示，您都可以將其匯出。 若要將您的警示匯出至 Azure Sentinel、任何第三方 SIEM 或任何其他外部工具，請依照[將警示匯出至 SIEM](continuous-export.md) 中的指示操作。

- > [!div class="nextstepaction"]
    > [啟用 Azure Defender](security-center-pricing.md#enable-azure-defender)