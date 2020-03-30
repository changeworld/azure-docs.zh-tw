---
title: 如何復原已刪除的儲存體帳戶
description: 瞭解如何恢復已刪除的存儲帳戶
author: todmccoy
manager: dcscontentpm
ms.service: storage
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: rogarana
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: 05465d4a03335ac607ba8981116c66fd6dac9416
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252639"
---
# <a name="how-to-recover-a-deleted-storage-account"></a>如何復原已刪除的儲存體帳戶

Azure 存儲通過自動副本提供資料恢復能力，但不會阻止使用者或應用程式代碼意外或惡意損壞資料。 在應用程式或使用者錯誤發生期間保持資料逼真度需要更高級的技術，例如使用稽核記錄將資料複製到輔助存儲位置。

下表概述了存儲帳戶恢復的範圍，具體取決於複寫原則。

| |LRS|ZRS|GRS|RA - GRS|
|---|---|---|---|---|
|存儲帳戶 Azure 資源管理器|是|是|是|是|
|存儲帳戶經典|是|是|是|是|

收集以下資訊，並與 Microsoft 支援部門提交支援請求：

* 儲存體帳戶名稱
* 刪除日期
* 存儲帳戶區域
* 如何刪除存儲帳戶？
* 刪除存儲帳戶的方法是什麼？ （門戶、電源外殼等）

要點

* 存儲服務執行垃圾回收通常可能需要自刪除時間起最多 15 天，因此可能無法使用 SLA 恢復存儲帳戶恢復。
* Microsoft 支援將盡力恢復容器/帳戶，並且不能保證恢復。

> [!NOTE]
> 如果已重新創建帳戶，則恢復可能不成功。 如果您已重新創建帳戶，必須先將其刪除，然後才能嘗試恢復。
