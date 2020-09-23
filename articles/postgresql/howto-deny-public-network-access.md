---
title: 拒絕公用網路存取-Azure 入口網站-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 瞭解如何使用適用於 PostgreSQL 的 Azure 資料庫單一伺服器的 Azure 入口網站設定拒絕公用網路存取
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: 1dfc8d473d0cfe663569d2508404bf190f2da841
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90901582"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>使用 Azure 入口網站在適用於 PostgreSQL 的 Azure 資料庫單一伺服器中拒絕公用網路存取

本文說明如何設定適用於 PostgreSQL 的 Azure 資料庫單一伺服器，以拒絕所有的公用設定，並只允許透過私人端點的連線，以進一步強化網路安全性。

## <a name="prerequisites"></a>必要條件

若要完成本操作說明指南，您需要：

* [適用於 PostgreSQL 的 Azure 資料庫單一伺服器](quickstart-create-server-database-portal.md)

## <a name="set-deny-public-network-access"></a>設定拒絕公用網路存取

遵循下列步驟來設定于 postgresql 單一伺服器拒絕公用網路存取：

1. 在 [ [Azure 入口網站](https://portal.azure.com/)中，選取您現有的適用於 PostgreSQL 的 Azure 資料庫單一伺服器。

1. 在 [于 postgresql 單一伺服器] 頁面的 [ **設定**] 底下，按一下 [連線 **安全性** ] 以開啟 [連接安全性設定] 頁面。

1. 在 [ **拒絕公用網路存取**] 中選取 **[是]** ，以啟用于 postgresql 單一伺服器的 [拒絕公用存取]。

    :::image type="content" source="./media/howto-deny-public-network-access/deny-public-network-access.PNG" alt-text="適用於 PostgreSQL 的 Azure 資料庫單一伺服器拒絕網路存取":::

1. 按一下 [儲存] 儲存變更。

1. 通知會確認已成功啟用連線安全性設定。

    :::image type="content" source="./media/howto-deny-public-network-access/deny-public-network-access-success.png" alt-text="適用於 PostgreSQL 的 Azure 資料庫單一伺服器拒絕網路存取成功":::

## <a name="next-steps"></a>下一步

瞭解 [如何建立計量警示](howto-alert-on-metric.md)。
