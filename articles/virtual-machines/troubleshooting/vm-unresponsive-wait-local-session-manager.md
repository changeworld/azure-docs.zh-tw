---
title: 等候本機會話管理員時，VM 沒有回應
description: 本文提供的步驟可解決當虛擬作業系統在開機 Azure VM 時，處於等待本機會話管理員完成處理的問題。
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/22/2020
ms.author: v-mibufo
ms.openlocfilehash: ae3d35bea1c75c797447100316ceab9a27fcbe48
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424042"
---
# <a name="vm-is-unresponsive-while-waiting-for-the-local-session-manager"></a>等候本機會話管理員時，VM 沒有回應

本文提供的步驟可解決當虛擬機器在 (VM) 啟動 Azure 虛擬機器時，虛擬作業系統停滯在等候本機會話管理員完成處理的問題。

## <a name="symptoms"></a>徵兆

當您使用 [開機診斷](./boot-diagnostics.md) 來查看 VM 的螢幕擷取畫面時，您會看到螢幕擷取畫面顯示提示訊息：「 *請等候本機會話管理員* 」

![螢幕擷取畫面顯示來賓 OS 停滯于 Windows Server 2012 R2 中的「請等候本機會話管理員」訊息。](media/vm-unresponsive-wait-local-session-manager/vm-unresponsive-wait-local-session-manager-1.png)

## <a name="cause"></a>原因

有許多原因會導致虛擬機器停滯在等候本機會話管理員。 如果等候本機會話管理員是持續性的問題，您必須收集記憶體傾印以進行分析。

## <a name="solution"></a>解決方案

在某些情況下，只要等待足夠的時間來完成程式，就能解決您的問題。 如果您的 VM 在等候畫面上的等待時間超過一小時，您應該收集記憶體傾印，然後聯絡 Microsoft 支援服務。

### <a name="collect-the-memory-dump-file"></a>收集記憶體傾印檔案

若要解決此問題，您必須先收集損毀的記憶體傾印檔案，然後再以記憶體傾印檔案的支援來聯絡。 若要收集傾印檔案，請遵循下列步驟：

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>將 OS 磁片連結至新的修復 VM

1. 請依照 [VM 修復命令的步驟 1-3](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) 來準備修復 VM。
2. 使用遠端桌面連線連接到修復 VM。

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>找出傾印檔案並提交支援票證

1. 在修復 VM 上，移至連接的 OS 磁片中的 Windows 資料夾。 如果指派給已連結 OS 磁片的磁碟機號標示為 *F* ，則您必須移至 `F:\Windows` 。
2. 找出 **記憶體 dmp** 檔案，然後使用記憶體傾印檔案來 [提交支援票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 。
3. 如果找不到 **記憶體 dmp** 檔案的問題，請遵循指南， [使用非遮罩式插斷 (NMI) 呼叫來產生損毀](/windows/client-management/generate-kernel-or-complete-crash-dump)傾印檔案。

如需有關 NMI 呼叫的詳細資訊，請參閱 [序列主控台使用者指南中的非遮罩中斷 (NMI) 呼叫](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) 。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [針對 Azure 虛擬機器開機錯誤進行疑難排解](boot-error-troubleshoot.md)