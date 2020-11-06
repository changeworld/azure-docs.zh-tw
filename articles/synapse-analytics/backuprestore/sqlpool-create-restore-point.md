---
title: 為專用的 SQL 集區建立使用者定義的還原點
description: 如何建立專用 SQL 集區的還原點。
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c0835fb48d00fe5277732f34fd6b0de1448f6a78
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "94332053"
---
# <a name="user-defined-restore-points"></a>使用者定義的還原點

在本文中，您將瞭解如何使用 Azure 入口網站，在 Azure Synapse Analytics 中為專用的 SQL 集區建立新的使用者定義還原點。

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>透過 Azure 入口網站建立使用者定義的還原點

您也可以透過 Azure 入口網站建立使用者定義的還原點。

1. 登入您的 [Azure 入口網站](https://portal.azure.com/) 帳戶。

2. 流覽至您想要為其建立還原點的專用 SQL 集區。

3. 從左窗格選取 **[總覽** ]，然後選取 [ **+ 新增還原點** ]。 如果 [新的還原點] 按鈕未啟用，請確定未暫停專用的 SQL 集區。

    ![新增還原點](../media/sql-pools/create-sqlpool-restore-point-01.png)

4. 指定使用者定義的還原點名稱， **然後按一下 [** 套用]。 使用者定義還原點的預設保留期間為七天。

    ![還原點名稱](../media/sql-pools/create-sqlpool-restore-point-02.png)

## <a name="next-steps"></a>後續步驟

- [還原現有的專用 SQL 集區](restore-sql-pool.md)

