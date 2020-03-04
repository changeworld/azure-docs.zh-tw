---
title: 如何復原已刪除的儲存體帳戶
description: 瞭解如何復原已刪除的儲存體帳戶
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
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252639"
---
# <a name="how-to-recover-a-deleted-storage-account"></a>如何復原已刪除的儲存體帳戶

Azure 儲存體透過自動化複本提供資料恢復功能，但不會防止使用者或應用程式程式碼損毀資料（不論是不小心或惡意）。 在應用程式或使用者錯誤的實例中維持資料精確度需要更先進的技術，例如將資料複製到具有 audit 記錄檔的次要儲存體位置。

下表提供儲存體帳戶復原範圍的總覽，視複寫策略而定。

| |LRS|ZRS|GRS|RA-GRS|
|---|---|---|---|---|
|儲存體帳戶 Azure Resource Manager|是|是|是|是|
|傳統儲存體帳戶|是|是|是|是|

收集下列資訊，並使用 Microsoft 支援服務提出支援要求：

* 儲存體帳戶名稱
* 刪除日期
* 儲存體帳戶區域
* 如何刪除儲存體帳戶？
* 哪些方法會刪除儲存體帳戶？ （入口網站、PowerShell 等）

重要重點

* 最多可能需要15天的時間來刪除儲存體服務，才能執行垃圾收集，因此無法使用 SLA 來復原儲存體帳戶。
* Microsoft 支援服務將會嘗試以最佳方式復原容器/帳戶，而且無法保證修復。

> [!NOTE]
> 如果帳戶已重新建立，則復原可能無法成功。 如果您已經重新建立帳戶，您必須先將它刪除，然後才能嘗試復原。
