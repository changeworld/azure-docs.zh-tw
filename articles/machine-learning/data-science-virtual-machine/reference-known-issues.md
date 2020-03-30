---
title: 參考：疑難排解&已知問題
titleSuffix: Azure Data Science Virtual  Machine
description: 獲取 Azure 資料科學虛擬機器的已知問題、解決方法和故障排除的清單
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: b83effa95b17d712d4019f8ab5bf13c4f02a7d2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206515"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>已知問題和 Azure 資料科學虛擬機器的故障排除

本文可説明您查找和更正使用 Azure 資料科學虛擬機器時可能會遇到的錯誤或故障。

## <a name="python-package-installation-issues"></a>Python 套裝軟體安裝問題

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>安裝具有 pip 中斷對 Linux 依賴項的套裝軟體

在`sudo pip install`安裝包`pip install`時使用而不是使用。

## <a name="disk-encryption-issues"></a>磁碟加密問題

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Ubuntu DSVM 上的磁片加密失敗

Ubuntu DSVM 當前不支援 Azure 磁片加密 （ADE）。 作為解決方法，請考慮配置 Azure[託管磁片的伺服器端加密](../../virtual-machines/windows/disk-encryption.md)。

## <a name="tool-appears-disabled"></a>工具顯示為禁用

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Hyper-V 在 Windows DSVM 上不起作用

Hyper-V 最初在 Windows 上不起作用是預期行為。 為了獲得啟動性能，我們禁用了某些服務。 要啟用 Hyper-V：

1. 打開 Windows DSVM 上的搜索欄
1. 鍵入"服務"
1. 將所有 Hyper-V 服務設置為"手動"
1. 將"Hyper-V 虛擬機器管理"設置為"自動"

您的最終螢幕應如下所示：

   ![啟用 Hyper-V](./media/workaround/hyperv-enable-dsvm.png)

