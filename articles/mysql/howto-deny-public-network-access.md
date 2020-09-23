---
title: 拒絕公用網路存取-Azure 入口網站-適用於 MySQL 的 Azure 資料庫
description: 瞭解如何使用適用於 MySQL 的 Azure 資料庫的 Azure 入口網站設定拒絕公用網路存取
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: eb5c4e4c4dfb73b2f7c9dc9f2629296790790885
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90896146"
---
# <a name="deny-public-network-access-in-azure-database-for-mysql-using-azure-portal"></a>使用 Azure 入口網站拒絕適用於 MySQL 的 Azure 資料庫中的公用網路存取

本文說明如何設定適用於 MySQL 的 Azure 資料庫伺服器拒絕所有的公用設定，並只允許透過私人端點的連線，以進一步增強網路安全性。

## <a name="prerequisites"></a>必要條件

若要完成本操作說明指南，您需要：

* [適用於 MySQL 的 Azure 資料庫](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>設定拒絕公用網路存取

遵循下列步驟來設定 MySQL 伺服器拒絕公用網路存取：

1. 在 [ [Azure 入口網站](https://portal.azure.com/)中，選取您現有的適用於 MySQL 的 Azure 資料庫伺服器。

1. 在 [MySQL 伺服器] 頁面的 [ **設定**] 底下，按一下 [連線 **安全性** ] 以開啟 [連接安全性設定] 頁面。

1. 在 [ **拒絕公用網路存取**] 中，選取 **[是]** ，為您的 MySQL 伺服器啟用 [拒絕公用存取]。

    :::image type="content" source="./media/howto-deny-public-network-access/setting-deny-public-network-access.PNG" alt-text="適用於 MySQL 的 Azure 資料庫拒絕網路存取":::

1. 按一下 [儲存] 儲存變更。

1. 通知會確認已成功啟用連線安全性設定。

    :::image type="content" source="./media/howto-deny-public-network-access/setting-deny-public-network-access-success.png" alt-text="適用於 MySQL 的 Azure 資料庫拒絕網路存取成功":::

## <a name="next-steps"></a>下一步

瞭解 [如何建立計量警示](howto-alert-on-metric.md)。