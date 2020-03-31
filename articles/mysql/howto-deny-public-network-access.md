---
title: 拒絕公共網路訪問 - Azure 門戶 - MySQL 的 Azure 資料庫
description: 瞭解如何使用 Azure 資料庫的 Azure 資料庫為 MySQL 配置拒絕公共網路訪問
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: b5f93f3a3583900810ca75f925c6a88df9102652
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79374949"
---
# <a name="deny-public-network-access-in-azure-database-for-mysql-using-azure-portal"></a>使用 Azure 門戶拒絕 Azure 資料庫中的公用網路訪問

本文介紹如何為 MySQL 伺服器配置 Azure 資料庫以拒絕所有公共配置，並只允許通過專用終結點進行連接以進一步增強網路安全。

## <a name="prerequisites"></a>Prerequisites

若要完成本操作說明指南，您需要：

* [MySQL 的 Azure 資料庫](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>設置拒絕公共網路訪問

按照以下步驟設置 MySQL 伺服器拒絕公共網路訪問：

1. 在[Azure 門戶](https://portal.azure.com/)中，為 MySQL 伺服器選擇現有的 Azure 資料庫。

1. 在 MySQL 伺服器頁上，在 **"設置"** 下，按一下 **"連接安全**"以打開連接安全配置頁。

1. 在 **"拒絕公共網路訪問"** 中，選擇 **"是**"以啟用 MySQL 伺服器的拒絕公共訪問。

    ![用於 MySQL 拒絕網路訪問的 Azure 資料庫](./media/howto-deny-public-network-access/setting-deny-public-network-access.PNG)

1. 按一下 **[儲存]**，儲存變更。

1. 通知將確認連接安全設置已成功啟用。

    ![用於 MySQL 拒絕網路訪問成功的 Azure 資料庫](./media/howto-deny-public-network-access/setting-deny-public-network-access-success.png)

## <a name="next-steps"></a>後續步驟

瞭解如何[在指標上創建警報](howto-alert-on-metric.md)。