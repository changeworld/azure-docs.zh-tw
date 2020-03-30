---
title: 拒絕公共網路訪問 - Azure 門戶 - MariaDB 的 Azure 資料庫
description: 瞭解如何使用 Azure 門戶為 MariaDB 的 Azure 資料庫配置拒絕公共網路訪問
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 87b6033e486e9009573436628d0183c8a022aced
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79375235"
---
# <a name="deny-public-network-access-in-azure-database-for-mariadb-using-azure-portal"></a>使用 Azure 門戶拒絕 MariaDB 在 Azure 資料庫中的公共網路訪問

本文介紹如何為 MariaDB 伺服器配置 Azure 資料庫以拒絕所有公共配置，並只允許通過專用終結點進行連接以進一步增強網路安全。

## <a name="prerequisites"></a>Prerequisites

若要完成本操作說明指南，您需要：

* [MariaDB 的 Azure 資料庫](quickstart-create-MariaDB-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>設置拒絕公共網路訪問

按照以下步驟設置 MariaDB 伺服器拒絕公共網路訪問：

1. 在[Azure 門戶](https://portal.azure.com/)中，為 MariaDB 伺服器選擇現有的 Azure 資料庫。

1. 在 MariaDB 伺服器頁上，在 **"設置"** 下，按一下 **"連接安全**"以打開連接安全配置頁。

1. 在"拒絕公共網路訪問"中，選擇 **"是**"以啟用 MariaDB 伺服器的拒絕公共訪問。

    ![用於 MariaDB 拒絕網路訪問的 Azure 資料庫](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. 按一下 **[儲存]**，儲存變更。

1. 通知將確認連接安全設置已成功啟用。

    ![用於 MariaDB 拒絕網路訪問成功的 Azure 資料庫](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>後續步驟

瞭解如何[在指標上創建警報](howto-alert-metric.md)。