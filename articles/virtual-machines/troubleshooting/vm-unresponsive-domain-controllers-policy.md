---
title: 套用預設網域控制站原則時，VM 沒有回應
titlesuffix: Azure Virtual Machines
description: 本文提供的步驟可解決預設網域控制站原則防止 Azure VM 開機的問題。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 34e6b765-3496-46a1-b7d7-6def00884394
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/08/2020
ms.author: v-miegge
ms.openlocfilehash: 53e1daca47a2917a19cbc30db5348e4fcc06b325
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2020
ms.locfileid: "90039014"
---
# <a name="vm-is-unresponsive-while-applying-default-domain-controllers-policy"></a>套用預設網域控制站原則時，VM 沒有回應

本文提供的步驟可解決下列問題：預設網域控制站原則會防止 (VM) 的 Azure 虛擬機器進行開機。

## <a name="symptom"></a>徵狀

當您使用 [開機診斷](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) 來查看 VM 的螢幕擷取畫面時，您會看到螢幕擷取畫面顯示 OS 在使用 [訊息 **預設網域控制站] 原則**開機時，會變成沒有回應。

  ![[圖 1] 顯示作業系統卡在訊息「預設網域控制站原則」](./media/vm-unresponsive-domain-controllers-policy/1-default-domain-controllers-policy.png)

## <a name="cause"></a>原因

此問題可能是因為對預設網域控制站原則所做的最新變更所造成。 否則，將需要執行記憶體傾印檔案分析，以判斷根本原因。

## <a name="solution"></a>解決方案

如果您最近對預設網域控制站原則進行了變更，您可能會想要復原這些變更以修正問題。 如果您不確定造成問題的原因，請收集記憶體傾印，然後提交支援票證。

### <a name="collect-the-memory-dump-file"></a>收集記憶體傾印檔案

若要解決此問題，您應該先收集損毀的記憶體傾印檔案，然後將支援與記憶體傾印檔案聯絡。 若要收集傾印檔案，請遵循下列步驟：

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>將 OS 磁片連結至新的修復 VM

1. 使用 [VM 修復命令](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) \(部分機器翻譯\) 的步驟 1-3 準備修復 VM。

1. 使用遠端桌面連線連線至修復 VM。

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>找出傾印檔案並提交支援票證

1. 在修復 VM 上，移至連接的 OS 磁片中的 Windows 資料夾。 如果指派給已連結 OS 磁碟的磁碟機代號是 `F`，您必須移至 `F:\Windows`。

1. 找出記憶體 dmp 檔案，然後使用記憶體傾印檔案來 [提交支援票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 。

1. 如果您找不到找出記憶體 dmp 檔案的問題，您可能會想要改為 [在序列主控台中使用非遮罩式插斷 (NMI) 呼叫](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) 。 遵循指南， [使用 NMI 呼叫來產生損毀](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump)傾印檔案。
