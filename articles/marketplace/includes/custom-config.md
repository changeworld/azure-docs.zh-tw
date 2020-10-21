---
title: 包含檔案
description: 檔案
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: emuench
ms.author: mingshen
ms.date: 10/09/2020
ms.openlocfilehash: a1bc7cf1fd339ca3660c7b39326f37d2763c74b2
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283901"
---
如果您需要額外的設定，請使用在啟動時執行的排程工作，在 VM 部署完成後進行最後的變更。 並請考慮下列項目：

- 若為執行一次的工作，則此工作應該在順利完成後自行刪除。
- 設定不應依賴 C 或 D 以外的磁碟機，因為只有這兩個磁碟機一律保證會存在 (磁碟機 C 是作業系統磁碟，而磁碟機 D 則是暫存本機磁碟)。

如需 Linux 自訂項目的詳細資訊，請參閱[適用於 Linux 的虛擬機器擴充功能和功能](../../virtual-machines/extensions/features-linux.md)。