---
title: 參考:資料科學 虛擬機器映像
titleSuffix: Azure Data Science Virtual Machine
description: 有關影響 Azure 資料科學虛擬機器的棄用的詳細資訊
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 04/03/2020
ms.topic: reference
ms.openlocfilehash: 6004ae0c1fd2fa25a07ab84776ab74789f6e7da9
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754762"
---
# <a name="reference-deprecation-of-dsvm-images"></a>參考:DSVM 映像的棄用

下面我們將討論有關處理 Azure 數據科學虛擬機上即將進行的棄用的建議。

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012:移植資料磁碟

我們將在 2019 年 12 月 31 日停止支援 Windows 2012 DSVM 映射。 為了將資料磁碟從現有 Windows 2012 DSVM 遷移到 Windows 2016 DSVM,請執行以下步驟:

1. 按照[此處](./provision-vm.md#create-your-dsvm)顯示的說明創建新的 Windows 2016 DSVM。
1. 使用這些[說明](../../virtual-machines/windows/detach-disk.md)從 Windows 2012 映射中刪除現有數據磁碟。
1. 使用這些[說明](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm)將上一步的磁碟附加到 Windows 2016 映射。

## <a name="centos"></a>CentOS

新使用者應使用最新的 Ubuntu 或 Windows 映射。 CentOS將繼續可用於現有解決方案範本。