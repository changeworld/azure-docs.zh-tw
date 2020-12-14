---
title: Windows 虛擬桌面準備 MSIX 應用程式附加映射預覽-Azure
description: 如何建立 Windows 虛擬桌面主機集區的 MSIX 應用程式附加映射。
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 204cc9a05d62caf62179100fa3496be422a3ec0c
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97425792"
---
# <a name="prepare-an-msix-image-for-windows-virtual-desktop"></a>準備適用于 Windows 虛擬桌面的 MSIX 映射

> [!IMPORTANT]
> MSIX 應用程式附加目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

MSIX 應用程式附加 (預覽版) 是一種應用程式分層解決方案，可讓您將應用程式從 MSIX 套件動態附加至使用者會話。 MSIX 封裝系統會將應用程式與作業系統分開，讓您更輕鬆地為虛擬機器建立映射。 MSIX 套件也可讓您更有效地控制使用者可以在其虛擬機器中存取的應用程式。 您甚至可以將應用程式與主要映射分開，稍後再將它們提供給使用者。

## <a name="create-a-vhd-or-vhdx-package-for-msix"></a>建立適用于 MSIX 的 VHD 或 VHDX 套件

MSIX 套件必須使用 VHD 或 VHDX 格式才能正常運作。 這表示，若要開始使用，您必須建立 VHD 或 VHDX 套件。

>[!NOTE]
>如果您還沒有這麼做，請務必遵循在 [Windows 10 上安裝 hyper-v](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)中的指示來啟用 hyper-v。

若要建立適用于 MSIX 的 VHD 或 VHDX 套件：

1. 首先，開啟 PowerShell。
2. 接下來，執行下列 Cmdlet 以建立 VHD：

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    > 請確定 VHD 夠大，足以容納擴充的 MSIX 套件。

3. 執行下列 Cmdlet 來掛接您剛才建立的 VHD：

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

4. 接下來，執行此 Cmdlet 以初始化掛接的 VHD：

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

5. 之後，請執行此 Cmdlet 來為初始化的 VHD 建立新的磁碟分割：

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

6. 執行此 Cmdlet 來格式化磁碟分割：

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

7. 最後，在裝載的 VHD 上建立父資料夾。 這是必要步驟，因為 MSIX 套件必須有上層資料夾才能正常運作。 您將上層資料夾命名不重要，只要上層資料夾存在即可。

## <a name="expand-msix"></a>展開 MSIX

之後，您必須將其檔案「解壓縮」至 VHD，以擴充 MSIX 映射。

若要展開 MSIX 映射：

1. [下載 msixmgr 工具](https://aka.ms/msixmgr)，並將 .zip 資料夾儲存至工作階段主機 VM 內的資料夾。

2. 將 msixmgr 工具 .zip 資料夾解壓縮。

3. 將來源 MSIX 套件放在您解壓縮 msixmgr 工具的相同資料夾中。

4. 以系統管理員身分開啟命令提示字元，並瀏覽至您下載並解壓縮 msixmgr 工具的資料夾。

5. 執行下列 Cmdlet，將 MSIX 解壓縮至您在上一節中建立的 VHD。

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    完成解壓縮之後，應會出現下列訊息：

    > 已成功解壓縮並套用適用于封裝的 Acl： <package name> . msix

    >[!NOTE]
    > 如果您使用的是網路上的商務用 Microsoft Store 或教育版或未連線到網際網路的裝置上的套件，則必須從 Microsoft Store 下載並安裝套件授權，才能執行應用程式。 若要取得授權，請參閱 [離線使用套件](app-attach.md#use-packages-offline)。

6. 移至已掛接的 VHD 並開啟應用程式資料夾，以確定套件內容存在。

7. 卸載 VHD。

## <a name="upload-msix-image-to-share"></a>將 MSIX 映射上傳至共用

建立 MSIX 套件之後，您必須將產生的 VHD、VHDX 或 CIM 檔案上傳至使用者的虛擬機器可以存取的共用。

## <a name="next-steps"></a>後續步驟

在 [Windows 虛擬桌面 >techcommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)詢問有關這項功能的問題。

您也可以在 [Windows 虛擬桌面意見反應中樞](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)留下 Windows 虛擬桌面的意見反應。

以下是您可能會覺得有用的一些其他文章：

- [MSIX 應用程式附加詞彙](app-attach-glossary.md)
- [MSIX 應用程式附加常見問題](app-attach-faq.md)