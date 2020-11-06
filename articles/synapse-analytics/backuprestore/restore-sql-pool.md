---
title: 還原現有的專用 SQL 集區
description: 還原現有專用 SQL 集區的操作指南。
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
ms.openlocfilehash: 86150107273b247bef2c74e5b8c5272d7148587e
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "94332043"
---
# <a name="restore-an-existing-dedicated-sql-pool"></a>還原現有的專用 SQL 集區

在本文中，您將瞭解如何使用 Azure 入口網站和 Synapse Studio，在 Azure Synapse Analytics 中還原現有的專用 SQL 集區。 本文適用于還原和異地還原。 

## <a name="restore-an-existing-dedicated-sql-pool-through-the-synapse-studio"></a>透過 Synapse Studio 還原現有的專用 SQL 集區

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 流覽至您的 Synapse 工作區。 
3. 在 [消費者入門-> 開啟 Synapse Studio 中，選取 [ **開啟** ]。

    ![ Synapse Studio](../media/sql-pools/open-synapse-studio.png)
4. 在左側流覽窗格中，選取 [ **資料** ]。
5. 選取 [ **管理** 集區]。 
6. 選取 [ **+ 新增** ] 以建立新的專用 SQL 集區。 
7. 在 [其他設定] 索引標籤中，選取要從中還原的還原點。 

    如果您想要執行異地還原，請選取您想要復原的工作區和專用的 SQL 集區。 

8. 選取 [自動還原點] 或 [使用者定義的還原點]。 

    ![還原點](../media/sql-pools/restore-point.PNG)

    如果專用的 SQL 集區沒有任何自動還原點，請等候幾個小時，或在還原之前先建立使用者定義的還原點。 針對 User-Defined 還原點，請選取現有的還原點，或建立一個新的。

    如果您要還原異地備份，只要選取位於來源區域的工作區，以及您想要還原的專用 SQL 集區。 

9. 選取 [檢閱 + 建立]。

## <a name="restore-an-existing-dedicated-sql-pool-through-the-azure-portal"></a>透過 Azure 入口網站還原現有的專用 SQL 集區

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 流覽至您想要從中還原的專用 SQL 集區。
3. 在 [概觀] 刀鋒視窗頂端，選取 [還原]。

    ![ 還原概觀](../media/sql-pools/restore-sqlpool-01.png)

4. 選取 [自動還原點] 或 [使用者定義的還原點]。 

    如果專用的 SQL 集區沒有任何自動還原點，請稍候幾小時或建立使用者定義的還原點，然後再進行還原。 

    如果您想要執行異地還原，請選取您想要復原的工作區和專用的 SQL 集區。 

5. 選取 [檢閱 + 建立]。

## <a name="next-steps"></a>後續步驟

- [建立還原點](sqlpool-create-restore-point.md)
