---
title: Azure VM 啟動程序停滯於 Windows Update | Microsoft Docs
description: 了解如何在 Azure VM 的啟動程序停滯於 Windows Update 時進行疑難排解。
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: genli
ms.openlocfilehash: 8a47131cb4f19cce1664eafa50c67ab1a1171e67
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77919425"
---
# <a name="azure-vm-startup-is-stuck-at-windows-update"></a>Azure VM 啟動程序停滯於 Windows Update

本文將協助您解決虛擬機器 (VM) 在啟動期間停滯於 Windows Update 階段的問題。 


## <a name="symptom"></a>徵狀

 Windows 虛擬機器未啟動。 當您查看[開機診斷](../troubleshooting/boot-diagnostics.md)視窗中的螢幕擷取畫面時，您發現啟動作業停滯於更新程序中。 下列範例顯示您可能收到的訊息：

- 正在安裝 Windows ##%，請勿關閉電腦。 此作業需要一些時間，您的電腦將重新啟動數次
- 請保持開機狀態，直到此作業完成。 正在安裝更新 # (共 # 項)... 
- 我們無法完成更新，正在復原變更，請勿關閉電腦
- Windows 更新設定失敗，正在還原變更，請勿關閉電腦
- 套用更新作業 ##### (共 ##### 項) 時發生錯誤 <錯誤碼> (\Regist...)
- 套用更新作業 ##### (共 ##### 項) 時發生嚴重錯誤 <錯誤碼> ($$...)


## <a name="solution"></a>解決方案

執行更新程序可能需要一些時間，視要更新或回復的更新數而定。 請將 VM 保持在此狀態 8 小時。 如果 VM 在這段期間後仍處於此狀態，請從 Azure 入口網站重新啟動 VM，並確認是否正常啟動。 如果此步驟無法解決問題，請嘗試下列解決方案。

### <a name="remove-the-update-that-causes-the-problem"></a>移除造成問題的更新

1. 擷取受影響虛擬機器作業系統磁碟的快照集作為備份。 如需詳細資訊，請參閱[擷取磁碟快照集](../windows/snapshot-copy-managed-disk.md)。 
2. [將 OS 磁片連結至復原 VM](troubleshoot-recovery-disks-portal-windows.md)。
3. 當 OS 磁碟連結至復原 VM 後，請執行 **diskmgmt.msc** 來開啟磁碟管理，並確認連結的磁碟是**連線**狀態。 記下為已連結而包含 \windows 資料夾的 OS 磁碟指派的磁碟機代號。 如果該磁碟已加密，請先將其解密，再繼續進行本文件中的後續步驟。

4. 開啟提升權限的命令提示字元執行個體 (以系統管理員身分執行)。 執行下列命令以取得連結的 OS 磁碟上包含的更新套件清單：

        dism /image:<Attached OS disk>:\ /get-packages > c:\temp\Patch_level.txt

    例如，若連結的 OS 磁碟是磁碟機 F，請執行下列命令：

        dism /image:F:\ /get-packages > c:\temp\Patch_level.txt
5. 開啟 C:\temp\Patch_level.txt 檔案，然後由下至上閱讀。 找出處於 [安裝擱置中]**** 或 [解除安裝擱置中]**** 狀態的更新。  以下是更新狀態的範例：

     ```
    Package Identity : Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    State : Install Pending
    Release Type : Security Update
    Install Time :
    ```
6. 移除造成問題的更新：
    
    ```
    dism /Image:<Attached OS disk>:\ /Remove-Package /PackageName:<PACKAGE NAME TO DELETE>
    ```
    範例： 

    ```
    dism /Image:F:\ /Remove-Package /PackageName:Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    ```

    > [!NOTE] 
    > 根據套件的大小，DISM 工具將需要一些時間來處理解除安裝程序。 此程序通常會在 16 分鐘內完成。

7. [將 OS 磁碟中斷連結並建立 VM](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk)。 然後檢查問題是否已解決。
