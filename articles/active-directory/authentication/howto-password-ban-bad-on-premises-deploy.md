---
title: 部署內部部署 Azure AD 密碼保護
description: 瞭解如何在內部部署 Active Directory Domain Services 環境中規劃和部署 Azure AD 的密碼保護
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: b773fb887d3663a2af2e340912e378c7fccaba4a
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89003536"
---
# <a name="plan-and-deploy-on-premises-azure-active-directory-password-protection"></a>規劃和部署內部部署 Azure Active Directory 密碼保護

使用者通常會建立使用常見當地字組 (例如學校、運動團隊或知名人士) 的密碼。 這些密碼很容易猜測，且容易遭受字典型攻擊。 若要在您的組織中強制執行強式密碼，Azure Active Directory (Azure AD) 密碼保護提供全域和自訂禁用密碼清單。 如果這些禁用密碼清單中有相符的密碼，密碼變更要求就會失敗。

若要保護您的內部部署 Active Directory Domain Services (AD DS) 環境中，您可以安裝和設定 Azure AD 密碼保護，以使用內部部署的 DC。 本文說明如何在內部部署環境中安裝及註冊 Azure AD 的密碼保護 proxy 服務和 Azure AD 密碼保護 DC 代理程式。

如需有關如何在內部部署環境中 Azure AD 密碼保護的詳細資訊，請參閱 [如何強制執行 Windows Server Azure AD 的密碼保護 Active Directory](concept-password-ban-bad-on-premises.md)。

## <a name="deployment-strategy"></a>部署策略

下圖顯示在內部部署 Active Directory 環境中，Azure AD 密碼保護的基本元件如何一起運作：

