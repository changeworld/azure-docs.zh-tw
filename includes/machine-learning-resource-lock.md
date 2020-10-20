---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/16/2020
ms.author: larryfr
ms.openlocfilehash: 25e304daf75b60a7d037640d496ed0972581f13a
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92204452"
---
Azure 可讓您將 _鎖定_ 放置在資源上，使其無法被刪除或處於唯讀狀態。 __鎖定資源可能會導致非預期的結果。__ 某些看似無法修改資源的作業，其實需要鎖定封鎖的動作。 

例如，將刪除鎖定套用至工作區的資源群組，將會防止 Azure ML 計算叢集的調整作業。

如需鎖定資源的詳細資訊，請參閱 [鎖定資源以防止非預期的變更](../articles/azure-resource-manager/management/lock-resources.md)。