---
title: Windows 虛擬桌面的 Microsoft 小組-Azure
description: 如何在 Windows 虛擬桌面上使用 Microsoft 小組。
author: Heidilohr
ms.topic: how-to
ms.date: 07/28/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 049b962740abc98a6ac7d029c1419d40aa722165
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88922560"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>在 Windows 虛擬桌面使用 Microsoft 小組

>[!IMPORTANT]
>Microsoft 365 政府 (GCC) 環境支援小組的媒體優化。 GCC-High 或 DoD 不支援小組的媒體優化。

>[!NOTE]
>Microsoft 小組的媒體優化僅適用于 Windows 10 電腦上的 Windows 桌面用戶端。 媒體優化需要 Windows 桌面用戶端版本1.2.1026.0 或更新版本。

Windows 虛擬桌面的 Microsoft 小組支援聊天與協同作業。 使用媒體優化時，它也支援呼叫和會議功能。 若要深入瞭解如何在虛擬桌面基礎結構 (VDI) 環境中使用 Microsoft 小組，請參閱 [虛擬桌面基礎結構的團隊](/microsoftteams/teams-for-vdi/)。

使用 Microsoft 團隊的媒體優化，Windows 桌面用戶端會在本機處理適用于小組通話和會議的音訊和影片。 您仍然可以搭配其他用戶端使用 Windows 虛擬桌面的 Microsoft 小組，而不需要優化通話和會議。 所有平臺都支援小組聊天和共同作業功能。 若要重新導向遠端會話中的本機裝置，請參閱 [自訂主機集區的遠端桌面通訊協定屬性](#customize-remote-desktop-protocol-properties-for-a-host-pool)。

## <a name="prerequisites"></a>Prerequisites

在 Windows 虛擬桌面電腦上使用 Microsoft 小組之前，您必須執行下列動作：

- 為 Microsoft 小組[準備您的網路](/microsoftteams/prepare-network/)。
- 在 Windows 10 或 Windows 10 IoT 企業版裝置上安裝 [Windows 桌面用戶端](connect-windows-7-10.md) ，以符合 [Windows 電腦上小組](/microsoftteams/hardware-requirements-for-the-teams-app#hardware-requirements-for-teams-on-a-windows-pc/)的 Microsoft 小組硬體需求。
- 連線到 Windows 10 多會話或 Windows 10 企業版虛擬機器 (VM) 。
- 使用每一電腦安裝，在主機上[下載](https://www.microsoft.com/microsoft-365/microsoft-teams/download-app)並安裝小組桌面應用程式。 Microsoft 小組的媒體優化需要小組傳統型應用程式版本1.3.00.4461 或更新版本。

## <a name="install-the-teams-desktop-app"></a>安裝小組桌面應用程式

本節將說明如何在您的 Windows 10 多會話或 Windows 10 企業版 VM 映射上安裝小組桌面應用程式。 若要深入瞭解，請參閱 [VDI 上的安裝或更新團隊桌面應用程式](/microsoftteams/teams-for-vdi#install-or-update-the-teams-desktop-app-on-vdi)。

### <a name="prepare-your-image-for-teams"></a>為小組準備您的映射

若要為小組啟用媒體優化，請在主機上設定下列登錄機碼：

1. 從 [開始] 功能表，以系統管理員身分執行 **RegEdit** 。 流覽至 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams**。
2. 為小組金鑰建立下列值：

| 名稱             | 類型   | 資料/值  |
|------------------|--------|-------------|
| IsWVDEnvironment | DWORD  | 1           |

### <a name="install-the-teams-websocket-service"></a>安裝小組 WebSocket 服務

在 VM 映射上安裝最新的 [WebSocket 服務](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4AQBt) 。 如果您遇到安裝錯誤，請安裝 [最新的 Microsoft Visual C++](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) 可轉散發套件，然後再試一次。

#### <a name="latest-websocket-service-versions"></a>最新的 WebSocket 服務版本

下表列出 WebSocket 服務的最新版本：

|版本        |發行日期  |
|---------------|--------------|
|1.0.2006.11001 |07/28/2020    |
|0.11.0         |05/29/2020    |

#### <a name="updates-for-version-10200611001"></a>1.0.2006.11001 版本的更新

- 修正在通話或會議期間將小組應用程式最小化，而導致內送影片下降的問題。
- 已新增在多監視器桌面會話中選取一個要共用的監視器的支援。

### <a name="install-microsoft-teams"></a>安裝 Microsoft 小組

您可以使用每部電腦或每一使用者的安裝來部署小組桌面應用程式。 若要在您的 Windows 虛擬桌面環境中安裝 Microsoft 小組：

1. 下載符合您環境的 [小組 MSI 套件](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm) 。 建議您在64位作業系統上使用64位安裝程式。

      > [!IMPORTANT]
      > 最新的團隊桌面用戶端版本更新1.3.00.21759 修正了團隊在聊天、頻道和行事曆中顯示 UTC 時區的問題。 新版本的用戶端會顯示遠端會話時區。

2. 執行下列其中一個命令，以將 MSI 安裝至主機 VM：

    - 個別使用者安裝

        ```powershell
        msiexec /i <path_to_msi> /l*v <install_logfile_name>
        ```

        此程式是預設安裝，會將小組安裝到 **% AppData%** 使用者資料夾。 在非持續性設定上，小組無法與每位使用者的安裝正常搭配運作。

    - 每一電腦安裝

        ```powershell
        msiexec /i <path_to_msi> /l*v <install_logfile_name> ALLUSER=1
        ```

        這會將團隊安裝至64位作業系統上的 Program Files (x86) 資料夾，以及32位作業系統上的 Program Files 資料夾。 到目前為止，黃金映射設定已完成。 非持續性的安裝需要針對每部電腦安裝團隊。

        安裝小組時可以設定兩個旗標， **ALLUSER = 1** 和 **ALLUSERS = 1**。 請務必瞭解這些參數之間的差異。 **ALLUSER = 1**參數只能用於 VDI 環境中，以指定每部電腦安裝。 **ALLUSERS = 1**參數可以用於非 VDI 和 vdi 環境。 當您設定此參數時，team Machine-Wide 安裝程式會顯示在主控台的 [程式和功能] 中，以及 Windows 設定中 & 功能的應用程式。 在電腦上具有系統管理員認證的所有使用者都可以卸載團隊。

        > [!NOTE]
        > 在此時登入期間，使用者和系統管理員無法停用小組的自動啟動。

3. 若要從主機 VM 卸載 MSI，請執行下列命令：

      ```powershell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      這會根據作業系統環境，將程式檔 (x86) 資料夾或 Program Files 資料夾中卸載。

      > [!NOTE]
      > 當您使用 MSI 設定 ALLUSER = 1 安裝小組時，自動更新將會停用。 建議您務必至少每個月更新小組一次。 若要深入瞭解如何部署小組傳統型應用程式，請參閱將 [小組桌面應用程式部署至 VM](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm/)。

### <a name="verify-media-optimizations-loaded"></a>確認已載入媒體優化

安裝 WebSocket 服務和小組桌面應用程式之後，請遵循下列步驟來確認小組媒體優化已載入：

1. 選取您的使用者設定檔影像，然後選取 [ **關於**]。
2. 選取 [ **版本**]。

      如果已載入媒體優化，橫幅會顯示已 **優化的 WVD 媒體**。 如果橫幅顯示 **未連線的 WVD 媒體**，請結束小組應用程式，然後再試一次。

3. 選取您的使用者設定檔影像，然後選取 [ **設定**]。

      如果已載入媒體優化，則會在裝置功能表中列舉在本機可用的音訊裝置和攝影機。 如果功能表顯示 **遠端音訊**，請結束小組應用程式，然後再試一次。 如果裝置仍未出現在功能表中，請檢查您本機電腦上的隱私權設定。 確定已開啟 **[設定**  >  **隱私權**  >  **應用程式] 許可權**設定 [**允許應用程式存取您的麥克風**]。 **On** 中斷與遠端會話的連線，然後重新連線，然後再次檢查音訊和影片裝置。 若要使用影片加入通話和會議，您也必須授與應用程式存取相機的許可權。

## <a name="known-issues-and-limitations"></a>已知的問題及限制

在虛擬化環境中使用小組與在非虛擬化環境中使用小組不同。 如需有關虛擬化環境中小組限制的詳細資訊，請參閱 [虛擬化桌面基礎結構的團隊](/microsoftteams/teams-for-vdi#known-issues-and-limitations)。

### <a name="client-deployment-installation-and-setup"></a>用戶端部署、安裝和設定

- 使用每部電腦安裝時，不會自動以非 VDI 團隊用戶端的相同方式更新 VDI 上的小組。 若要更新用戶端，您必須安裝新的 MSI 來更新 VM 映射。
- 只有執行 Windows 10 的電腦上的 Windows 桌面用戶端支援小組的媒體優化。
- 不支援使用在端點上定義的明確 HTTP proxy。

### <a name="calls-and-meetings"></a>通話和會議

- Windows 虛擬桌面環境中的小組桌面用戶端不支援實況活動。 目前，建議您改為從遠端會話中的 [小組 web 用戶端](https://teams.microsoft.com) 加入實況活動。
- 通話或會議目前不支援應用程式共用。 桌面會話支援桌面共用。
- 目前不支援提供控制項和取得控制項。
- Windows 虛擬桌面的團隊一次只支援一個傳入的影片輸入。 這表示每當有人嘗試共用其畫面時，就會顯示其畫面，而不是會議領導人的畫面。
- 由於 WebRTC 限制，內送和外寄影片串流解析僅限720p。
- 小組應用程式不支援隱藏隱藏按鈕或其他裝置的 LED 控制項。

針對與虛擬化環境無關的小組已知問題，請參閱 [您組織中的支援小組](/microsoftteams/known-issues)

## <a name="uservoice-site"></a>UserVoice 網站

在團隊 [UserVoice 網站](https://microsoftteams.uservoice.com/)上的 Windows 虛擬桌面提供 Microsoft 小組意見反應。

## <a name="collect-teams-logs"></a>收集團隊記錄

如果您在 Windows 虛擬桌面環境中遇到小組桌面應用程式的問題，請收集主機 VM 上 **% appdata% \Microsoft\Teams\logs.txt** 下的用戶端記錄。

如果您遇到通話和會議的問題，請使用按鍵組合**Ctrl**  +  **Alt**  +  **Shift**  +  **1**收集小組 Web 用戶端記錄。 記錄檔將會寫入至主機 VM 上的 **%Userprofile%\Downloads\MSTeams 診斷記錄 DATE_TIME.txt** 。

## <a name="contact-microsoft-teams-support"></a>聯絡 Microsoft 小組支援

若要聯絡 Microsoft 團隊支援，請移至 [Microsoft 365 系統管理中心](/microsoft-365/admin/contact-support-for-business-products)。

## <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>自訂主機集區的遠端桌面通訊協定屬性

自訂主機集區的遠端桌面通訊協定 (RDP) 屬性（例如多監視器體驗或啟用麥克風和音訊重新導向），可讓您根據使用者的需求為您的使用者提供最佳體驗。

使用具有媒體優化的小組時，不需要啟用裝置重新導向。 如果您使用沒有媒體優化的小組，請設定下列 RDP 屬性來啟用麥克風和攝影機重新導向：

- `audiocapturemode:i:1` 從本機裝置啟用音訊捕捉，並在遠端會話中重新導向音訊應用程式。
- `audiomode:i:0` 在本機電腦上播放音訊。
- `camerastoredirect:s:*` 重新導向所有相機。

若要深入瞭解，請參閱 [自訂主機集區的遠端桌面通訊協定屬性](customize-rdp-properties.md)。
