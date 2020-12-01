---
title: 從小組刪除 Azure 實驗室服務實驗室
description: 瞭解如何從小組刪除 Azure 實驗室服務實驗室。
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: 8d1e20f8f676eb9863187b550a3c0400871d670c
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433949"
---
# <a name="delete-labs-within-teams"></a>在小組內刪除實驗室

本文說明如何從 **Azure 實驗室服務** 應用程式刪除實驗室。

## <a name="prerequisites"></a>Prerequisites

* 在 Azure 入口網站中[建立實驗室服務帳戶](tutorial-setup-lab-account.md#create-a-lab-account)。
* [開始使用並在小組內建立實驗室服務實驗室](how-to-get-started-create-lab-within-teams.md)。

## <a name="delete-labs"></a>刪除實驗室

您可以藉由直接刪除實驗室（如[Azure 實驗室服務中的管理實驗室](how-to-manage-classroom-labs.md)中所述），在[實驗室服務網站](https://labs.azure.com)中刪除小組內建立的實驗室。 

刪除小組時，也會觸發實驗室刪除。 如果已刪除實驗室建立所在的小組，則會在觸發自動使用者清單同步處理之後24小時自動刪除實驗室。 

> [!IMPORTANT]
> 刪除索引標籤或卸載應用程式不會導致刪除實驗室。 

如果刪除索引標籤，除非透過刪除網站上的實驗室或刪除小組，否則小組成員資格清單上的使用者仍然可以存取 [實驗室服務網站](https://labs.azure.com) 上的 vm。 

## <a name="next-steps"></a>後續步驟

- [在小組內使用 Azure 實驗室服務總覽](lab-services-within-teams-overview.md)
- [管理小組內的實驗室使用者清單](how-to-manage-user-lists-within-teams.md)
- [管理小組內實驗室的 VM 集區](how-to-manage-vm-pool-within-teams.md)
- [建立及管理小組內的實驗室排程](how-to-create-schedules-within-teams.md)
- [存取小組內的 VM –學生版視圖](how-to-access-vm-for-students-within-teams.md)

