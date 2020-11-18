---
title: Windows 虛擬桌面安全性最佳作法-Azure
description: 讓 Windows 虛擬桌面環境保持安全的最佳作法。
author: heidilohr
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: dc39c1e43580c5cf302e85664783dba4509889af
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842629"
---
# <a name="security-best-practices"></a>安全性最佳做法

Windows 虛擬桌面是受管理的虛擬桌面服務，其中包含許多安全性功能，可讓您的組織保持安全。 在 Windows 虛擬桌面部署中，Microsoft 會代表客戶管理部分服務。 此服務有許多內建的 advanced security 功能，例如反向連接，可減少從任何地方存取遠端桌面時所牽涉到的風險。

本文說明您可以採取的其他步驟，以系統管理員身分，讓客戶的 Windows 虛擬桌面部署保持安全。

## <a name="security-responsibilities"></a>安全性責任

雲端服務與傳統內部部署虛擬桌面基礎結構有何不同 (VDIs) 是它們如何處理安全性責任。 例如，在傳統的內部部署 VDI 中，客戶會負責安全性的所有層面。 不過，在大部分的雲端服務中，這些責任會在客戶與公司之間共用。

當您使用 Windows 虛擬桌面時，請務必瞭解，雖然某些元件已針對您的環境受到保護，但您必須自行設定其他區域，以符合組織的安全性需求。

以下是您要在 Windows 虛擬桌面部署中負責的安全性需求：

| 安全性需求 | 客戶負責這項作業嗎？ |
|---------------|:-------------------------:|
|身分識別|是|
| (行動裝置和電腦) 的使用者裝置|是|
|應用程式安全性|是|
|工作階段主機 OS|是|
|部署設定|是|
|網路控制措施|是|
|虛擬化控制平面|否|
|實體主機|否|
|實體網路|否|
|實體資料中心|否|

客戶不負責的安全性需求由 Microsoft 處理。

## <a name="azure-security-best-practices"></a>Azure 安全性最佳做法

Windows 虛擬桌面是 Azure 下的一項服務。 若要將 Windows 虛擬桌面部署的安全性最大化，您也應該確保您也可以保護周圍的 Azure 基礎結構和管理平面。 若要保護您的基礎結構，請考慮 Windows 虛擬桌面如何融入您較大的 Azure 生態系統。 若要深入瞭解 Azure 生態系統，請參閱 [azure 安全性最佳作法和模式](../security/fundamentals/best-practices-and-patterns.md)。

本節說明保護 Azure 生態系統的最佳做法。

### <a name="enable-azure-security-center"></a>啟用 Azure 資訊安全中心

建議您啟用訂用帳戶、虛擬機器、金鑰保存庫和儲存體帳戶的 Azure 資訊安全中心標準。

使用 Azure 資訊安全中心 Standard 時，您可以：

* 管理弱點。
* 使用 PCI 之類的通用架構來評估合規性。
* 強化環境的整體安全性。

若要深入瞭解，請參閱將 [您的 Azure 訂用帳戶上架到資訊安全中心標準](../security-center/security-center-get-started.md)。

### <a name="improve-your-secure-score"></a>改善您的安全分數

安全分數提供建議和最佳做法建議，以改善整體安全性。 這些建議的優先順序是為了協助您挑選最重要的建議，而快速修正選項可協助您快速解決潛在的弱點。 這些建議也會隨著時間而更新，讓您保持最新狀態，以維護環境的安全性。 若要深入瞭解，請參閱 [Azure 資訊安全中心中的增強您的安全分數](../security-center/security-center-secure-score.md)。

## <a name="windows-virtual-desktop-security-best-practices"></a>Windows 虛擬桌面安全性最佳作法

Windows 虛擬桌面有許多內建的安全性控制項。 在本節中，您將瞭解可用來讓使用者和資料保持安全的安全性控制項。

### <a name="require-multi-factor-authentication"></a>需要多重要素驗證

Windows 虛擬桌面中的所有使用者和系統管理員需要多重要素驗證，可改善整個部署的安全性。 若要深入瞭解，請參閱 [啟用 Windows 虛擬桌面的 Azure AD Multi-Factor Authentication](set-up-mfa.md)。

