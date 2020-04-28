---
title: Windows 虛擬桌面上的 Microsoft 小組-Azure
description: 如何在 Windows 虛擬桌面上使用 Microsoft 團隊。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a860b005457c6e02187423a3ffbbc63fe7c758b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187523"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>在 Windows 虛擬桌面上使用 Microsoft 團隊

> 適用于： Windows 10 和 Windows 10 IoT 企業版

虛擬化環境針對共同作業應用程式（例如 Microsoft 團隊）提供了一組獨特的挑戰，包括增加延遲、高主機 CPU 使用率，以及整體音訊和影片效能不佳。 若要深入瞭解如何在 VDI 環境中使用 Microsoft 小組，請參閱[虛擬化桌面基礎結構的小組](https://docs.microsoft.com/microsoftteams/teams-for-vdi)。

## <a name="prerequisites"></a>Prerequisites

在 Windows 虛擬桌面上使用 Microsoft 團隊之前，您必須執行下列動作：

- 在符合 Microsoft 小組[硬體需求](https://docs.microsoft.com/microsoftteams/hardware-requirements-for-the-teams-app)的 windows 10 裝置上安裝[windows 桌面用戶端](connect-windows-7-and-10.md)。
- 連接到 Windows 10 多會話或 Windows 10 企業版虛擬機器（VM）。
- 為 Microsoft 團隊[準備您的網路](https://docs.microsoft.com/microsoftteams/prepare-network)。

## <a name="use-unoptimized-microsoft-teams"></a>使用未優化的 Microsoft 團隊

您可以在 Windows 虛擬桌面環境中使用 Microsoft 團隊，以利用 Microsoft 團隊的交談和共同作業功能。 Windows 虛擬桌面不支援 VDI 音訊/影片（AV）優化的小組。 不支援通話與會議。 如果貴組織的原則允許，您仍然可以使用音訊進行音訊通話和加入會議，但是電話中未優化的音訊品質會根據您的主機設定而有所不同，而且可能不可靠。 若要深入瞭解，請查看[有關 VDI 效能考慮的小組](https://docs.microsoft.com/microsoftteams/teams-for-vdi#teams-on-vdi-performance-considerations)。

### <a name="prepare-your-image-for-teams"></a>為小組準備您的映射

若要啟用各電腦安裝的小組，請在主機上設定下列登錄機碼：

```shell
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams\IsWVDEnvironment]
  Type: REG_DWORD
  Value: 1
```

### <a name="install-microsoft-teams"></a>安裝 Microsoft 團隊

您可以使用每部電腦安裝來部署小組桌面應用程式。 若要在您的 Windows 虛擬桌面環境中安裝 Microsoft 團隊：

1. 下載符合您環境的[小組 MSI 套件](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm)。 我們建議在64位作業系統上使用64位安裝程式。
2. 執行此命令以將 MSI 安裝到主機 VM。

      ```shell
      msiexec /i <msi_name> /l*v < install_logfile_name> ALLUSER=1
      ```

      這會將小組安裝到 Program Files 或 Program Files （x86）。 下次登入並啟動小組時，應用程式會要求您提供認證。

      > [!NOTE]
      > 在登入期間，使用者和系統管理員無法停用小組自動啟動。

      若要從主機 VM 卸載 MSI，請執行此命令：

      ```shell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      > [!NOTE]
      > 如果您安裝具有 MSI 設定 ALLUSER = 1 的小組，自動更新將會停用。 我們建議您至少每個月更新一次小組。 若要深入瞭解如何部署小組桌面應用程式，請參閱將[小組桌面應用程式部署至 VM](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm)。

## <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>自訂主機集區的遠端桌面通訊協定屬性
自訂主機集區的遠端桌面通訊協定（RDP）內容，例如多監視器體驗、啟用麥克風和音訊重新導向功能，可讓您根據自己的需求為使用者提供最佳的體驗。 您可以使用**RdsHostPool** Cmdlet 中的 **-CustomRdpProperty**參數，在 WINDOWS 虛擬桌面中自訂 RDP 屬性。
如需支援的屬性及其預設值的完整清單，請參閱[支援的 RDP 檔案設定](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context)。
