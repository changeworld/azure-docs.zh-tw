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
ms.openlocfilehash: d022b1124146a1e506401e6cee257805e3a38fd3
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526539"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Azure 資料科學虛擬機器的已知問題和疑難排解

本文可協助您找出並修正使用 Azure 資料科學虛擬機器時所遇到的錯誤或失敗。

## <a name="python-package-installation-issues"></a>Python 套件安裝問題

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>在 Linux 上安裝具有 pip 中斷相依性的套件

安裝套件時，請使用 `sudo pip install` 而不是 `pip install`。

## <a name="disk-encryption-issues"></a>磁片加密問題

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Ubuntu DSVM 上的磁片加密失敗

Ubuntu DSVM 目前不支援 Azure 磁碟加密（ADE）。 因應措施是考慮[使用客戶管理的金鑰來設定 Azure 儲存體加密](../../storage/common/storage-encryption-keys-portal.md)。

## <a name="tool-appears-disabled"></a>工具顯示為停用

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Hyper-v 無法在 Windows DSVM 上運作

這是預期的行為，如同開機效能，我們已停用某些服務。 若要重新啟用，請在您的 Windows DSVM 上開啟搜尋列，輸入「服務」，然後將所有 Hyper-v 服務設定為「手動」，並將「Hyper-v 虛擬機器管理」設定為「自動」。

最後的畫面看起來應該像這樣：

   ![啟用 Hyper-V](./media/workaround/hyperv-enable-dsvm.png)

