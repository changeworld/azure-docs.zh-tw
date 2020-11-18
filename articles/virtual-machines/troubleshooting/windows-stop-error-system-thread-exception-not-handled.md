---
title: Windows 停止錯誤-無法處理0x0000007E 系統執行緒例外狀況
description: 本文提供的步驟可解決虛擬作業系統遇到問題，並想要重新開機 Azure VM 的問題。 訊息會指出「未處理系統執行緒例外狀況」。
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/04/2020
ms.author: v-mibufo
ms.openlocfilehash: 1ce594d9e3ffddf781c61717ae4534f0c7bd40f8
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94681885"
---
# <a name="windows-stop-error---0x0000007e-system-thread-exception-not-handled"></a>Windows 停止錯誤-無法處理0x0000007E 系統執行緒例外狀況

本文提供的步驟可解決客體作業系統 (客體作業系統) 遇到問題，並嘗試重新開機 Azure 虛擬機器 (VM) 的問題。 顯示的錯誤訊息會顯示「未處理系統執行緒例外狀況」。

## <a name="symptoms"></a>徵兆

當您使用 [開機診斷](./boot-diagnostics.md) 來查看 VM 輸出的螢幕擷取畫面時，您會看到 Windows 需要使用「系統執行緒例外狀況未處理」停止程式碼或「0x0000007e」錯誤碼來重新開機。

![顯示 Windows 在開機期間停滯的螢幕擷取畫面，其中包含「您的電腦遇到問題，需要重新開機。 我們會為您重新開機。」 錯誤訊息和「系統執行緒例外狀況未處理」停止程式碼。](media/windows-stop-error-system-thread-exception-not-handled/windows-stop-error-system-thread-exception-not-handled-1.png)

## <a name="cause"></a>原因

在分析記憶體傾印檔案之前無法判斷原因。 繼續收集記憶體傾印檔案。

## <a name="solution"></a>解決方案

若要解決此問題，您必須先收集損毀的記憶體傾印檔案，然後將檔案傳送給 Microsoft 支援服務。 若要收集傾印檔案，請遵循接下來兩節中的指示。

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>將 OS 磁片連結至新的修復 VM

1. 若要準備修復 VM，請遵循 [vm repair 命令](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md)的步驟1-3。
1. 使用遠端桌面連線連接到修復 VM。

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>找出傾印檔案並提交支援票證

1. 在修復 VM 上，移至連接的 OS 磁片上的 Windows 資料夾。 例如，如果指派給已連結 OS 磁片的磁碟機號標示為 *F*，請移至 `F:\Windows` 。
1. 尋找 *記憶體 dmp* 檔案，然後 [提交支援票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 並附上記憶體傾印檔案。
1. 如果找不到 *記憶體 dmp* 檔案的問題，請遵循指南， [使用不可遮罩的插斷 (NMI) 呼叫來產生損毀](/windows/client-management/generate-kernel-or-complete-crash-dump)傾印檔案。

如需有關 NMI 呼叫的詳細資訊，請參閱 [Azure 序列主控台使用者指南中的 nmi 呼叫](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) 。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [針對 Azure 虛擬機器開機錯誤進行疑難排解](./boot-error-troubleshoot.md)