### <a name="enable-conditional-access"></a>啟用條件式存取

啟用 [條件式存取](../active-directory/conditional-access/best-practices.md) 可讓您在將 Windows 虛擬桌面環境的存取權授與使用者之前，先管理風險。 當您決定要授與存取權的使用者時，建議您也考慮使用者是誰、登入方式，以及他們使用的裝置。

### <a name="collect-audit-logs"></a>收集稽核記錄

啟用「audit 記錄檔收集」可讓您查看與 Windows 虛擬桌面相關的使用者和系統管理活動。 以下是一些重要的審核記錄範例：

-   [Azure 活動記錄](../azure-monitor/platform/activity-log-collect.md)
-   [Azure Active Directory 活動記錄](../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md)
-   [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)
-   [工作階段主機](../azure-monitor/platform/agent-windows.md)
-   [Windows 虛擬桌面診斷記錄](../virtual-desktop/diagnostics-log-analytics.md)
-   [Key Vault 記錄](../key-vault/general/logging.md)

### <a name="use-remoteapps"></a>使用 Remoteapp

選擇部署模型時，您可以為遠端使用者提供整個虛擬桌面的存取權，或只選取應用程式。 遠端應用程式或 Remoteapp，可讓使用者在其虛擬桌面電腦上使用應用程式時提供順暢的體驗。 Remoteapp 只讓使用者使用應用程式所公開的一部分遠端電腦，來降低風險。

### <a name="monitor-usage-with-azure-monitor"></a>使用 Azure 監視器監視使用方式

