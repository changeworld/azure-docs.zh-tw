---
title: 包含檔案
description: 檔案
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: emuench
ms.author: mingshen
ms.date: 10/09/2020
ms.openlocfilehash: f4e34e850391696506beed9f6f386f85528dff24
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283894"
---
如果您選取其中一個預先設定作業系統的 Vm (以及選用的額外服務) ，則您已挑選標準的 Azure VM 大小。 建議使用預先設定的作業系統啟動您的解決方案。 不過，如果您要手動安裝 OS，您必須在 VM 映射中調整主要 VHD 的大小。 確定 OS 磁片大小是在 Linux 或 Windows 的限制內。

| OS | VHD 大小 |
| --- | --- |
| Linux | 30至 1023 GB |
| Windows | 30至 250 GB |
|

以核准的基底形式提供的基底 Windows 或 SQL Server 映射已符合這些需求，因此請勿變更 VHD 的格式或大小。

資料磁碟的大小可高達 1 TB。 決定大小時，請記得客戶無法於部署時在映像中調整 VHD 大小。 將資料磁片 Vhd 建立為固定格式的 Vhd。 它們也應該可以展開 (的稀疏/動態) 。 初始資料磁碟可為空白，也可以含有資料。