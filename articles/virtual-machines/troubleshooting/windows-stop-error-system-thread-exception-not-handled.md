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
ms.openlocfilehash: dd18d69009b9c150c4c12a755e9060cd5dfaccae
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424036"
---
# <a name="windows-stop-error---0x0000007e-system-thread-exception-not-handled"></a>Windows 停止錯誤-無法處理0x0000007E 系統執行緒例外狀況

本文提供的步驟可解決虛擬作業系統遇到問題，並想要重新開機 Azure VM 的問題。 訊息會指出「未處理系統執行緒例外狀況」。

## <a name="symptoms"></a>徵兆

當您使用 [開機診斷](./boot-diagnostics.md) 來查看 VM 的螢幕擷取畫面時，您會看到螢幕擷取畫面顯示 Windows 需要重新開機，而不是處理停止程式碼 **系統執行緒例外** 狀況或錯誤碼 **0x0000007e** 。

![螢幕擷取畫面顯示 Windows 在開機期間停滯，並顯示下列訊息：「您的電腦遇到問題，需要重新開機。 我們會為您重新開機。」 停止程式碼：「系統執行緒例外狀況未處理」](media/windows-stop-error-system-thread-exception-not-handled/windows-stop-error-system-thread-exception-not-handled-1.png)

## <a name="cause"></a>原因

在分析記憶體傾印檔案之前無法判斷原因。 繼續收集記憶體傾印檔案。

## <a name="solution"></a>解決方案

### <a name="collect-the-memory-dump-file"></a>收集記憶體傾印檔案

若要解決此問題，您必須先收集損毀的記憶體傾印檔案，然後再以記憶體傾印檔案的支援來聯絡。 若要收集傾印檔案，請遵循下列步驟：

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>將 OS 磁片連結至新的修復 VM

1. 請依照 [VM 修復命令的步驟 1-3](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) 來準備修復 VM。
2. 使用遠端桌面連線連接到修復 VM。

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>找出傾印檔案並提交支援票證

1. 在修復 VM 上，移至連接的 OS 磁片中的 Windows 資料夾。 如果指派給已連結 OS 磁片的磁碟機號標示為 *F* ，則您必須移至 `F:\Windows` 。
2. 找出 **記憶體 dmp** 檔案，然後使用記憶體傾印檔案來 [提交支援票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 。
3. 如果找不到 **記憶體 dmp** 檔案的問題，請遵循指南， [使用非遮罩式插斷 (NMI) 呼叫來產生損毀](/windows/client-management/generate-kernel-or-complete-crash-dump)傾印檔案。

如需有關 NMI 呼叫的詳細資訊，請參閱 [序列主控台使用者指南中的不可遮罩的插斷 (NMI) 呼叫](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) 。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [針對 Azure 虛擬機器開機錯誤進行疑難排解](./boot-error-troubleshoot.md)