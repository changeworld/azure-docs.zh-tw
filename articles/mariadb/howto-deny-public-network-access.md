---
title: 拒絕公用網路存取-Azure 入口網站-適用於 MariaDB 的 Azure 資料庫
description: 瞭解如何使用適用於 MariaDB 的 Azure 資料庫的 Azure 入口網站來設定拒絕公用網路存取
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 87b6033e486e9009573436628d0183c8a022aced
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375235"
---
# <a name="deny-public-network-access-in-azure-database-for-mariadb-using-azure-portal"></a>使用 Azure 入口網站拒絕適用於 MariaDB 的 Azure 資料庫中的公用網路存取

本文說明如何將適用於 MariaDB 的 Azure 資料庫伺服器設定為拒絕所有公用設定，並僅允許透過私人端點的連線，以進一步增強網路安全性。

## <a name="prerequisites"></a>Prerequisites

若要完成本操作說明指南，您需要：

* [適用於 MariaDB 的 Azure 資料庫](quickstart-create-MariaDB-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>設定拒絕公用網路存取

請遵循下列步驟來設定適用于 mariadb 伺服器拒絕公用網路存取：

1. 在  [Azure 入口網站](https://portal.azure.com/)中，選取您現有的適用於 MariaDB 的 Azure 資料庫伺服器。

1. 在 [適用于 mariadb 伺服器] 頁面的 [**設定**] 底下，按一下 [連線**安全性**] 以開啟 [連線安全性設定] 頁面。

1. 在 [拒絕公用網路存取] 中，選取 **[是]** 以啟用適用于 mariadb 伺服器的拒絕公用存取。

    ![適用於 MariaDB 的 Azure 資料庫拒絕網路存取](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. 按一下 [儲存] 儲存變更。

1. 通知會確認已成功啟用連線安全性設定。

    ![適用於 MariaDB 的 Azure 資料庫拒絕網路存取成功](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>後續步驟

深入瞭解[如何建立計量的警示](howto-alert-metric.md)。