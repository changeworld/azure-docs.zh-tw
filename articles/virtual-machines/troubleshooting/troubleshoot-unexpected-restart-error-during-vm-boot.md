---
title: OS 啟動-電腦意外重新開機或發生未預期的錯誤
description: 本文提供的步驟可解決 VM 在安裝 Windows 時遇到非預期的重新開機或錯誤的問題。
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
ms.openlocfilehash: 186b1c46303be59e191a1754361e07a2003b997a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87036177"
---
# <a name="os-start-up--computer-restarted-unexpectedly-or-encountered-an-unexpected-error"></a>OS 啟動-電腦意外重新開機或發生未預期的錯誤

本文提供的步驟可解決虛擬機器（VM）在安裝 Windows 時遇到非預期的重新開機或錯誤的問題。

## <a name="symptom"></a>徵狀

當您使用 [[開機診斷](./boot-diagnostics.md)] 來查看 VM 的螢幕擷取畫面時，您會看到螢幕擷取畫面顯示 Windows 安裝失敗，並出現下列錯誤：

**電腦意外重新開機或發生未預期的錯誤。Windows 安裝無法繼續。若要安裝 Windows，請按一下 [確定] 重新開機電腦，然後重新開機安裝。**

![Windows 安裝正在進行時發生錯誤：電腦意外重新開機或發生未預期的錯誤。 Windows 安裝無法繼續。 若要安裝 Windows，請按一下 [確定] 重新開機電腦，然後重新開機安裝。](./media/unexpected-restart-error-during-vm-boot/1.png)
 
![Windows 安裝安裝程式啟動服務時發生錯誤：電腦意外重新開機或發生未預期的錯誤。 Windows 安裝無法繼續。 若要安裝 Windows，請按一下 [確定] 重新開機電腦，然後重新開機安裝。](./media/unexpected-restart-error-during-vm-boot/2.png)

## <a name="cause"></a>原因

電腦嘗試執行[一般化映射](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation)的初始開機，但因為處理的自訂回應檔案（unattend.xml）而遇到問題。 Azure 不支援自訂回應檔案。 

回應檔案是特殊的 XML 檔案，其中包含您想要在安裝 Windows Server 作業系統時自動執行的設定定義和值。 設定選項包括如何分割磁片、在何處尋找要安裝的 Windows 映像、要套用的產品金鑰，以及您想要執行的其他命令的指示。

在 Azure 中，不支援自訂回應檔案。 如果您使用選項來指定自訂**Unattend.xml**檔案 `sysprep /unattend:<your file’s name>` ，就會發生此錯誤。

在 Azure 中，使用**Sysprep.exe**中的 [**進入系統全新體驗（OOBE）** ] 選項，或使用， `sysprep /oobe` 而不是 Unattend.xml 檔案。

當您使用**Sysprep.exe**搭配內部部署 vm 來將一般化 vm 上傳至 Azure 時，通常會建立此問題。 在此情況下，您可能也會想要如何正確上傳一般化 VM。

## <a name="solution"></a>解決方案

### <a name="replace-unattended-answer-file-option"></a>取代自動回應檔案選項

當映射已準備好在 Azure 中使用，但它使用了 Azure 中不支援的自訂回應檔案，而且您已使用**SYSPREP**與類似下列命令的旗標時，就會發生這種情況：

`sysprep /oobe /generalize /unattend:<NameOfYourAnswerFile.XML> /shutdown`

- 在上一個命令中，將取代 `<NameOfYourAnswerFile.XML>` 為您的檔案名。

若要修正此問題，請遵循[Azure 指引來準備/捕獲映射](../windows/upload-generalized-managed.md)，並準備新的一般化映射。 在 sysprep 期間，請勿使用 `/unattend:<answerfile>` 旗標。 相反地，請只使用下列旗標：

`sysprep /oobe /generalize /shutdown`

- **全新體驗**（OOBE）是 Azure vm 的支援設定。

您也可以使用**系統準備工具 GUI**來完成與上述命令相同的工作，方法是選取如下所示的選項：

- 進入全新體驗
- 一般化
- Shutdown
 
![已選取 [OOBE]、[一般化] 和 [關機] 選項的 [系統準備工具] 視窗。](./media/unexpected-restart-error-during-vm-boot/3.png)
