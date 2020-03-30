---
title: 準備和自訂主 VHD 映射 - Azure
description: 如何準備、自訂 Windows 虛擬桌面主映射並將其上載到 Azure。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 867b327ac25d51cd3955e622da9d8067ae6d9ae9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127719"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>準備和自訂主要 VHD 映像

本文介紹如何準備用於上載到 Azure 的主虛擬硬碟 （VHD） 映射，包括如何創建虛擬機器 （VM） 並在其中安裝軟體。 這些指示適用於可搭配您組織現有程序使用的 Windows 虛擬桌面特定設定。

## <a name="create-a-vm"></a>建立 VM

Windows 10 企業多會話在 Azure 映射庫中可用。 自訂此映射有兩個選項。

第一個選項是按照[從託管映射創建 VM](../virtual-machines/windows/create-vm-generalized-managed.md)中的說明在 Azure 中預配虛擬機器 （VM），然後跳到[軟體準備和安裝](set-up-customize-master-image.md#software-preparation-and-installation)。

第二個選項是通過下載映射、預配 Hyper-V VM 和自訂映射以滿足您的需要在本地創建映射，我們將在下一節仲介紹。

### <a name="local-image-creation"></a>本地映射創建

將映射下載到本地位置後，打開**Hyper-V 管理器**以創建具有複製的 VHD 的 VM。 以下說明是一個簡單的版本，但在[Hyper-V 中創建虛擬機器中](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v/)可以找到更詳細的說明。

要使用複製的 VHD 創建 VM，請：

1. 打開**新的虛擬機器嚮導**。

2. 在"指定生成"頁上，選擇**第 1 代**。

    ![指定生成頁的螢幕截圖。 選擇"第 1 代"選項。](media/a41174fd41302a181e46385e1e701975.png)

3. 在"檢查點類型"下，通過取消選中核取方塊來禁用檢查點。

    ![檢查點頁面的檢查點類型部分的螢幕截圖。](media/20c6dda51d7cafef33251188ae1c0c6a.png)

您還可以在 PowerShell 中運行以下 Cmdlet 以禁用檢查點。

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>固定磁片

如果從現有 VHD 創建 VM，預設情況下會創建動態磁碟。 可以通過選擇 **"編輯磁片..."** 將其更改為固定磁片， 如下圖所示。 有關更詳細的說明，請參閱準備[Windows VHD 或 VHDX 上載到 Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md)。

!["編輯磁片"選項的螢幕截圖。](media/35772414b5a0f81f06f54065561d1414.png)

您還可以運行以下 PowerShell Cmdlet 以將磁片更改為固定磁片。

```powershell
Convert-VHD –Path c:\\test\\MY-VM.vhdx –DestinationPath c:\\test\\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>軟體準備和安裝

本節介紹如何準備和安裝 FSLogix 和 Windows Defender，以及應用和映射註冊表的一些基本配置選項。 

如果要在 VM 上安裝 Office 365 ProPlus 和 OneDrive，請轉到[主 VHD 映射上的安裝 Office，](install-office-on-wvd-master-image.md)然後按照說明安裝應用。 完成後，返回到本文。

如果使用者需要訪問某些 LOB 應用程式，我們建議您在完成本節說明後安裝它們。

### <a name="set-up-user-profile-container-fslogix"></a>設置使用者設定檔容器 （FSLogix）

要將 FSLogix 容器包含在映射中，請按照[使用檔共用 為主機池創建設定檔容器](create-host-pools-user-profile.md#configure-the-fslogix-profile-container)的說明進行操作。 您可以使用[此快速入門](/fslogix/configure-cloud-cache-tutorial/)測試 FSLogix 容器的功能。

### <a name="configure-windows-defender"></a>配置 Windows 防禦器

如果在 VM 中配置了 Windows Defender，請確保它在附件期間未掃描 VHD 和 VHDX 檔的全部內容。

此配置僅在附件期間刪除 VHD 和 VHDX 檔的掃描，但不會影響即時掃描。

有關如何在 Windows 伺服器上配置 Windows Defender 的更多詳細資訊，請參閱[在 Windows 伺服器上配置 Windows Defender 防病毒排除](/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus/)項 。

要瞭解有關如何配置 Windows Defender 以從掃描中排除某些檔，請參閱[根據檔副檔名和資料夾位置配置和驗證排除項](/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus/)。

### <a name="disable-automatic-updates"></a>禁用自動更新

要通過本機群組策略禁用自動更新，請：

1. 打開**本機群組策略編輯器\\管理範本\\Windows\\元件 Windows 更新**。
2. 按右鍵 **"配置自動更新**"並將其設置為 **"已禁用**"。

您還可以在命令提示符上運行以下命令以禁用自動更新。

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>為 Windows 10 電腦指定"開始"佈局（可選）

運行此命令以為 Windows 10 電腦指定"開始"佈局。

```batch
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="set-up-time-zone-redirection"></a>設置時區重定向

可以在群組原則級別強制執行時區重定向，因為主機池中的所有 VM 都是同一安全性群組的一部分。

要重定向時區：

1. 在活動目錄伺服器上，打開**群組原則管理主控台**。
2. 展開域和群組原則物件。
3. 按右鍵為群組原則設置創建的**群組原則物件**，然後選擇"**編輯**"。
4. 在**群組原則管理編輯器**中，導航到**電腦配置** > **策略** > **管理範本** > **Windows 元件** > **遠端桌面服務** > **遠端桌面工作階段主機** > **設備和資源重定向**。
5. 啟用 **"允許時區重定向**"設置。

您還可以在主映射上運行此命令以重定向時區：

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>禁用存儲感知

對於使用 Windows 10 企業版或 Windows 10 企業多會話的 Windows 虛擬桌面工作階段主機，我們建議禁用存儲感知。 您可以在 **"存儲**"下的"設置"功能表中禁用"存儲感知"，如下圖所示：

!["設置"下的存儲功能表的螢幕截圖。 "存儲感知"選項已關閉。](media/storagesense.png)

還可以通過運行以下命令來更改註冊表設置：

```batch
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy" /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>包括其他語言支援

本文不介紹如何配置語言和區域支援。 如需詳細資訊，請參閱下列文章：

- [將語言新增至 Windows 映像](/windows-hardware/manufacture/desktop/add-language-packs-to-windows/)
- [按需功能](/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities/)
- [按需語言和區域功能 （FOD）](/windows-hardware/manufacture/desktop/features-on-demand-language-fod/)

### <a name="other-applications-and-registry-configuration"></a>其他應用程式和註冊表配置

本節介紹應用程式和作業系統配置。 本節中的所有配置都通過登錄機碼完成，登錄機碼可通過命令列和註冊工具執行。

>[!NOTE]
>您可以使用群組原則物件 （GPO） 或註冊表導入實現配置中的最佳做法。 管理員可以根據組織的要求選擇任一選項。

有關 Windows 10 企業多會話上遙測資料的回饋中樞集合，運行此命令：

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 3 /f
```

運行以下命令以修復 Watson 崩潰：

```batch
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

在登錄編輯程式中輸入以下命令以修復 5k 解析度支援。 必須先運行命令，然後才能啟用並行堆疊。

```batch
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxYResolution /t REG_DWORD /d 2880 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxYResolution /t REG_DWORD /d 2880 /f
```

## <a name="prepare-the-image-for-upload-to-azure"></a>準備映射以上載到 Azure

完成配置並安裝所有應用程式後，請按照[準備 Windows VHD 或 VHDX](../virtual-machines/windows/prepare-for-upload-vhd-image.md)中的說明上載到 Azure 以準備映射。

準備要上載的圖像後，請確保 VM 保持關閉或交易位置狀態。

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>將主映射上載到 Azure 中的存儲帳戶

此部分僅適用于在本地創建主映射時。

以下說明將告訴您如何將主映射上載到 Azure 存儲帳戶。 如果還沒有 Azure 存儲帳戶，請按照[本文](/azure/javascript/tutorial-vscode-static-website-node-03)中的說明創建一個帳戶。

1. 如果尚未修復，則將 VM 映射 （VHD） 轉換為"已修復"。 如果不將圖像轉換為"已修復"，則無法成功創建圖像。

2. 將 VHD 上載到存儲帳戶中的 Blob 容器。 您可以使用[存儲資源管理器工具](https://azure.microsoft.com/features/storage-explorer/)快速上傳。 要瞭解有關存儲資源管理器工具的更多資訊，請參閱[本文](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)。

    ![微軟 Azure 存儲資源管理器工具的搜索視窗的螢幕截圖。 選中"將 .vhd 或 vhdx 檔作為頁面 blob（推薦）"核取方塊選中。](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. 接下來，轉到瀏覽器中的 Azure 門戶並搜索"圖像"。 您的搜索應引導您訪問 **"創建圖像**"頁，如以下螢幕截圖所示：

    ![Azure 門戶的"創建圖像"頁的螢幕截圖，其中填充了圖像的示例值。](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. 創建映射後，您應該會看到如下螢幕截圖中的通知：

    !["已成功創建的圖像"通知的螢幕截圖。](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>後續步驟

現在，您有了映射，就可以創建或更新主機池。 要瞭解有關如何創建和更新主機池的詳細資訊，請參閱以下文章：

- [使用 Azure Resource Manager 範本建立主機集區](create-host-pools-arm-template.md)
- [教程：使用 Azure 應用商店創建主機池](create-host-pools-azure-marketplace.md)
- [使用 PowerShell 建立主機集區](create-host-pools-powershell.md)
- [使用檔案共用建立主機集區的設定檔容器](create-host-pools-user-profile.md)
- [設定 Windows 虛擬桌面負載平衡方法](configure-host-pool-load-balancing.md)
