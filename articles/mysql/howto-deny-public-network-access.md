---
title: 拒絕公用網路存取-Azure 入口網站-適用於 MySQL 的 Azure 資料庫
description: 瞭解如何使用適用於 MySQL 的 Azure 資料庫的 Azure 入口網站來設定拒絕公用網路存取
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: b5f93f3a3583900810ca75f925c6a88df9102652
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "79374949"
---
# <a name="deny-public-network-access-in-azure-database-for-mysql-using-azure-portal"></a>使用 Azure 入口網站拒絕適用於 MySQL 的 Azure 資料庫中的公用網路存取

本文說明如何將適用於 MySQL 的 Azure 資料庫伺服器設定為拒絕所有公用設定，並僅允許透過私人端點的連線，以進一步增強網路安全性。

## <a name="prerequisites"></a>必要條件

若要完成本操作說明指南，您需要：

* [適用於 MySQL 的 Azure 資料庫](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>設定拒絕公用網路存取

請遵循下列步驟來設定 MySQL server 拒絕公用網路存取：

1. 在 [ [Azure 入口網站](https://portal.azure.com/)中，選取您現有的適用於 MySQL 的 Azure 資料庫伺服器。

1. 在 [MySQL 伺服器] 頁面的 [**設定**] 底下，按一下 [連線**安全性**] 以開啟 [連線安全性設定] 頁面。

1. 在 [**拒絕公用網路存取**] 中，選取 **[是]** 以啟用您 MySQL 伺服器的 [拒絕公用存取]。

    ![適用於 MySQL 的 Azure 資料庫拒絕網路存取](./media/howto-deny-public-network-access/setting-deny-public-network-access.PNG)

1. 按一下 [儲存]  儲存變更。

1. 通知會確認已成功啟用連線安全性設定。

    ![適用於 MySQL 的 Azure 資料庫拒絕網路存取成功](./media/howto-deny-public-network-access/setting-deny-public-network-access-success.png)

## <a name="next-steps"></a>後續步驟

深入瞭解[如何建立計量的警示](howto-alert-on-metric.md)。