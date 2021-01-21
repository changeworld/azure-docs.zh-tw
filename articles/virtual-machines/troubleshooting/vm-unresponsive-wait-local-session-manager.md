---
title: 在等候本機會話管理員服務時，VM 沒有回應
description: 本文提供的步驟可解決當虛擬作業系統正在將 Azure VM 開機時，處於等待本機會話管理員完成處理的問題。
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
ms.openlocfilehash: fc3bd5d2590e969db07e9dffa61b4902ea4604c3
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632541"
---
# <a name="vm-is-unresponsive-while-waiting-for-the-local-session-manager-service"></a>在等候本機會話管理員服務時，VM 沒有回應

本文提供的步驟可解決下列問題：當客體作業系統 (客體作業系統) 在將 Azure 虛擬機器 (VM) 開機時，會停滯于等待本機會話管理員完成處理。

## <a name="symptoms"></a>徵兆

當您使用 [開機診斷](./boot-diagnostics.md) 來查看 VM 輸出的螢幕擷取畫面時，您會看到螢幕擷取畫面顯示出現「請等候本機會話管理員」訊息的提示。

![螢幕擷取畫面，顯示 Windows Server 2012 R2 中停滯的來賓 OS，並出現「請等候本機會話管理員」訊息。](media/vm-unresponsive-wait-local-session-manager/vm-unresponsive-wait-local-session-manager-1.png)

## <a name="cause"></a>原因

有幾個原因會導致 VM 停滯在等候本機會話管理員。 如果此問題持續發生，您需要收集記憶體傾印以進行分析。

## <a name="solution"></a>解決方案

> [!TIP]
> 如果您有最新的 VM 備份，您可以嘗試 [從備份還原 vm](../../backup/backup-azure-arm-restore-vms.md) 以修正開機問題。

在某些情況下，只要等候完成的程式就能解決問題。 如果您的 VM 沒有回應，並在等候畫面上保持超過一小時，您應該收集記憶體傾印，然後聯絡 Microsoft 支援服務。

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>將 OS 磁片連結至新的修復 VM

1. 若要準備修復 VM，請遵循 [vm repair 命令](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md)的步驟1-3。
1. 使用遠端桌面連線連接到修復 VM。

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>找出傾印檔案並提交支援票證

1. 在修復 VM 上，移至連接的 OS 磁片上的 Windows 資料夾。 例如，如果指派給已連結 OS 磁片的磁碟機號標示為 *F*，請移至 `F:\Windows` 。
1. 尋找 *記憶體 dmp* 檔案，然後 [提交支援票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 並附上記憶體傾印檔案。
1. 如果找不到 *記憶體 dmp* 檔案的問題，請遵循指南， [使用不可遮罩的插斷 (NMI) 呼叫來產生損毀](/windows/client-management/generate-kernel-or-complete-crash-dump)傾印檔案。

如需有關 NMI 呼叫的詳細資訊，請參閱 [Azure 序列主控台使用者指南中的 nmi 呼叫](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) 。

## <a name="next-steps"></a>下一步

> [!div class="nextstepaction"]
> [針對 Azure 虛擬機器開機錯誤進行疑難排解](boot-error-troubleshoot.md)