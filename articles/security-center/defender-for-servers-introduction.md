---
title: 適用于伺服器的 Azure Defender-優點和功能
description: 瞭解適用于伺服器的 Azure Defender 的優點和功能。
author: memildin
ms.author: memildin
ms.date: 9/23/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: d0e75ab8a2e4d853fa2edc66174502dd3a7930e9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301664"
---
# <a name="introduction-to-azure-defender-for-servers"></a>適用于伺服器的 Azure Defender 簡介

適用于伺服器的 Azure Defender 會為您的 Windows 和 Linux 電腦新增威脅偵測和先進的防禦功能。

針對 Windows，Azure Defender 會與 Azure 服務整合，以監視及保護您的 Windows 電腦。 資訊安全中心會以便於使用的格式，提供來自這些服務的警示和補救建議。

針對 Linux，Azure Defender 會使用 **auditd**（最常見的 linux 審核架構之一），從 linux 機器收集審核記錄。 auditd 位於主線核心中。 


## <a name="what-are-the-benefits-of-azure-defender-for-servers"></a>適用于伺服器的 Azure Defender 有哪些優點？

適用于伺服器的 Azure Defender 所提供的威脅偵測和保護功能包括：

- **Vm 的弱點評定掃描** -Azure 資訊安全中心隨附的弱點掃描程式是由 Qualys 提供技術支援。 

    Qualys 的掃描器是在 Azure 虛擬機器中即時識別弱點的其中一個領先的工具。 您不需要 Qualys 授權或 Qualys 帳戶，一切都可以在資訊安全中心內流暢進行。 [深入了解](deploy-vulnerability-assessment-vm.md)。

- **及時 (JIT) VM 存取**  -威脅執行者主動利用開啟的管理埠（例如 RDP 或 SSH）來搜尋可存取的機器。 您的所有虛擬機器都是攻擊的潛在目標。 當 VM 成功遭到入侵時，它會作為進入點，以在您的環境中攻擊進一步的資源。

    當您為伺服器啟用 Azure Defender 時，您可以使用即時 VM 存取來鎖定 Vm 的輸入流量、降低暴露于攻擊的風險，同時在需要時提供簡單的存取權來連線至 Vm。 [深入了解](just-in-time-explained.md)。

- 檔案**完整性監視 (fim) **檔案完整性監視 (fim) （也稱為變更監視）會檢查作業系統、應用程式軟體的檔案和登錄，以及其他可能表示攻擊的變更。 它使用比較方法來判斷檔案的目前狀態是否不同於上次掃描的檔案。 您可以利用這項比較，來判斷對您的檔案所做的修改為有效或可疑。

    當您為伺服器啟用 Azure Defender 時，您可以使用 FIM 來驗證 Windows 檔案、Windows 登錄和 Linux 檔案的完整性。 [深入了解](security-center-file-integrity-monitoring.md)。

- 自動調整**應用程式控制 (AAC) **自我調整應用程式控制項，是一種智慧型和自動化的解決方案，可為您的機器定義已知安全應用程式的允許清單。

    當您已啟用並設定彈性應用程式控制之後，如果任何應用程式執行的應用程式不是您定義為安全的應用程式，您將會收到安全性警示。 [深入了解](security-center-adaptive-network-hardening.md)。

- 自動調整**網路強化 (ANH) ** -套用網路安全性群組 (NSG) ，以篩選進出資源的流量，進而改善您的網路安全性狀態。 不過，在某些情況下，實際流經 NSG 的流量是所定義 NSG 規則的子集。 在這些情況下，您可以根據實際的流量模式強化 NSG 規則，以進一步改善安全性狀態。

    彈性網路強化可提供建議，以進一步強化 NSG 規則。 它會使用機器學習演算法，以實際的流量、已知的受信任設定、威脅情報和其他入侵指標作為因素，然後提供建議，只允許來自特定 IP/埠元組的流量。 [深入了解](security-center-adaptive-network-hardening.md)。

