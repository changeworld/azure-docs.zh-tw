---
title: 設定裝置重新導向-Azure
description: 如何設定 Windows 虛擬桌面的裝置重新導向。
author: Heidilohr
ms.topic: how-to
ms.date: 09/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f3441d7c7f42c58928bb97c945e7b1e7673f7afa
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876985"
---
# <a name="configure-device-redirections"></a>設定裝置重新導向

為您的 Windows 虛擬桌面環境設定裝置重新導向，可讓您在遠端會話中使用印表機、USB 裝置、麥克風和其他週邊裝置。 某些裝置重新導向需要對遠端桌面通訊協定 (RDP) 屬性和群組原則設定進行變更。

## <a name="supported-device-redirections"></a>支援的裝置重新導向

每個用戶端都支援不同的裝置重新導向。 請查看 [用戶端](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-app-compare) ，以取得每個用戶端支援之裝置重新導向的完整清單。

## <a name="customizing-rdp-properties-for-a-host-pool"></a>自訂主機集區的 RDP 屬性

若要深入瞭解如何使用 PowerShell 或 Azure 入口網站來自訂主機集區的 RDP 內容，請參閱 [RDP 屬性](customize-rdp-properties.md)。 如需支援的 RDP 屬性完整清單，請參閱 [支援的 rdp 檔案設定](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context)。

## <a name="setup-device-redirections"></a>設定裝置重新導向

您可以使用下列 RDP 屬性和群組原則設定來設定裝置重新導向。

### <a name="audio-input-microphone-redirection"></a>音訊輸入 (麥克風) 重新導向

設定下列 RDP 屬性以設定音訊輸入重新導向：

- `audiocapturemode:i:1` 啟用音訊輸入重新導向。
- `audiocapturemode:i:0` 停用音訊輸入重新導向。

### <a name="audio-output-speaker-redirection"></a>音訊輸出 (說話者) 重新導向

設定下列 RDP 屬性以設定音訊輸出重新導向：

- `audiomode:i:0` 啟用音訊輸出重新導向。
- `audiomode:i:1` 或 `audiomode:i:2` 停用音訊輸出重新導向。

### <a name="camera-redirection"></a>攝影機重新導向

設定下列 RDP 屬性來設定相機重新導向：

- `camerastoredirect:s:*` 重新導向所有相機。
- `camerastoredirect:s:` 停用相機重新導向。

您也可以使用以分號分隔的 KSCATEGORY_VIDEO_CAMERA 介面清單來重新導向特定相機，例如 `camerastoredirect:s:\?\usb#vid_0bda&pid_58b0&mi` 。

### <a name="clipboard-redirection"></a>剪貼簿重新導向

設定下列 RDP 屬性來設定剪貼簿重新導向：

- `redirectclipboard:i:1` 啟用剪貼簿重新導向。
- `redirectclipboard:i:0` 停用剪貼簿重新導向。

### <a name="com-port-redirections"></a>COM 埠重新導向

設定下列 RDP 屬性來設定 COM 埠重新導向：

- `redirectcomports:i:1` 啟用 COM 埠重新導向。
- `redirectcomports:i:0` 停用 COM 埠重新導向。

### <a name="usb-redirection"></a>USB 重新導向

首先，設定下列 RDP 屬性以啟用 USB 裝置重新導向：

- `usbdevicestoredirect:s:*` 啟用 USB 裝置重新導向。
- `usbdevicestoredirect:s:` 停用 USB 裝置重新導向。

其次，在使用者的本機裝置上設定下列群組原則：

- 流覽至**Computer Configuration**  >  **Policies** >  **Administrative Templates**  >  **Windows Components**  >  **遠端桌面服務**  >  **遠端桌面連線用戶端**  >  **RemoteFX USB 裝置**重新導向的系統管理範本 Windows 元件的電腦設定原則。
- 選取 [ **允許從這部電腦對其他支援的 REMOTEFX USB 裝置進行 RDP**重新導向]。
- 選取 [ **已啟用** ] 選項，然後選取 [RemoteFX USB 重新導向存取權限] 方塊中的 [系統 **管理員和使用者** ]。
- 選取 [確定]  。

### <a name="plug-and-play-device-redirection"></a>隨插即用裝置重新導向

設定下列 RDP 屬性以設定隨插即用裝置重新導向：

- `devicestoredirect:s:*` 啟用所有隨插即用裝置的重新導向。
- `devicestoredirect:s:` 停用隨插即用裝置的重新導向。

您也可以使用以分號分隔的清單（例如）來選取特定的隨插即用裝置 `devicestoredirect:s:root\*PNP0F08` 。

### <a name="local-drive-redirection"></a>本機磁片磁碟機重新導向

設定下列 RDP 屬性來設定本機磁片磁碟機重新導向：

- `drivestoredirect:s:*` 啟用所有磁片磁碟機的重新導向。
- `drivestoredirect:s:` 停用本機磁片磁碟機重新導向。

您也可以使用以分號分隔的清單（例如）來選取特定磁片磁碟機 `drivestoredirect:s:C:;E:;` 。

### <a name="printer-redirection"></a>印表機重新導向

設定下列 RDP 屬性來設定印表機重新導向：

- `redirectprinters:i:1` 啟用印表機重新導向。
- `redirectprinters:i:0` 停用印表機重新導向。

### <a name="smart-card-redirection"></a>智慧卡重新導向

設定下列 RDP 屬性來設定智慧卡重新導向：

- `redirectsmartcards:i:1` 啟用智慧卡重新導向。
- `redirectsmartcards:i:0` 停用智慧卡重新導向。