![Azure AD 密碼保護元件如何一起運作](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

在您部署軟體之前，最好先複習一下軟體的運作方式。 如需詳細資訊，請參閱 [Azure AD 密碼保護的概念總覽](concept-password-ban-bad-on-premises.md)。

建議您在 *audit* 模式中開始部署。 Audit 模式是預設的初始設定，可繼續設定密碼。 將封鎖的密碼會記錄在事件記錄檔中。 在稽核模式中部署 proxy 伺服器和 DC 代理程式之後，請監視密碼原則在強制執行原則時對使用者的影響。

在審核階段，許多組織都發現下列情況適用：

* 它們必須改善現有的作業程序，才能使用更安全的密碼。
* 使用者通常會使用不安全的密碼。
* 他們需要告知使用者即將進行的安全性強制變更、可能的影響，以及如何選擇更安全的密碼。

您也可以進行更強的密碼驗證，以影響現有的 Active Directory 網域控制站部署自動化。 建議您至少進行一個 DC 升級，並在審核期間評估期間進行一個 DC 降級，以協助找出這類問題。 如需詳細資訊，請參閱下列文章：

* [Ntdsutil.exe 無法設定弱式目錄服務修復模式密碼](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [網域控制站複本升級失敗，因為有弱式目錄服務修復模式密碼](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [由於本機系統管理員密碼弱，導致網域控制站降級失敗](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

當功能在 audit 模式中以合理的時間執行之後，您可以從 *audit* 切換設定，以 *強制* 要求更安全的密碼。 在這段時間內的其他監視是個不錯的主意。

請務必注意，Azure AD 密碼保護只能在密碼變更或設定作業期間驗證密碼。 部署 Azure AD 密碼保護之前，在 Active Directory 中接受和儲存的密碼永遠不會經過驗證，而且會繼續依原樣繼續工作。 經過一段時間之後，所有使用者和帳戶最終都會開始使用 Azure AD 密碼保護驗證的密碼，因為其現有密碼會正常過期。 使用「密碼永久有效」設定的帳戶會豁免。

### <a name="multiple-forest-considerations"></a>多個樹系考慮

沒有任何要在多個樹系部署 Azure AD 密碼保護的額外需求。

每個樹系都有個別設定，如下一節所述，可 [部署內部內部部署 Azure AD 密碼保護](#download-required-software)。 每個 Azure AD 密碼保護 proxy 都只能支援其所加入之樹系中的網域控制站。

無論 Active Directory 信任設定為何，任何樹系中的 Azure AD 密碼保護軟體都不會察覺部署在其他樹系中的密碼保護軟體。

### <a name="read-only-domain-controller-considerations"></a>唯讀網域控制站考慮

密碼變更或設定事件不會在唯讀網域控制站上處理和保存 (Rodc) 。 相反地，它們會轉送到可寫入的網域控制站。 您不需要在 Rodc 上安裝 Azure AD 的密碼保護 DC 代理程式軟體。

此外，也不支援在唯讀網域控制站上執行 Azure AD 密碼保護 proxy 服務。

### <a name="high-availability-considerations"></a>高可用性考慮

當樹系中的 Dc 嘗試從 Azure 下載新原則或其他資料時，密碼保護的主要考慮是 Azure AD 密碼保護 proxy 伺服器的可用性。 每個 Azure AD 密碼保護 DC 代理程式都會使用簡單的迴圈配置資源演算法，以決定要呼叫的 proxy 伺服器。 代理程式會略過沒有回應的 proxy 伺服器。

對於大部分完全連線的 Active Directory 部署，其目錄和 sysvol 資料夾狀態的狀況良好複寫，兩個 Azure AD 的密碼保護 proxy 伺服器足以確保可用性。 這項設定會導致及時下載新的原則和其他資料。 如有需要，您可以部署額外的 Azure AD 密碼保護 proxy 伺服器。

Azure AD 密碼保護 DC 代理程式軟體的設計可減少與高可用性相關的一般問題。 Azure AD 密碼保護 DC 代理程式會維護最近下載之密碼原則的本機快取。 即使所有已註冊的 proxy 伺服器變得無法使用，Azure AD 的密碼保護 DC 代理程式仍會繼續強制執行其快取的密碼原則。

大型部署中的密碼原則合理的更新頻率通常是數天，而不是數小時或更少。 因此，proxy 伺服器的短暫中斷並不會大幅影響 Azure AD 密碼保護。

## <a name="deployment-requirements"></a>部署需求

如需授權的詳細資訊，請參閱 [Azure AD 密碼保護授權需求](concept-password-ban-bad.md#license-requirements)。

適用下列核心需求：

* 已安裝 Azure AD 密碼保護元件的所有機器（包括網域控制站）都必須安裝通用 C 執行時間。
    * 您可以藉由確定您有來自 Windows Update 的所有更新，來取得執行時間。 或者，您可以在 OS 特定的更新套件中取得它。 如需詳細資訊，請參閱 [Windows 中的通用 C 執行時間更新](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows)。
* 您需要在樹系根域中具有 Active Directory 網域系統管理員許可權的帳戶，才能向 Azure AD 註冊 Windows Server Active Directory 樹系。
* 您必須在執行 Windows Server 2012 的網域中的所有網域控制站上啟用金鑰發佈服務。 根據預設，此服務會透過手動觸發程式啟動來啟用。
* 每個網域中至少要有一個網域控制站與一個裝載 proxy 服務的伺服器之間必須有網路連線，才能 Azure AD 密碼保護。 此連線必須允許網域控制站存取 proxy 服務上的 RPC 端點對應程式埠135和 RPC 伺服器埠。
    * 根據預設，RPC 伺服器埠是動態 RPC 埠，但可以設定為 [使用靜態埠](#static)。
* 將安裝 Azure AD 密碼保護 Proxy 服務的所有電腦都必須具備下列端點的網路存取權：

    |**端點**|**目的**|
    | --- | --- |
    |`https://login.microsoftonline.com`|驗證要求|
    |`https://enterpriseregistration.windows.net`|Azure AD 密碼保護功能|

### <a name="azure-ad-password-protection-dc-agent"></a>Azure AD 密碼保護 DC 代理程式

下列需求適用于 Azure AD 密碼保護 DC 代理程式：

* 將安裝 Azure AD 密碼保護 DC 代理程式軟體的所有電腦都必須執行 Windows Server 2012 或更新版本。
    * Active Directory 網域或樹系不需要位於 Windows Server 2012 網域功能等級 (DFL) 或樹系功能等級 (FFL) 。 如同在 [設計原則](concept-password-ban-bad-on-premises.md#design-principles)中所述，執行 DC 代理程式或 proxy 軟體沒有必要的最小 DFL 或 FFL。
* 所有執行 Azure AD 密碼保護 DC 代理程式的電腦都必須安裝 .NET 4.5。
* 任何執行 Azure AD 密碼保護 DC 代理程式服務的 Active Directory 網域，都必須使用分散式檔案系統 Replication (DFSR) 進行 sysvol 複寫。
   * 如果您的網域尚未使用 DFSR，您必須先遷移，才能安裝 Azure AD 密碼保護。 如需詳細資訊，請參閱 [SYSVOL 複寫遷移指南： FRS 至 DFS 複寫](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

    > [!WARNING]
    > Azure AD 的密碼保護 DC 代理程式軟體目前會安裝在仍使用 FRS 之網域中的網域控制站上 (前身為 DFSR) 以進行 sysvol 複寫，但軟體在此環境中將無法正常運作。
    >
    > 其他負面副作用包括無法複寫的個別檔案，而且 sysvol 還原程式似乎成功，但無法以無訊息方式複寫所有檔案。
    >
    > 將您的網域遷移至儘快使用 DFSR，這兩者適用于 DFSR 的固有優點，以及解除封鎖 Azure AD 密碼保護的部署。 軟體的未來版本將會在以仍在使用 FRS 的網域中執行時自動停用。

### <a name="azure-ad-password-protection-proxy-service"></a>Azure AD 密碼保護 proxy 服務

下列需求適用于 Azure AD 密碼保護 proxy 服務：

* 將安裝 Azure AD 密碼保護 proxy 服務的所有電腦都必須執行 Windows Server 2012 R2 或更新版本。

    > [!NOTE]
    > 即使網域控制站可能具有連出的直接網際網路連線，Azure AD 密碼保護 proxy 服務部署也是部署 Azure AD 密碼保護的必要需求。

* 將安裝 Azure AD 密碼保護 proxy 服務的所有電腦都必須安裝 .NET 4.7。
    * .NET 4.7 應該已經安裝在完整更新的 Windows Server 上。 如有必要，請下載並執行 [Windows 的 .NET Framework 4.7 離線安裝](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows)程式中找到的安裝程式。
* 裝載 Azure AD 密碼保護 proxy 服務的所有機器都必須設定為授與網域控制站登入 proxy 服務的能力。 這項功能是透過「從網路存取這台電腦」許可權指派來控制。
* 裝載 Azure AD 密碼保護 proxy 服務的所有機器都必須設定為允許輸出 TLS 1.2 HTTP 流量。
* *全域系統管理員*帳戶，用來向 Azure AD 註冊 Azure AD 的密碼保護 proxy 服務和樹系。
* 必須針對 [應用程式 Proxy 環境安裝](../manage-apps/application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)程式中指定的一組埠和 url 啟用網路存取。

### <a name="microsoft-azure-ad-connect-agent-updater-prerequisites"></a>Microsoft Azure AD Connect 代理程式更新程式的必要條件

Microsoft Azure AD Connect 代理程式更新程式服務會與 Azure AD 密碼保護 Proxy 服務並存安裝。 需要進行其他設定，才能讓 Microsoft Azure AD Connect 代理程式更新程式服務運作：

* 如果您的環境使用 HTTP proxy 伺服器，請遵循使用 [現有的內部部署 proxy 伺服器](../manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)所指定的指導方針。
* Microsoft Azure AD Connect 代理程式更新程式服務也需要在 [tls 需求](../manage-apps/application-proxy-add-on-premises-application.md#tls-requirements)中指定 tls 1.2 步驟。

> [!WARNING]
> Azure AD 密碼保護 proxy 和 Azure AD 應用程式 Proxy 會安裝不同版本的 Microsoft Azure AD Connect 代理程式更新程式服務，這也是指示參考應用程式 Proxy 內容的原因。 當並存安裝時，這些不同的版本不相容，因此會導致代理程式更新程式服務無法與 Azure 聯繫以進行軟體更新，因此您永遠不應該在同一部電腦上安裝 Azure AD 的密碼保護 Proxy 和應用程式 Proxy。

## <a name="download-required-software"></a>下載必要的軟體

內部部署 Azure AD 密碼保護部署有兩個必要的安裝程式：

* Azure AD 密碼保護 DC 代理程式 (*AzureADPasswordProtectionDCAgentSetup.msi*) 
* Azure AD 密碼保護 proxy (*AzureADPasswordProtectionProxySetup.exe*) 

請從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=57071)下載這兩個安裝程式。

## <a name="install-and-configure-the-proxy-service"></a>安裝和設定 proxy 服務

Azure AD 的密碼保護 proxy 服務通常是在內部部署 AD DS 環境中的成員伺服器上。 安裝之後，Azure AD 的密碼保護 proxy 服務會與 Azure AD 通訊，以維護 Azure AD 租使用者的全域和客戶禁用密碼清單複本。

在下一節中，您會在內部部署 AD DS 環境中的網域控制站上安裝 Azure AD 的密碼保護 DC 代理程式。 這些 DC 代理程式會與 proxy 服務通訊，以取得在網域內處理密碼變更事件時使用的最新禁用密碼清單。

選擇一或多部伺服器來裝載 Azure AD 的密碼保護 proxy 服務。 下列考慮適用于伺服器 (s) ：

* 每個這類服務只能為單一樹系提供密碼原則。 主機電腦必須加入該樹系中的網域。 同時支援根和子域。 樹系的每個網域中至少要有一個 DC 和密碼保護電腦之間的網路連線能力。
* 您可以在網域控制站上執行 Azure AD 密碼保護 proxy 服務來進行測試，但是該網域控制站接著需要網際網路連線。 這種連線可能是安全性考慮。 建議您只進行測試。
* 我們建議至少有兩個 Azure AD 的密碼保護 proxy 伺服器來進行冗余，如先前的 [高可用性考慮](#high-availability-considerations)一節所述。
* 不支援在唯讀網域控制站上執行 Azure AD 密碼保護 proxy 服務。

若要安裝 Azure AD 密碼保護 proxy 服務，請完成下列步驟：

1. 若要安裝 Azure AD 密碼保護 proxy 服務，請執行 `AzureADPasswordProtectionProxySetup.exe` 軟體安裝程式。

    軟體安裝不需要重新開機，而且可以使用標準 MSI 程式來自動執行，如下列範例所示：
    
    ```console
    AzureADPasswordProtectionProxySetup.exe /quiet
    ```
    
    > [!NOTE]
    > 在安裝封裝之前，必須先執行 Windows 防火牆服務 `AzureADPasswordProtectionProxySetup.exe` ，以避免發生安裝錯誤。
    >
    > 如果 Windows 防火牆設定為不執行，則因應措施是在安裝期間暫時啟用和執行防火牆服務。 在安裝後，proxy 軟體對 Windows 防火牆沒有特定的相依性。
    >
    > 如果您使用的是協力廠商防火牆，仍然必須將它設定為符合部署需求。 這些包括允許埠135和 proxy RPC 伺服器埠的輸入存取。 如需詳細資訊，請參閱上一節的 [部署需求](#deployment-requirements)。

1. Azure AD 的密碼保護 proxy 套裝軟體含新的 PowerShell 模組 `AzureADPasswordProtection` 。 下列步驟會從這個 PowerShell 模組執行各種 Cmdlet。

    若要使用此模組，請以系統管理員身分開啟 PowerShell 視窗，然後匯入新的模組，如下所示：
    
    ```powershell
    Import-Module AzureADPasswordProtection
    ```

1. 若要檢查 Azure AD 的密碼保護 proxy 服務是否正在執行，請使用下列 PowerShell 命令：

    ```powershell
    Get-Service AzureADPasswordProtectionProxy | fl
    ```

    結果應該會顯示 [*正在*執行]**狀態**。

1. Proxy 服務正在電腦上執行，但沒有與 Azure AD 通訊的認證。 使用 Cmdlet 向 Azure AD 註冊 Azure AD 的密碼保護 proxy 伺服器 `Register-AzureADPasswordProtectionProxy` 。

    此 Cmdlet 需要您 Azure 租使用者的全域管理員認證。 您也需要樹系根域中的內部部署 Active Directory 網域系統管理員許可權。 您也必須使用具有本機系統管理員許可權的帳戶來執行此 Cmdlet：

    針對 Azure AD 密碼保護 proxy 服務成功執行一次此命令之後，其他的調用會成功，但不需要。

    此 `Register-AzureADPasswordProtectionProxy` Cmdlet 支援下列三種驗證模式。 前兩個模式支援 Azure Multi-Factor Authentication 但第三個模式則不支援。

    > [!TIP]
    > 第一次對特定 Azure 租使用者執行此 Cmdlet 時，可能會有明顯的延遲。 除非回報失敗，否則請不要擔心此延遲。

     * 互動式驗證模式：

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > 此模式不適用於 Server Core 作業系統。 相反地，請使用下列其中一種驗證模式。 此外，如果已啟用 Internet Explorer 增強式安全性設定，此模式可能會失敗。 解決方法是停用該設定、註冊 proxy，然後重新啟用它。

     * 裝置代碼驗證模式：

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        出現提示時，請遵循連結開啟網頁瀏覽器並輸入驗證碼。

     * 無訊息 (密碼型) 驗證模式：

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > 如果您的帳戶需要 Azure Multi-Factor Authentication，此模式將會失敗。 在此情況下，請使用前兩種驗證模式的其中一種，或改為使用不需要 MFA 的其他帳戶。
        >
        > 您也可能會看到在 Azure 裝置註冊 (中使用的 Azure AD 密碼保護) 已設定為全域要求 MFA 時所需的 MFA。 若要解決此需求，您可以使用與先前兩個驗證模式中的其中一個支援 MFA 的不同帳戶，或者您也可以暫時放寬 Azure 裝置註冊 MFA 需求。
        >
        > 若要進行這項變更，請在 Azure 入口網站中搜尋並選取 **Azure Active Directory** ，然後選取 [ **裝置] > 裝置設定**。 設定 [ **需要多重要素驗證] 將裝置加入** [ *否*]。 註冊完成之後，請務必將此設定重新設定為 *[是]* 。
        >
        > 建議您只針對測試目的略過 MFA 需求。

    您目前不需要指定 *-ForestCredential* 參數，這是保留供未來功能使用的參數。

    只有在服務的存留期內，才需要註冊 Azure AD 密碼保護 proxy 服務一次。 之後，Azure AD 的密碼保護 proxy 服務會自動執行任何其他必要的維護。

1. 現在，使用 PowerShell Cmdlet，向內部部署 Active Directory 樹系註冊必要的認證，以與 Azure 進行通訊 `Register-AzureADPasswordProtectionForest` 。

    > [!NOTE]
    > 如果您的環境中安裝了多個 Azure AD 的密碼保護 proxy 伺服器，則您用來註冊樹系的 proxy 伺服器並不重要。

    此 Cmdlet 需要您 Azure 租使用者的全域管理員認證。 您也必須使用具有本機系統管理員許可權的帳戶來執行此 Cmdlet。 它也需要內部部署 Active Directory 企業系統管理員許可權。 此步驟會針對每一樹系執行一次。

    此 `Register-AzureADPasswordProtectionForest` Cmdlet 支援下列三種驗證模式。 前兩個模式支援 Azure Multi-Factor Authentication 但第三個模式則不支援。

    > [!TIP]
    > 第一次對特定 Azure 租使用者執行此 Cmdlet 時，可能會有明顯的延遲。 除非回報失敗，否則請不要擔心此延遲。

     * 互動式驗證模式：

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > 此模式不適用於 Server Core 作業系統。 請改用下列其中一種驗證模式。 此外，如果已啟用 Internet Explorer 增強式安全性設定，此模式可能會失敗。 解決方法是停用該設定、註冊樹系，然後重新啟用它。  

     * 裝置代碼驗證模式：

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        出現提示時，請遵循連結開啟網頁瀏覽器並輸入驗證碼。

     * 無訊息 (密碼型) 驗證模式：

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > 如果您的帳戶需要 Azure Multi-Factor Authentication，此模式將會失敗。 在此情況下，請使用前兩種驗證模式的其中一種，或改為使用不需要 MFA 的其他帳戶。
        >
        > 您也可能會看到在 Azure 裝置註冊 (中使用的 Azure AD 密碼保護) 已設定為全域要求 MFA 時所需的 MFA。 若要解決此需求，您可以使用與先前兩個驗證模式中的其中一個支援 MFA 的不同帳戶，或者您也可以暫時放寬 Azure 裝置註冊 MFA 需求。
        >
        > 若要進行這項變更，請在 Azure 入口網站中搜尋並選取 **Azure Active Directory** ，然後選取 [ **裝置] > 裝置設定**。 設定 [ **需要多重要素驗證] 將裝置加入** [ *否*]。 註冊完成之後，請務必將此設定重新設定為 *[是]* 。
        >
        > 建議您只針對測試目的略過 MFA 需求。

       只有當目前登入的使用者也是根域的 Active Directory 網域系統管理員時，這些範例才會成功。 如果不是這種情況，您可以透過 *-ForestCredential* 參數提供替代的網域認證。

    只有在樹系的存留期內，才需要註冊 Active Directory 樹系。 之後，樹系中 Azure AD 的密碼保護 DC 代理程式就會自動執行任何其他必要的維護。 `Register-AzureADPasswordProtectionForest`成功執行樹系之後，Cmdlet 的額外調用會成功，但並非必要。
    
    `Register-AzureADPasswordProtectionForest`若要成功，Azure AD 密碼保護 proxy 伺服器的網域中至少必須有一個執行 Windows Server 2012 或更新版本的 DC。 Azure AD 的密碼保護 DC 代理程式軟體，在此步驟之前，不需要在任何網域控制站上安裝。

### <a name="configure-the-proxy-service-to-communicate-through-an-http-proxy"></a>將 proxy 服務設定為透過 HTTP proxy 進行通訊

如果您的環境需要使用特定的 HTTP proxy 來與 Azure 進行通訊，請使用下列步驟來設定 Azure AD 密碼保護服務。

在資料夾中建立 *AzureADPasswordProtectionProxy.exe.config* 檔案 `%ProgramFiles%\Azure AD Password Protection Proxy\Service` 。 包含下列內容：

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

如果您的 HTTP proxy 需要驗證，請新增 *useDefaultCredentials* 標記：

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true" useDefaultCredentials="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

在這兩種情況下，請 `http://yourhttpproxy.com:8080` 將取代為您特定 HTTP proxy 伺服器的位址和埠。

如果您的 HTTP proxy 設定為使用授權原則，您必須將存取權授與裝載 proxy 服務之電腦的 Active Directory 電腦帳戶，以進行密碼保護。

建議您在建立或更新 *AzureADPasswordProtectionProxy.exe.config* 檔案之後，停止並重新啟動 Azure AD 的密碼保護 proxy 服務。

Proxy 服務不支援使用特定認證來連接到 HTTP proxy。

### <a name="configure-the-proxy-service-to-listen-on-a-specific-port"></a>將 proxy 服務設定為在特定埠上接聽

Azure AD 的密碼保護 DC 代理程式軟體會使用 RPC over TCP 來與 proxy 服務通訊。 根據預設，Azure AD 的密碼保護 proxy 服務會在任何可用的動態 RPC 端點上接聽。 您可以根據環境中的網路拓撲或防火牆需求，將服務設定為在特定 TCP 通訊埠上接聽。

<a id="static" /></a>若要將服務設定為在靜態埠下執行，請使用 Cmdlet，如下所示 `Set-AzureADPasswordProtectionProxyConfiguration` ：

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
```

> [!WARNING]
> 您必須停止並重新啟動 Azure AD 的密碼保護 proxy 服務，這些變更才會生效。

若要將服務設定為以動態埠執行，請使用相同的程式，但將 *StaticPort* 設回零：

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
```

> [!WARNING]
> 您必須停止並重新啟動 Azure AD 的密碼保護 proxy 服務，這些變更才會生效。

Azure AD 的密碼保護 proxy 服務需要在任何埠設定變更後手動重新開機。 進行這些設定變更之後，您就不需要重新開機網域控制站上的 Azure AD 密碼保護 DC 代理程式服務。

若要查詢目前的服務設定，請使用此 `Get-AzureADPasswordProtectionProxyConfiguration` Cmdlet，如下列範例所示

```powershell
Get-AzureADPasswordProtectionProxyConfiguration | fl
```

下列範例輸出顯示 Azure AD 的密碼保護 proxy 服務使用動態埠：

```output
ServiceName : AzureADPasswordProtectionProxy
DisplayName : Azure AD password protection Proxy
StaticPort  : 0
```

## <a name="install-the-dc-agent-service"></a>安裝 DC 代理程式服務

若要安裝 Azure AD 密碼保護 DC 代理程式服務，請執行 `AzureADPasswordProtectionDCAgentSetup.msi` 套件。

您可以使用標準 MSI 程式來自動執行軟體安裝，如下列範例所示：

```console
msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart
```

`/norestart`如果您想要讓安裝程式自動重新開機電腦，可以省略旗標。

軟體安裝（或卸載）需要重新開機。 這項需求是因為密碼篩選 Dll 只會在重新開機時載入或卸載。

在網域控制站上安裝 DC 代理程式軟體並重新啟動電腦之後，內部部署 Azure AD 密碼保護的安裝已完成。 任何其他設定都無須進行，也無法進行。 針對內部內部部署 Dc 的密碼變更事件會使用來自 Azure AD 的已設定禁用密碼清單。

若要從 Azure 入口網站啟用內部內部部署 Azure AD 密碼保護，或設定自訂禁用密碼，請參閱 [啟用內部部署 Azure AD 密碼保護](howto-password-ban-bad-on-premises-operations.md)。

> [!TIP]
> 您可以在還不是網域控制站的電腦上安裝 Azure AD 的密碼保護 DC 代理程式。 在此情況下，服務會啟動並執行，但會保持非使用中狀態，直到電腦升級為網域控制站為止。

## <a name="upgrading-the-proxy-service"></a>升級 proxy 服務

Azure AD 密碼保護 proxy 服務支援自動升級。 自動升級會使用 Microsoft Azure AD Connect 代理程式更新程式服務，此服務會與 proxy 服務並存安裝。 自動升級預設為開啟，而且可能會使用 Cmdlet 來啟用或停用 `Set-AzureADPasswordProtectionProxyConfiguration` 。

您可以使用 Cmdlet 來查詢目前的設定 `Get-AzureADPasswordProtectionProxyConfiguration` 。 建議您一律啟用自動升級設定。

此 `Get-AzureADPasswordProtectionProxy` Cmdlet 可用來查詢樹系中所有目前安裝 Azure AD 密碼保護 proxy 伺服器的軟體版本。

### <a name="manual-upgrade-process"></a>手動升級流程

手動升級是藉由執行最新版的 `AzureADPasswordProtectionProxySetup.exe` 軟體安裝程式來完成。 您可以從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=57071)取得軟體的最新版本。

不需要卸載目前版本的 Azure AD 密碼保護 proxy 服務，安裝程式會執行就地升級。 升級 proxy 服務時，不需要重新開機。 您可以使用標準 MSI 程式（例如）來自動升級軟體 `AzureADPasswordProtectionProxySetup.exe /quiet` 。

## <a name="upgrading-the-dc-agent"></a>升級 DC 代理程式

當有較新版本的 Azure AD 密碼保護 DC 代理程式軟體可供使用時，會執行最新版本的軟體套件來完成升級 `AzureADPasswordProtectionDCAgentSetup.msi` 。 您可以從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=57071)取得軟體的最新版本。

不需要卸載目前版本的 DC 代理程式軟體，安裝程式會執行就地升級。 升級 DC 代理程式軟體時一律需要重新開機-這項需求是由核心 Windows 行為所造成。

您可以使用標準 MSI 程式（例如）來自動升級軟體 `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart` 。

`/norestart`如果您想要讓安裝程式自動重新開機電腦，您可以省略旗標。

`Get-AzureADPasswordProtectionDCAgent`Cmdlet 可用來查詢樹系中所有目前安裝 Azure AD 密碼保護 DC 代理程式的軟體版本。

## <a name="next-steps"></a>後續步驟

既然您已在內部部署伺服器上安裝 Azure AD 密碼保護所需的服務，請 [在 Azure 入口網站中啟用內部內部部署 Azure AD 密碼保護](howto-password-ban-bad-on-premises-operations.md) ，以完成您的部署。