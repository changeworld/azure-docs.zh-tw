---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8b97a62626666fa39a5b0622852d9eec47c2410a
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024876"
---
將 RBAC 角色指派給安全性主體之前，請先決定安全性主體應該具備的存取範圍。 最佳做法規定，最好只授與最少的可能範圍。

下列清單說明從最小範圍開始，您可以將 Azure blob 和佇列資源存取範圍的層級：

- **個別容器。** 在此範圍中，角色指派會套用至容器中的所有 blob，以及容器屬性和中繼資料。
- **個別佇列。** 在此範圍中，角色指派會套用到佇列中的訊息，以及佇列屬性和中繼資料。
- **儲存體帳戶。** 在此範圍中，角色指派會套用至所有容器及其 blob，或所有佇列和其訊息。
- **資源群組。** 在此範圍中，角色指派會套用至資源群組中所有儲存體帳戶內的所有容器或佇列。
- **訂用帳戶。** 在此範圍中，角色指派會套用至訂用帳戶中所有資源群組中所有儲存體帳戶內的所有容器或佇列。
