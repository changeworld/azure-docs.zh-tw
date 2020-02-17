---
title: 準備和自訂主要 VHD 映射-Azure
description: 如何準備、自訂 Windows 虛擬桌面主要映射並將其上傳至 Azure。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
ms.openlocfilehash: 23763123ce8e92b6bb15b2b33a196ed1a1d75c9f
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368790"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>準備和自訂主要 VHD 映像

本文說明如何準備要上傳至 Azure 的主要虛擬硬碟（VHD）映射，包括如何建立虛擬機器（Vm）並在其上安裝軟體。 這些指示適用於可搭配您組織現有程序使用的 Windows 虛擬桌面特定設定。

## <a name="create-a-vm"></a>建立 VM

Windows 10 企業版多會話可在 Azure 映射庫中取得。 有兩個選項可自訂此影像。

第一個選項是遵循[從受控映射建立 VM](../virtual-machines/windows/create-vm-generalized-managed.md)中的指示，在 Azure 中布建虛擬機器（VM），然後直接跳到[軟體準備和安裝](set-up-customize-master-image.md#software-preparation-and-installation)。

第二個選項是在本機建立映射，方法是下載映射、布建 Hyper-v VM，並根據您的需求進行自訂，我們將在下一節中討論。

### <a name="local-image-creation"></a>建立本機映射

將映射下載到本機位置之後，請開啟 [ **Hyper-v 管理員**]，以使用您所複製的 VHD 來建立 VM。 下列指示是簡單版本，但您可以在在[hyper-v 中建立虛擬機器](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v/)中找到更詳細的指示。

若要使用複製的 VHD 建立 VM：

1. 開啟 [**新增虛擬機器]** 。

2. 在 [指定世代] 頁面上，選取 [**第1代**]。

    ![[指定世代] 頁面的螢幕擷取畫面。 [第1代] 選項已選取。](media/a41174fd41302a181e46385e1e701975.png)

3. 在 [檢查點類型] 底下，取消核取核取方塊以停用檢查點。

    ![[檢查點] 頁面之 [檢查點類型] 區段的螢幕擷取畫面。](media/20c6dda51d7cafef33251188ae1c0c6a.png)

您也可以在 PowerShell 中執行下列 Cmdlet 來停用檢查點。

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>固定磁片

如果您從現有的 VHD 建立 VM，預設會建立動態磁碟。 您可以選取 [**編輯磁片**] 來變更為固定磁片，如下圖所示。 如需更詳細的指示，請參閱[準備要上傳至 Azure 的 WINDOWS VHD 或 VHDX](../virtual-machines/windows/prepare-for-upload-vhd-image.md)。

![[編輯磁片] 選項的螢幕擷取畫面。](media/35772414b5a0f81f06f54065561d1414.png)

您也可以執行下列 PowerShell Cmdlet，將磁片變更為固定磁片。

```powershell
Convert-VHD –Path c:\\test\\MY-VM.vhdx –DestinationPath c:\\test\\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>軟體準備和安裝

本節涵蓋如何準備和安裝 FSLogix 和 Windows Defender，以及應用程式和映射登錄的一些基本設定選項。 

如果您要在您的 VM 上安裝 Office 365 ProPlus 和 OneDrive，請移至在[主要 VHD 映射上安裝 office](install-office-on-wvd-master-image.md) ，並依照該處的指示來安裝應用程式。 完成之後，請返回此文章。

如果您的使用者需要存取特定 LOB 應用程式，建議您在完成本節的指示之後加以安裝。

### <a name="set-up-user-profile-container-fslogix"></a>設定使用者設定檔容器（FSLogix）

若要包含 FSLogix 容器作為映射的一部分，請遵循[使用檔案共用建立主機集區的設定檔容器](create-host-pools-user-profile.md#configure-the-fslogix-profile-container)中的指示。 您可以使用[本快速入門](/fslogix/configure-cloud-cache-tutorial/)來測試 FSLogix 容器的功能。

### <a name="configure-windows-defender"></a>設定 Windows Defender

如果 VM 中已設定 Windows Defender，請確定它已設定為不會在附件期間掃描 VHD 和 VHDX 檔案的完整內容。

此設定只會在附件期間移除 VHD 和 VHDX 檔案的掃描，但不會影響即時掃描。

如需如何在 Windows Server 上設定 Windows Defender 的詳細指示，請參閱[在 Windows server 上設定 Windows Defender 防毒軟體排除](/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus/)。

若要深入瞭解如何設定 Windows Defender 以排除掃描的特定檔案，請參閱[根據副檔名和資料夾位置設定和驗證排除](/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus/)專案。

### <a name="disable-automatic-updates"></a>停用自動更新

若要透過本機群組原則停用自動更新：

1. 開啟**本機群組原則編輯器\\系統管理範本\\Windows 元件\\Windows Update**。
2. 以滑鼠右鍵按一下 [設定**自動更新**]，並將它設定為 [**停用**]。

您也可以在命令提示字元上執行下列命令，以停用自動更新。

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>指定 Windows 10 電腦的開始版面配置（選擇性）

執行此命令以指定 Windows 10 電腦的開始配置。

```batch
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="set-up-time-zone-redirection"></a>設定時區重新導向

因為主機集區中的所有 Vm 都屬於相同的安全性群組，所以可以在群組原則層級上強制執行時區重新導向。

若要重新導向時間區域：

1. 在 Active Directory 伺服器上，開啟**群組原則管理主控台**。
2. 展開您的網域，並群組原則物件。
3. 在您為群組原則設定建立的**群組原則物件**上按一下滑鼠右鍵，然後選取 [**編輯**]。
4. 在**群組原則管理編輯器**中，流覽至**電腦**設定 ** > 原則** > **系統管理範本** > **Windows 元件** ** > 遠端桌面服務** > **遠端桌面工作階段主機** **裝置和資源**重新導向。 > 
5. 啟用 [**允許時區**重新導向] 設定。

您也可以在主要映射上執行這個命令，以重新導向時間區域：

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>停用存放裝置意義

針對使用 Windows 10 企業版或 Windows 10 企業版多會話的 Windows 虛擬桌面工作階段主機，建議停用存放裝置感知。 您可以在 [**儲存體**] 下的 [設定] 功能表中停用存放裝置感知，如下列螢幕擷取畫面所示：

![[設定] 底下 [儲存體] 功能表的螢幕擷取畫面。 [儲存感知] 選項已關閉。](media/storagesense.png)

您也可以執行下列命令，將設定變更為登錄：

```batch
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy" /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>包含其他語言支援

本文並未涵蓋如何設定語言和區域支援。 如需詳細資訊，請參閱下列文章：

- [將語言新增至 Windows 映像](/windows-hardware/manufacture/desktop/add-language-packs-to-windows/)
- [功能隨選安裝](/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities/)
- [依需求的語言和區域功能（FOD）](/windows-hardware/manufacture/desktop/features-on-demand-language-fod/)

### <a name="other-applications-and-registry-configuration"></a>其他應用程式和登錄設定

本節涵蓋應用程式和作業系統設定。 本節中的所有設定都是透過可由命令列和 regedit 工具執行的登錄專案來完成。

>[!NOTE]
>您可以使用群組原則物件（Gpo）或登錄匯入來執行設定中的最佳作法。 系統管理員可以根據組織的需求選擇任一選項。

若要在 Windows 10 企業版多會話上收集遙測資料的意見反應中樞，請執行此命令：

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 3 /f
```

執行下列命令來修正 Watson 損毀：

```batch
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

在登錄編輯器中輸入下列命令，以修正最適合的解決方式支援。 您必須先執行命令，才能啟用並存堆疊。

```batch
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxYResolution /t REG_DWORD /d 2880 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxYResolution /t REG_DWORD /d 2880 /f
```

## <a name="prepare-the-image-for-upload-to-azure"></a>準備要上傳至 Azure 的映射

當您完成設定並安裝所有應用程式之後，請遵循[準備 WINDOWS VHD 或 VHDX 上傳至 Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md)以準備映射的指示。

準備要上傳的映射之後，請確定 VM 保持在關閉或已解除配置的狀態。

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>將主要映射上傳至 Azure 中的儲存體帳戶

本節僅適用于在本機建立主要映射時。

下列指示將告訴您如何將您的主要映射上傳至 Azure 儲存體帳戶。 如果您還沒有 Azure 儲存體帳戶，請遵循[這篇文章](/azure/javascript/tutorial-vscode-static-website-node-03)中的指示來建立一個。

1. 如果您還沒有 VM 映射（VHD），請將它轉換為固定。 如果您未將影像轉換為固定，則無法成功建立映射。

2. 將 VHD 上傳至儲存體帳戶中的 blob 容器。 您可以使用[儲存體總管工具](https://azure.microsoft.com/features/storage-explorer/)快速上傳。 若要深入瞭解儲存體總管工具，請參閱[這篇文章](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)。

    ![Microsoft Azure 儲存體總管工具之 [搜尋] 視窗的螢幕擷取畫面。 已選取 [將 .vhd 或 vhdx 檔案當做分頁 blob （建議選項）] 核取方塊。](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. 接下來，移至瀏覽器中的 Azure 入口網站，並搜尋「影像」。 您的搜尋應該會引導您到 [**建立映射**] 頁面，如下列螢幕擷取畫面所示：

    ![Azure 入口網站的 [建立影像] 頁面的螢幕擷取畫面，其中填入了影像的範例值。](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. 建立映射之後，您應該會看到類似下列螢幕擷取畫面的通知：

    ![「已成功建立的影像」通知的螢幕擷取畫面。](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>後續步驟

現在您已有映射，您可以建立或更新主機集區。 若要深入瞭解如何建立和更新主機集區，請參閱下列文章：

- [使用 Azure Resource Manager 範本建立主機集區](create-host-pools-arm-template.md)
- [教學課程：使用 Azure Marketplace 建立主機集區](create-host-pools-azure-marketplace.md)
- [使用 PowerShell 建立主機集區](create-host-pools-powershell.md)
- [使用檔案共用建立主機集區的設定檔容器](create-host-pools-user-profile.md)
- [設定 Windows 虛擬桌面負載平衡方法](configure-host-pool-load-balancing.md)
