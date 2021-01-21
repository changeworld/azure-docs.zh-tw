---
title: 作業系統啟動-電腦意外重新開機或發生未預期的錯誤
description: 本文提供的步驟可解決 VM 在安裝 Windows 時遇到未預期的重新開機或錯誤的問題。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 1d249a4e-71ba-475d-8461-31ff13e57811
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/22/2020
ms.author: v-mibufo
ms.openlocfilehash: d8d2ab2bb3f24e1faa4791ebdc1ce3852f6a790e
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632685"
---
# <a name="os-start-up--computer-restarted-unexpectedly-or-encountered-an-unexpected-error"></a>作業系統啟動-電腦意外重新開機或發生未預期的錯誤

本文提供的步驟可解決在安裝 Windows 時，虛擬機器 (VM) 遇到未預期的重新開機或錯誤的問題。

## <a name="symptom"></a>徵狀

當您使用 [開機診斷](./boot-diagnostics.md) 來查看 VM 的螢幕擷取畫面時，您會看到螢幕擷取畫面顯示 Windows 安裝失敗，並出現下列錯誤：

**電腦未預期地重新開機，或發生未預期的錯誤。無法繼續進行 Windows 安裝。若要安裝 Windows，請按一下 [確定] 重新開機電腦，然後重新開機安裝。**

![Windows 安裝正在進行時發生錯誤：電腦未預期地重新開機，或發生未預期的錯誤。 無法繼續進行 Windows 安裝。 若要安裝 Windows，請按一下 [確定] 重新開機電腦，然後重新開機安裝。](./media/unexpected-restart-error-during-vm-boot/1.png)
 
![Windows 安裝安裝程式啟動服務時發生錯誤：電腦未預期地重新開機，或發生未預期的錯誤。 無法繼續進行 Windows 安裝。 若要安裝 Windows，請按一下 [確定] 重新開機電腦，然後重新開機安裝。](./media/unexpected-restart-error-during-vm-boot/2.png)

## <a name="cause"></a>原因

電腦正在嘗試進行 [一般化映射](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation)的初始開機，但因為自訂回應檔案 ( # A0) 正在處理，所以遇到問題。 **Azure 不支援自訂回應** 檔。 

回應檔案是特殊的 XML 檔案，其中包含您想要在安裝 Windows Server 作業系統期間自動化的設定設定和值。 設定選項包括有關如何分割磁片、要在哪裡尋找要安裝的 Windows 映像、要套用的產品金鑰，以及您想要執行的其他命令的指示。

同樣地，Azure 中不支援自訂回應檔案。 因此，當映射準備好在 Azure 中使用時，就會發生這種情況，但您可以使用 **SYSPREP** 搭配類似下列命令的旗標來指定自訂 Unattend.xml 檔案：

`sysprep /oobe /generalize /unattend:<your file’s name> /shutdown`

在 Azure 中，請使用 **系統準備工具 GUI** 中的 [**進入系統全新體驗 (OOBE)** ] 選項，或使用 `sysprep /oobe` 而非 Unattend.xml 檔。

當您使用 sysprep 搭配內部部署 VM 將一般化 VM 上傳至 Azure 時，最常建立此問題。 在此情況下，您可能也會想要如何正確上傳一般化 VM。

## <a name="solution"></a>解決方案

### <a name="do-not-use-unattendxml"></a>請勿使用 Unattend.xml

> [!TIP]
> 如果您有最新的 VM 備份，您可以嘗試 [從備份還原 vm](../../backup/backup-azure-arm-restore-vms.md) 以修正開機問題。

若要修正此問題，請遵循 [Azure 有關準備/捕獲映射的指引](../windows/upload-generalized-managed.md) ，並準備新的一般化映射。 在 sysprep 期間， **請勿使用 `/unattend:<your file’s name>` 旗** 標。 相反地，請只使用下列旗標：

`sysprep /oobe /generalize /shutdown`

- 預設體驗 (OOBE) 是 Azure Vm 的支援設定。

您也可以使用 **系統準備工具 GUI** 來完成與上述命令相同的工作，方法是選取如下所示的選項：

- 進入全新體驗
- 一般化
- 關機
 
![已選取 OOBE、一般化和關機選項的系統準備工具視窗。](./media/unexpected-restart-error-during-vm-boot/3.png)