- **與 Microsoft Defender 進階威脅防護 (atp) 的整合僅限 (Windows) ** -Azure Defender 會與 MICROSOFT DEFENDER 進階威脅防護 (ATP) 整合。 兩者搭配運作下，可提供完整的端點偵測和回應 (EDR) 功能。 [深入了解](security-center-wdatp.md)。

    > [!IMPORTANT]
    > 在使用資訊安全中心的 Windows 伺服器上，會自動啟用 Microsoft Defender ATP 感應器。

    Microsoft Defender ATP 在偵測到威脅時，會觸發警示。 警示會顯示在 [安全性中心] 中。 您也可以從資訊安全中心切換至 Microsoft Defender ATP 主控台，並執行詳細的調查，以找出攻擊的範圍。 如需 Microsoft Defender ATP 的詳細資訊，請參閱[將伺服器上線至 Microsoft Defender ATP 服務](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)。

- **Docker 主機強化** -Azure 資訊安全中心識別裝載于 IaaS Linux vm 上的非受控容器，或執行 Docker 容器的其他 Linux 機器。 安全性中心會持續評估這些容器的設定。 然後，它會將它們與網際網路安全性 (CIS) Docker 基準測試的中心進行比較。 如果您的容器無法滿足任何控制項，則 [安全性中心] 會包含 CIS Docker 基準測試的完整規則集，併發出警示。 [深入了解](harden-docker-hosts.md)。

- **無檔案攻擊偵測 (僅限 Windows) ** 無檔案攻擊將惡意承載插入記憶體中，以避免以磁片為基礎的掃描技術偵測。 攻擊者的承載接著會在遭入侵的進程記憶體中保存，並執行各式各樣的惡意活動。

  透過無檔案攻擊偵測，自動化記憶體鑑識技術可識別無檔案攻擊的工具組、技術和行為。 此解決方案會在執行時間定期掃描您的機器，並直接從進程的記憶體中將見解解壓縮。 Linux 的特定深入解析包含下列各項的識別： 

  - 知名的工具組和加密的挖掘軟體 

  - 程式碼外殼，這是一小段程式碼，通常是用來做為惡意探索軟體弱點的弱點。

  - 在進程記憶體中插入惡意可執行檔

  無檔案攻擊偵測會產生包含其他進程中繼資料（例如網路活動）描述的詳細安全性警示。 這會加速警示分級、相互關聯和下游回應時間。 這種方法可補充以事件為基礎的 EDR 解決方案，並提供更高的偵測涵蓋範圍。

  如需無檔案攻擊偵測警示的詳細資訊，請參閱[警示的參考資料表](alerts-reference.md#alerts-windows)。

- **Linux auditd 警示和 Log Analytics 代理程式整合僅 (linux) ** -auditd 系統是由負責監視系統呼叫的核心層級子系統所組成。 此系統會依指定的規則集篩選這些呼叫，並將其訊息寫入至通訊端。 資訊安全中心會整合 Log Analytics 代理程式內的 auditd 套件所包含的功能。 這項整合可讓您在所有支援的 Linux 發行版本中收集 auditd 事件，而不需要任何必要條件。

    auditd 記錄在收集之後，會使用適用於 Linux 的 Log Analytics 代理程式進行擴充，並彙總至事件中。 資訊安全中心會持續新增新的分析，使用 Linux 信號偵測雲端和內部部署 Linux 機器上的惡意行為。 與 Windows 功能類似，這些分析的執行範圍遍及可疑的程序、可疑的登入嘗試、核心模組載入和其他活動。 出現這些活動時，表示機器可能受到攻擊或已遭入侵。  

    如需 Linux 警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-linux)。


## <a name="simulating-alerts"></a>模擬警示

您可以下載下列其中一個操作手冊來模擬警示：

- 若為 Windows： [Azure 資訊安全中心腳本：安全性警示](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Security%20Alerts%20Playbook_v2.pdf)

- 針對 Linux： [Azure 資訊安全中心腳本： linux](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Linux%20Detections_v2.pdf)偵測。




## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解適用于伺服器的 Azure Defender。 

如需相關內容，請參閱下列文章： 

- 無論是由資訊安全中心產生警示，還是由不同的安全性產品資訊安全中心接收的警示，您都可以將其匯出。 若要將您的警示匯出至 Azure Sentinel、任何第三方 SIEM 或任何其他外部工具，請依照[將警示匯出至 SIEM](continuous-export.md) 中的指示操作。

- > [!div class="nextstepaction"]
    > [啟用 Azure Defender](security-center-pricing.md)