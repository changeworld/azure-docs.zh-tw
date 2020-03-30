---
title: 參考：資料科學 虛擬機器映射棄用
titleSuffix: Azure Data Science Virtual Machine
description: 有關影響 Azure 資料科學虛擬機器的棄用的詳細資訊
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 71ce8519dca6fa71340b1c34fe3a6891ccf5753f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77525782"
---
# <a name="reference-deprecation-of-dsvm-images"></a>參考：DSVM 映射的棄用

下面我們將討論有關處理 Azure 資料科學虛擬機器上即將進行的棄用的建議。

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012：遷移資料磁片

我們將在 2019 年 12 月 31 日停止支援 Windows 2012 DSVM 映射。 為了將資料磁片從現有 Windows 2012 DSVM 遷移到 Windows 2016 DSVM，請執行以下步驟：

1. 按照[此處](./provision-vm.md#create-your-dsvm)顯示的說明創建新的 Windows 2016 DSVM。
1. 使用這些[說明](../../virtual-machines/windows/detach-disk.md)從 Windows 2012 映射中刪除現有資料磁片。
1. 使用這些[說明](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm)將上一步的磁片附加到 Windows 2016 映射。
