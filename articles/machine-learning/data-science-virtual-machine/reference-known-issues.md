---
title: 參考： & 疑難排解的已知問題
titleSuffix: Azure Data Science Virtual  Machine
description: 取得 Azure 資料科學虛擬機器的已知問題、因應措施和疑難排解清單
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: b83effa95b17d712d4019f8ab5bf13c4f02a7d2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "78206515"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Azure 資料科學虛擬機器的已知問題和疑難排解

本文可協助您尋找並修正使用 Azure 資料科學虛擬機器時可能會遇到的錯誤或失敗。

## <a name="python-package-installation-issues"></a>Python 套件安裝問題

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>在 Linux 上安裝具有 pip 中斷相依性的套件

`sudo pip install` `pip install` 在安裝封裝時使用而不是。

## <a name="disk-encryption-issues"></a>磁碟加密問題

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Ubuntu DSVM 上的磁片加密失敗

Ubuntu DSVM 目前不支援 Azure 磁碟加密 (ADE) 。 若要解決此問題，請考慮設定 [Azure 受控磁片的伺服器端加密](../../virtual-machines/windows/disk-encryption.md)。

## <a name="tool-appears-disabled"></a>工具會顯示為已停用

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Hyper-v 無法在 Windows DSVM 上運作

Hyper-v 最初無法在 Windows 上運作是預期的行為。 針對開機效能，我們停用了某些服務。 若要啟用 Hyper-v：

1. 開啟 Windows DSVM 上的搜尋列
1. 輸入「服務」
1. 將所有 Hyper-v 服務設定為 [手動]
1. 將 [Hyper-v 虛擬機器管理] 設定為 [自動]

您的最終畫面看起來應該像這樣：

   ![啟用 Hyper-V](./media/workaround/hyperv-enable-dsvm.png)