使用 [Azure 監視器](https://azure.microsoft.com/services/monitor/)監視您的 Windows 虛擬桌面服務使用狀況和可用性。 請考慮建立 Windows 虛擬桌面服務的 [服務健康狀態警示](../service-health/alerts-activity-log-service-notifications.md) ，以便在有服務影響事件時收到通知。

## <a name="session-host-security-best-practices"></a>工作階段主機安全性最佳作法

工作階段主機是在 Azure 訂用帳戶和虛擬網路內部執行的虛擬機器。 您的 Windows 虛擬桌面部署的整體安全性，取決於您放在工作階段主機上的安全性控制項。 本節說明讓您的工作階段主機保持安全的最佳做法。

### <a name="enable-endpoint-protection"></a>啟用 endpoint protection

若要保護您的部署免于已知的惡意軟體，建議您在所有的工作階段主機上啟用 endpoint protection。 您可以使用 Windows Defender 防毒軟體或協力廠商程式。 若要深入瞭解，請參閱 [VDI 環境中 Windows Defender 防毒軟體的部署指南](/windows/security/threat-protection/windows-defender-antivirus/deployment-vdi-windows-defender-antivirus)。

針對 FSLogix 或其他掛接 VHD 檔案的解決方案等設定檔解決方案，建議您不要將 VHD 副檔名排除。

### <a name="install-an-endpoint-detection-and-response-product"></a>安裝端點偵測和回應產品

建議您安裝端點偵測和回應 (EDR) 產品，以提供先進的偵測和回應功能。 若為已啟用 [Azure 資訊安全中心](../security-center/security-center-services.md) 的伺服器作業系統，安裝 EDR 產品將會部署 Defender ATP。 針對用戶端作業系統，您可以將 [DEFENDER ATP](/windows/security/threat-protection/microsoft-defender-atp/onboarding) 或協力廠商產品部署至這些端點。

### <a name="enable-threat-and-vulnerability-management-assessments"></a>啟用威脅和弱點管理評定

找出存在於作業系統和應用程式中的軟體弱點，對於讓您的環境保持安全相當重要。 Azure 資訊安全中心可透過伺服器作業系統的弱點評定，協助您找出問題點。 您也可以使用 Defender ATP，以提供桌上型電腦作業系統的威脅和弱點管理。 如果您想要使用協力廠商產品，也可以使用協力廠商產品，不過我們建議使用 Azure 資訊安全中心和 Defender ATP。

### <a name="patch-software-vulnerabilities-in-your-environment"></a>修補環境中的軟體弱點

一旦您找出弱點，就必須加以修補。 這也適用于虛擬環境，其中包括執行中的作業系統、在其中部署的應用程式，以及您用來建立新機器的映射。 遵循廠商修補通知通訊並及時套用修補程式。 建議您每月修補您的基礎映射，以確保新部署的機器盡可能安全。

### <a name="establish-maximum-inactive-time-and-disconnection-policies"></a>建立最大非使用中時間和中斷連接原則

當使用者處於非作用中狀態時，請將它們登出，以防止未經授權的使用者存取。 我們建議您將使用者生產力和資源使用方式平衡。 針對與無狀態應用程式互動的使用者，請考慮更積極的原則，以關閉電腦並保留資源。 中斷連接長時間執行的應用程式，如果使用者閒置，例如模擬或 CAD 轉譯，則可以中斷使用者的工作，甚至可能需要重新開機電腦。

### <a name="set-up-screen-locks-for-idle-sessions"></a>設定閒置會話的螢幕鎖定

您可以設定 Windows 虛擬桌面在閒置時間鎖定電腦的畫面，並要求驗證解除鎖定，以防止不必要的系統存取。

### <a name="establish-tiered-admin-access"></a>建立分層式系統管理員存取權

建議您不要將虛擬桌面的系統管理員存取權授與您的使用者。 如果您需要軟體套件，建議您透過 Microsoft 端點管理員等設定管理公用程式來提供它們。 在多會話環境中，建議您不要讓使用者直接安裝軟體。

### <a name="consider-which-users-should-access-which-resources"></a>考慮哪些使用者應該存取哪些資源

請將工作階段主機視為現有桌面部署的擴充功能。 建議您以您在環境中其他桌上型電腦的相同方式來控制對網路資源的存取，例如使用網路分割和篩選。 根據預設，工作階段主機可以連接到網際網路上的任何資源。 您可以使用數種方式來限制流量，包括使用 Azure 防火牆、網路虛擬裝置或 proxy。 如果您需要限制流量，請務必新增適當的規則，讓 Windows 虛擬桌面能夠正常運作。

### <a name="manage-office-pro-plus-security"></a>管理 Office Pro Plus security

除了保護您的工作階段主機，也請務必保護在其中執行的應用程式。 Office Pro Plus 是在工作階段主機中部署的其中一個最常見的應用程式。 若要改善 Office 部署安全性，建議您將 [安全性原則](/DeployOffice/overview-of-security-policy-advisor) 建議程式用於適用于企業的 Microsoft 365 應用程式。 此工具會識別可套用至您的部署以提高安全性的原則。 安全性原則顧問也會根據安全性和生產力的影響來建議原則。

### <a name="other-security-tips-for-session-hosts"></a>工作階段主機的其他安全性秘訣

藉由限制作業系統功能，您可以加強工作階段主機的安全性。 以下是您可以執行的幾項作業：

- 在遠端桌面會話中，將磁片磁碟機、印表機和 USB 裝置重新導向至使用者的本機裝置，以控制裝置重新導向。 建議您評估您的安全性需求，並檢查是否應該停用這些功能。

- 藉由隱藏本機和遠端磁片磁碟機對應來限制 Windows 檔案總管存取。 這可防止使用者探索有關系統組態和使用者的不必要資訊。

- 避免直接對您環境中的工作階段主機進行 RDP 存取。 如果您需要直接 RDP 存取以進行系統管理或疑難排解，請啟用 [即時](../security-center/security-center-just-in-time.md) 存取，以限制工作階段主機上的潛在攻擊面。

- 存取本機和遠端檔案系統時，授與使用者有限的許可權。 您可以藉由確定本機和遠端檔案系統使用具有最低許可權的存取控制清單來限制許可權。 如此一來，使用者只能存取所需的內容，而且無法變更或刪除重要的資源。

- 防止不想要的軟體在工作階段主機上執行。 您可以啟用應用程式保險箱以提供工作階段主機上的額外安全性，以確保只有您允許的應用程式可在主機上執行。

## <a name="next-steps"></a>後續步驟

若要瞭解如何啟用多重要素驗證，請參閱 [設定多重要素驗證](set-up-mfa.md)。