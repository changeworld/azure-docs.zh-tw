---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e96d205ef1a8f94baa3a0cfe6c5127b6cf570e5a
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420977"
---
本文介紹如何使用 AzCopy 將 VHD 從本地電腦上載到 Azure 託管磁碟或將託管磁碟複製到其他區域。 此過程(直接上傳)還使您能夠將高達 32 TiB 大小的 VHD 直接上傳到託管磁碟中。 目前,標準硬碟、標準 SSD 和高級 SSD 託管磁碟支援直接上傳。 它不支援超磁碟。

如果要為 Azure 中的 IaaS VM 提供備份解決方案,我們建議使用直接上載將客戶備份還原到託管磁碟。 將 VHD 從外部源上傳到 Azure 時,速度將取決於本地頻寬。 從 Azure VM 上傳或複製時,您的頻寬將與標準 HDD 相同。