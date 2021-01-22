---
title: Windows 停止錯誤狀態系統進程已終止
description: 本文提供的步驟可解決作業系統遇到停止錯誤0xC000021A 的問題，這會導致無法啟動 Azure 虛擬機器。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 037d0858-4611-4707-bd46-cc8085d011ed
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/21/2020
ms.author: v-mibufo
ms.openlocfilehash: 8af656e00c457dfa3d438f0cb104d85ccc687745
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661233"
---
# <a name="windows-stop-error---0xc000021a-status-system-process-terminated"></a>Windows 停止錯誤-0xC000021A 狀態系統進程已終止

本文提供的步驟可解決作業系統 (OS) 遇到停止錯誤0xC000021A 的問題，這會讓 Azure 虛擬機器 (VM) 開機。

## <a name="symptom"></a>徵狀

當您使用 [開機診斷](./boot-diagnostics.md) 來查看 VM 的螢幕擷取畫面時，螢幕擷取畫面會顯示作業系統在開機期間發生錯誤的訊息，並顯示下列訊息：

**您的電腦遇到問題，需要重新開機。我們只是收集一些錯誤資訊，然後您可以重新開機。 ( # #% 完成) 如果您想要深入瞭解，您可以稍後在線上搜尋此錯誤： 0xC000021a**。

  ![[圖 1] 顯示錯誤代碼 #0xC000021A 訊息「您的電腦遇到問題，需要重新開機。 我們只是收集一些錯誤資訊，然後您可以重新開機。」](./media/windows-stop-error-system-process-terminated/1-pc-problem-restart.png)

## <a name="cause"></a>原因

錯誤0xC000021A 表示 **STATUS_SYSTEM_PROCESS_TERMINATED**。

當重要的進程（例如 WinLogon ( # A0) 或用戶端伺服器 Run-Time 子系統 ( # A1) 失敗）時，就會發生此錯誤。 一旦核心偵測到其中一個服務已停止，就會引發停止的 **0xc000021a** 錯誤。 此錯誤可能有數個原因，包括：

- 已安裝不相符的系統檔案。
- Service Pack 或 KB 更新安裝失敗。
- 用來還原硬碟的備份程式未正確還原可能已在使用中的檔案。
- 已安裝不相容的協力廠商程式。

## <a name="solution"></a>解決方案

### <a name="collect-the-memory-dump-file"></a>收集記憶體傾印檔案

> [!TIP]
> 如果您有最新的 VM 備份，您可以嘗試 [從備份還原 vm](../../backup/backup-azure-arm-restore-vms.md) 以修正開機問題。


若要解決這個問題，將需要分析損毀傾印。 收集損毀和連絡人支援的記憶體傾印檔案。 若要收集傾印檔案，請遵循下列步驟：

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>將 OS 磁片連結至新的修復 VM

1.  使用 [VM 修復命令](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) \(部分機器翻譯\) 的步驟 1-3 準備修復 VM。
2.  使用 **遠端桌面連線** 連接到修復 VM。

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>找出傾印檔案並提交支援票證

1.  在修復 VM 上，移至連接的 OS 磁片中的 [windows] 資料夾。 如果指派給已連結 OS 磁片的磁碟機號是 F，請移至 F:\Windows。
2.  找出記憶體 dmp 檔案，然後使用記憶體傾印檔案來 [提交支援票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 。
3.  如果您找不到找出記憶體 dmp 檔案的問題，您可能會想要改為 [在序列主控台中使用非遮罩式插斷 (NMI) 呼叫](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) 。 您可以遵循本指南，[在這裡使用 NMI 呼叫來產生損毀傾印檔案](/windows/client-management/generate-kernel-or-complete-crash-dump)。

## <a name="next-steps"></a>後續步驟

- 如需進一步的疑難排解資訊，請參閱針對 [常見的開機錯誤進行疑難排解](./boot-error-troubleshoot.md) ，或 [將 OS 磁片連結至復原 VM，以針對 Windows VM 進行疑難排解](./troubleshoot-recovery-disks-windows.md)。 您也應該熟悉 [如何使用開機診斷來針對虛擬機器進行疑難排解](./boot-diagnostics.md)。
- 如需使用 Resource Manager 的詳細資訊，請參閱 [Azure Resource Manager 概觀](../../azure-resource-manager/management/overview.md)。
- 如果您無法連線至您的 VM，請參閱針對 [AZURE VM 的 RDP 連線進行疑難排解](./troubleshoot-rdp-connection.md)。