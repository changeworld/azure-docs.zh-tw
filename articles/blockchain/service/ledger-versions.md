---
title: Azure 區塊鏈服務分類帳版本、修補、&升級
description: Azure 區塊鏈服務中受支援的分類帳版本的概述，包括有關系統修補、系統管理和使用者管理升級的策略。
ms.date: 11/20/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 8d4a77699dd80743acfadd4d72d6d75bc1939b3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325178"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>受支援的 Azure 區塊鏈服務分類帳版本

Azure 區塊鏈服務使用基於以非坊[的 Quorum](https://www.goquorum.com/developers)分類帳，用於處理一組已知參與者中的私人事務，這些參與者在 Azure 區塊鏈服務中標識為聯合體。

目前，Azure 區塊鏈服務支援[Quorum 版本 2.2.3](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.3)和[Tessera 交易管理員](https://github.com/jpmorganchase/tessera)。

## <a name="managing-updates-and-upgrades"></a>管理更新和升級

在"仲裁"中版本控制是通過主要、次要和修補程式版本完成的。 例如，如果 Quorum 版本為 2.0.1，則發佈類型將分類如下：

|主要 | Minor  | Patch  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Azure 區塊鏈服務在從 Quorum 提供後 30 天內自動將 Quorum 的修補程式版本更新到現有正在運行的成員。

## <a name="availability-of-new-ledger-versions"></a>新分類帳版本的可用性

Azure 區塊鏈服務在從仲裁製造商獲得後 60 天內提供最新的主要和次要版本的 Quorum 分類帳。 在預配新成員和聯合體時，最多提供四個次要版本供聯營集團選擇。 當前不支援從主要或次要版本升級到。 例如，如果您正在運行版本 2.x，則當前不支援升級到版本 3.x。 同樣，如果您正在運行版本 2.2，則當前不支援升級到版本 2.3。

## <a name="next-steps"></a>後續步驟

[Azure 區塊鏈服務中的限制](limits.md)
