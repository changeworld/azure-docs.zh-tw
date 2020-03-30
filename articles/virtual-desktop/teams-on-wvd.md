---
title: 微軟團隊在 Windows 虛擬桌面 - Azure
description: 如何在 Windows 虛擬桌面上使用 Microsoft 團隊。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ea9bfd21e7f3b92c99600a2492a809a0fc051ed9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159614"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>在 Windows 虛擬桌面上使用 Microsoft 團隊

> 適用于：Windows 10 和 Windows 10 IoT 企業

虛擬化環境為 Microsoft Teams 等協作應用帶來了一系列獨特的挑戰，包括延遲增加、主機 CPU 使用率高以及整體音訊和視頻性能不佳。 要瞭解有關在 VDI 環境中使用 Microsoft 團隊的詳細資訊，請查看[虛擬化桌面基礎結構團隊](https://docs.microsoft.com/microsoftteams/teams-for-vdi)。

## <a name="prerequisites"></a>Prerequisites

在 Windows 虛擬桌面上使用 Microsoft 團隊之前，您需要執行以下操作：

- 在滿足微軟團隊[硬體要求的](https://docs.microsoft.com/microsoftteams/hardware-requirements-for-the-teams-app)Windows 10 設備上安裝[Windows 桌面用戶端](connect-windows-7-and-10.md)。
- 連接到 Windows 10 多會話或 Windows 10 企業虛擬機器 （VM）。
- 為 Microsoft 團隊[準備網路](https://docs.microsoft.com/microsoftteams/prepare-network)。

## <a name="use-unoptimized-microsoft-teams"></a>使用未優化的微軟團隊

您可以在 Windows 虛擬桌面環境中使用未優化的 Microsoft 團隊來利用 Microsoft 團隊的完整聊天和協作功能以及音訊撥號。 呼叫中的音訊品質將因主機配置而異，因為未優化的呼叫會使用更多主機 CPU。

### <a name="install-microsoft-teams"></a>安裝微軟團隊

要在 Windows 虛擬桌面環境中安裝 Microsoft 團隊，請執行：

1. 下載與您的環境相匹配[的團隊 MSI 包](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm)。 我們建議在 64 位作業系統上使用 64 位安裝程式。
2. 運行此命令以將 MSI 安裝到主機 VM。

      ```shell
      msiexec /i <msi_name> /l*v < install_logfile_name> ALLUSER=1
      ```

      這將安裝團隊到程式檔或程式檔 （x86）。 下次登錄並啟動 Teams 時，應用將要求您提供憑據。

      > [!NOTE]
      > 此時，使用者和管理員無法禁用 Teams 的自動啟動。

      要從主機 VM 卸載 MSI，請運行以下命令：

      ```shell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      > [!NOTE]
      > 如果安裝具有 MSI 設置 ALLUSER_1 的團隊，將自動更新將被禁用。 我們建議您確保每月至少更新一次 Teams。
