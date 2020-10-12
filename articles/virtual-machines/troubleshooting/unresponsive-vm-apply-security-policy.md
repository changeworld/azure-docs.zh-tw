---
title: 將安全性原則套用至系統時，Azure VM 沒有回應
description: 本文提供的步驟可解決當 VM 在 Azure VM 中將安全性原則套用至系統時，負載畫面停滯的問題。
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5629
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 06/15/2020
ms.author: v-mibufo
ms.openlocfilehash: 6b50bffd1a44c0cf53f15650f5ff4d938f45df4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84907975"
---
# <a name="azure-vm-is-unresponsive-while-applying-security-policy-to-the-system"></a>將安全性原則套用至系統時，Azure VM 沒有回應

本文提供的步驟可解決作業系統在 Azure VM 中套用安全性原則時，會停止回應並變得沒有回應的問題。

## <a name="symptoms"></a>徵兆

當您使用 [開機診斷](boot-diagnostics.md) 來查看 VM 的螢幕擷取畫面時，您會看到螢幕擷取畫面顯示 OS 在開機時停滯，並顯示下列訊息：

> 「將安全性原則套用至系統」。

:::image type="content" source="media/unresponsive-vm-apply-security-policy/apply-policy.png" alt-text="Windows Server 2012 R2 啟動畫面的螢幕擷取畫面已停滯。":::

:::image type="content" source="media/unresponsive-vm-apply-security-policy/apply-policy-2.png" alt-text="Windows Server 2012 R2 啟動畫面的螢幕擷取畫面已停滯。":::

## <a name="cause"></a>原因

此問題的可能原因有眾多。 在執行記憶體傾印分析之前，您將無法知道來源。

## <a name="resolution"></a>解決方案

### <a name="process-overview"></a>程序概觀

1. [建立及存取修復 VM](#create-and-access-a-repair-vm)
2. [啟用序列主控台和記憶體傾印收集](#enable-serial-console-and-memory-dump-collection)
3. [重建 VM](#rebuild-the-vm)
4. [收集記憶體傾印檔案](#collect-the-memory-dump-file)

### <a name="create-and-access-a-repair-vm"></a>建立和存取修復 VM

1. 使用 [VM 修復命令的步驟 1-3](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) 準備修復 VM。
2. 使用遠端桌面連線連接至修復 VM。

### <a name="enable-serial-console-and-memory-dump-collection"></a>啟用序列主控台和記憶體傾印收集

若要啟用記憶體傾印集合和序列主控台，請執行下列指令碼：

1. 開啟提升權限的命令提示字元工作階段 (以系統管理員身分執行)。
2. 列出 BCD 存放區資料，並判斷您將在下一個步驟中使用的開機載入器識別碼。

     1. 針對第1代 VM，請輸入下列命令，並記下列出的識別碼：

        ```console
        bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
        ```

        在命令中， \<BOOT PARTITON> 將取代為連接磁片中包含開機資料夾之磁碟分割的字母。

        :::image type="content" source="media/unresponsive-vm-apply-security-policy/store-data.png" alt-text="Windows Server 2012 R2 啟動畫面的螢幕擷取畫面已停滯。" /v NMICrashDump /t REG_DWORD /d 1 /f
        ```

        卸載中斷的 OS 磁碟：

        ```console
        REG UNLOAD HKLM\BROKENSYSTEM
        ```

### <a name="rebuild-the-vm"></a>重建 VM

使用 [VM 修復命令的步驟 5](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) 重新組裝 VM。

### <a name="collect-the-memory-dump-file"></a>收集記憶體傾印檔案

若要解決這個問題，您需要先收集記憶體傾印檔案，以取得損毀，並支援記憶體傾印檔案。 若要收集傾印檔案，請遵循下列步驟：

1. 將 OS 磁片連結至新的修復 VM：

    - 使用 [VM Repair 命令的步驟 1-3](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) 來準備新的修復 VM。
    - 使用遠端桌面連線連接至修復 VM。

2. 找出傾印檔案，並提交支援票證：

    - 在修復 VM 上，移至連接的 OS 磁片中的 [windows] 資料夾。 如果指派給已連結 OS 磁碟的磁碟機代號是 `F`，您必須移至 `F:\Windows`。
    - 找出記憶體 dmp 檔案，然後使用記憶體傾印檔案來 [提交支援票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 。
    - 如果您找不到找出記憶體 dmp 檔案的問題，您可能會想要改為 [在序列主控台中使用非遮罩式插斷 (NMI) 呼叫](serial-console-windows.md#use-the-serial-console-for-nmi-calls) 。 您可以遵循本指南， [使用 NMI 呼叫來產生損毀](/windows/client-management/generate-kernel-or-complete-crash-dump)傾印檔案。

## <a name="next-steps"></a>接下來的步驟

如果您在套用本機使用者和群組原則時遇到問題，請參閱套用 [群組原則本機使用者和群組原則時，VM 沒有回應](unresponsive-vm-apply-group-policy.md)