---
title: Windows 虛擬桌面上的 Microsoft 小組-Azure
description: 如何在 Windows 虛擬桌面上使用 Microsoft 團隊。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 07/28/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f6185cbb871d63cfdf5a4c336944158593b63e4a
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87372836"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>在 Windows 虛擬桌面上使用 Microsoft 團隊

>[!IMPORTANT]
>Microsoft 365 的政府環境不支援小組的媒體優化。

>[!NOTE]
>Microsoft 小組的媒體優化僅適用于 Windows 10 電腦上的 Windows 桌面用戶端。 媒體優化需要 Windows 桌面用戶端版本1.2.1026.0 或更新版本。

Microsoft 團隊的 Windows 虛擬桌面支援聊天與共同作業。 透過媒體優化，它也支援呼叫和會議功能。 若要深入瞭解如何在虛擬桌面基礎結構（VDI）環境中使用 Microsoft 小組，請參閱[虛擬化桌面基礎結構的小組](/microsoftteams/teams-for-vdi/)。

透過適用于 Microsoft 小組的媒體優化，Windows 桌面用戶端會在本機處理音訊和影片，供小組通話和會議使用。 您仍然可以使用 Microsoft 小組在 Windows 虛擬桌面與其他用戶端，而不需要經過優化的通話和會議。 所有平臺都支援小組交談和共同作業功能。 若要重新導向遠端會話中的本機裝置，請參閱[自訂主機集區的遠端桌面通訊協定屬性](#customize-remote-desktop-protocol-properties-for-a-host-pool)。

## <a name="prerequisites"></a>先決條件

在 Windows 虛擬桌面上使用 Microsoft 團隊之前，您必須執行下列動作：

- 為 Microsoft 團隊[準備您的網路](/microsoftteams/prepare-network/)。
- 在 windows 10 或 Windows 10 IoT 企業版裝置上安裝[Windows 桌面用戶端](connect-windows-7-10.md)，以符合 Microsoft 小組[在 windows 電腦上的小組硬體需求](/microsoftteams/hardware-requirements-for-the-teams-app#hardware-requirements-for-teams-on-a-windows-pc/)。
- 連接到 Windows 10 多會話或 Windows 10 企業版虛擬機器（VM）。
- 使用每一電腦安裝，在主機上安裝小組桌面應用程式。 Microsoft 小組的媒體優化需要小組桌面應用程式版本1.3.00.4461 或更新版本。

## <a name="install-the-teams-desktop-app"></a>安裝小組桌面應用程式

本節將說明如何在您的 Windows 10 多會話或 Windows 10 企業版 VM 映射上安裝小組桌面應用程式。 若要深入瞭解，請參閱[在 VDI 上安裝或更新小組桌面應用程式](/microsoftteams/teams-for-vdi#install-or-update-the-teams-desktop-app-on-vdi/)。

### <a name="prepare-your-image-for-teams"></a>為小組準備您的映射

若要為小組啟用媒體優化，請在主機上設定下列登錄機碼：

1. 在 [開始] 功能表中，以系統管理員身分執行**RegEdit** 。 流覽至**HKEY_LOCAL_MACHINE \software\microsoft\teams**。
2. 為小組索引鍵建立下列值：

| 名稱             | 類型   | 資料/值  |
|------------------|--------|-------------|
| IsWVDEnvironment | DWORD  | 1           |

### <a name="install-the-teams-websocket-service"></a>安裝小組 WebSocket 服務

在您的 VM 映射上安裝最新的[WebSocket 服務](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4AQBt)。 如果您遇到安裝錯誤，請安裝[最新的 Microsoft Visual C++](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)可轉散發套件，然後再試一次。

#### <a name="latest-websocket-service-versions"></a>最新的 WebSocket 服務版本

下表列出 WebSocket 服務的最新版本：

|版本        |發行日期  |
|---------------|--------------|
|1.0.2006.11001 |07/28/2020    |
|0.11.0         |05/29/2020    |

#### <a name="updates-for-version-10200611001"></a>版本1.0.2006.11001 的更新

- 已修正問題：在呼叫或會議期間，將小組應用程式降至最低會導致連入影片下降。
- 已新增在多監視器桌面會話中選取一個監視器以進行共用的支援。

### <a name="install-microsoft-teams"></a>安裝 Microsoft 團隊

您可以使用每部電腦或每個使用者的安裝來部署小組桌面應用程式。 若要在您的 Windows 虛擬桌面環境中安裝 Microsoft 團隊：

1. 下載符合您環境的[小組 MSI 套件](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm/)。 我們建議在64位作業系統上使用64位安裝程式。

      > [!NOTE]
      > Microsoft 小組的媒體優化需要小組桌面應用程式版本1.3.00.4461 或更新版本。

2. 執行下列其中一個命令，以將 MSI 安裝到主機 VM：

    - 個別使用者安裝

        ```powershell
        msiexec /i <path_to_msi> /l*v <install_logfile_name> ALLUSERS=1
        ```

        此程式是預設安裝，會將小組安裝到 **% AppData%** user 資料夾。 小組不會在非持續性的設定上，與每個使用者的安裝搭配運作。

    - 每一電腦安裝

        ```powershell
        msiexec /i <path_to_msi> /l*v <install_logfile_name> ALLUSER=1 ALLUSERS=1
        ```

        這會將小組安裝到64位作業系統上的 Program Files （x86）資料夾，以及32位作業系統上的 Program Files 資料夾。 此時，黃金映射設定已完成。 非持續性的安裝需要為每部電腦安裝小組。

        下次當您在會話中開啟小組時，系統會要求您提供認證。

        > [!NOTE]
        > 在登入期間，使用者和系統管理員無法停用小組自動啟動。

3. 若要從主機 VM 卸載 MSI，請執行此命令：

      ```powershell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      這會根據作業系統環境，從 [Program Files （x86）] 資料夾或 [Program Files] 資料夾卸載小組。

      > [!NOTE]
      > 當您安裝具有 MSI 設定 ALLUSER = 1 的小組時，自動更新將會停用。 我們建議您至少每個月更新一次小組。 若要深入瞭解如何部署小組桌面應用程式，請參閱將[小組桌面應用程式部署至 VM](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm/)。

### <a name="verify-media-optimizations-loaded"></a>確認媒體優化已載入

安裝 WebSocket 服務和小組桌面應用程式之後，請遵循下列步驟來確認小組媒體優化已載入：

1. 選取您的使用者設定檔影像，然後選取 [**關於**]。
2. 選取 [**版本**]。

      如果已載入媒體優化，橫幅會顯示**WVD 媒體優化**。 如果橫幅顯示**WVD 未連線媒體**，請結束小組應用程式，然後再試一次。

3. 選取您的使用者設定檔影像，然後選取 [**設定**]。

      如果已載入媒體優化，則會在 [裝置] 功能表中列舉本機可用的音訊裝置和相機。 如果功能表顯示**遠端音訊**，請退出小組應用程式，然後再試一次。 如果裝置仍未出現在功能表中，請檢查您的本機電腦上的隱私權設定。 確定 [**設定**] [  >  **隱私權**  >  **應用程式**] [許可權] 設定的 [**允許應用程式存取您的麥克風**] 已**開啟**。 中斷與遠端會話的連線，然後重新連線並再次檢查音訊和視頻裝置。 若要使用影片加入電話和會議，您也必須授與應用程式存取相機的許可權。

## <a name="known-issues-and-limitations"></a>已知的問題和限制

在虛擬化環境中使用小組與在非虛擬化環境中使用小組不同。 如需有關虛擬化環境中小組限制的詳細資訊，請參閱[虛擬化桌面基礎結構的小組](/microsoftteams/teams-for-vdi#known-issues-and-limitations/)。

### <a name="client-deployment-installation-and-setup"></a>用戶端部署、安裝和設定

- 使用每部電腦安裝時，VDI 上的小組不會以非 VDI 團隊用戶端的相同方式自動更新。 若要更新用戶端，您必須安裝新的 MSI 來更新 VM 映射。
- 小組目前只會顯示聊天、頻道和行事曆中的 UTC 時區。
- 只有執行 Windows 10 之電腦上的 Windows 桌面用戶端，才支援小組的媒體優化。
- 不支援使用定義在端點上的明確 HTTP proxy。

### <a name="calls-and-meetings"></a>通話與會議

- Windows 虛擬桌面環境中的小組桌面用戶端不支援實況活動。 目前，我們建議您改為在遠端會話中加入[小組 web 用戶端](https://teams.microsoft.com)的實況活動。
- 通話或會議目前不支援應用程式共用。 桌面會話支援桌面共用。
- 目前不支援 [提供控制項] 和 [取得控制項]。
- Windows 虛擬桌面的小組一次只支援一個傳入的影片輸入。 這表示每當有人嘗試共用其畫面時，畫面將會出現，而不是會議領導者的畫面。
- 由於 WebRTC 限制，傳入和傳出的影片串流解析度僅限於720p。
- 小組應用程式不支援使用其他裝置的 HID 按鈕或 LED 控制項。

針對與虛擬化環境無關的已知問題，請參閱[組織中的支援小組](/microsoftteams/known-issues/)

## <a name="uservoice-site"></a>UserVoice 網站

針對小組[UserVoice 網站](https://microsoftteams.uservoice.com/)上的 Windows 虛擬桌面，提供 Microsoft 團隊的意見反應。

## <a name="collect-teams-logs"></a>收集小組記錄

如果您的 Windows 虛擬桌面環境中的小組桌面應用程式發生問題，請收集主機 VM 上 **% appdata% \Microsoft\Teams\logs.txt**下的用戶端記錄檔。

如果您遇到通話和會議的問題，請使用**ctrl**  +  **Alt**  +  **Shift**  +  **1**按鍵組合來收集小組 Web 用戶端記錄。 記錄將會寫入至主機 VM 上的 **%Userprofile%\Downloads\MSTeams 診斷記錄 DATE_TIME.txt** 。

## <a name="contact-microsoft-teams-support"></a>聯絡 Microsoft 團隊支援

若要聯繫 Microsoft 團隊支援，請移至[Microsoft 365 系統管理中心](/microsoft-365/admin/contact-support-for-business-products)。

## <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>自訂主機集區的遠端桌面通訊協定屬性

自訂主機集區的遠端桌面通訊協定（RDP）內容，例如多監視器體驗或啟用麥克風和音訊重新導向，可讓您根據使用者的需求為您的使用者提供最佳的體驗。

使用具有媒體優化的小組時，不需要啟用裝置重新導向。 如果您使用沒有媒體優化的小組，請設定下列 RDP 屬性來啟用麥克風和相機重新導向：

- `audiocapturemode:i:1`從本機裝置啟用音訊捕捉，並重新導向遠端會話中的音訊應用程式。
- `audiomode:i:0`在本機電腦上播放音訊。
- `camerastoredirect:s:*`重新導向所有相機。

若要深入瞭解，請參閱[自訂主機集區的遠端桌面通訊協定屬性](customize-rdp-properties.md)。
