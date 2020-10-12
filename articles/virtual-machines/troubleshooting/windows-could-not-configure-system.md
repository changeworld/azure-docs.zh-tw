---
title: 針對 Windows 無法完成系統設定進行疑難排解
titlesuffix: Azure Virtual Machines
description: 本文提供的步驟可解決 Sysprep 程式無法啟動 Azure VM 的問題。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5fc57a8f-5a0c-4b5f-beef-75eecb4d310d
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/09/2020
ms.author: v-miegge
ms.openlocfilehash: 9c170607d6300c4921285e85ac78db5a8a18ad9b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90078801"
---
# <a name="troubleshoot-windows-could-not-finish-configuring-the-system"></a>針對 Windows 無法完成系統設定進行疑難排解

本文提供的步驟可解決 Sysprep 程式無法將 Azure 虛擬機器 (VM) 啟動的問題。

## <a name="symptom"></a>徵狀

當您使用 [開機診斷](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) 來查看 VM 的螢幕擷取畫面時，您會在 Windows 安裝程式啟動服務時看到螢幕擷取畫面顯示安裝 windows 錯誤。 錯誤會顯示下列訊息：

`Windows could not finish configuring the system. To attempt to resume configuration, restart the computer. Setup is starting services`

  ![[圖 1] 顯示的是安裝 Windows 錯誤訊息：「Windows 無法完成設定系統。 若要嘗試繼續設定，請重新開機電腦。 安裝程式正在啟動服務」](./media/windows-could-not-configure-system/1-windows-error-configure.png)

## <a name="cause"></a>原因

當作業系統 (OS) 無法完成 [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-process-overview)程式時，就會導致此錯誤。 當您嘗試進行一般化 VM 的初始開機時，將會發生此錯誤。 如果您遇到此問題，請重新建立一般化映射，因為映射處於無法部署的狀態，且無法復原。

## <a name="solution"></a>解決方法

若要修正此問題，請遵循 [Azure 有關準備/捕獲映射的指引](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed) ，並準備新的一般化映射。
