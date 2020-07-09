---
title: TLS 設定-Azure 入口網站-適用於 MariaDB 的 Azure 資料庫
description: 瞭解如何為您的適用於 MariaDB 的 Azure 資料庫使用 Azure 入口網站設定 TLS 設定
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: fac719daf05e8b319db7c86d0dbc61c2814b0a0c
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86120342"
---
# <a name="configuring-tls-settings-in-azure-database-for-mariadb-using-azure-portal"></a>使用 Azure 入口網站在適用於 MariaDB 的 Azure 資料庫中進行 TLS 設定

本文說明如何設定適用於 MariaDB 的 Azure 資料庫伺服器，以強制執行最基本的 TLS 版本以進行連線，並使用比設定的最低 TLS 版本更低的 tls 版本來拒絕所有連接，從而增強網路安全性。

您可以為其資料庫伺服器設定最低的 TLS 版本，藉以強制連線到其適用於 MariaDB 的 Azure 資料庫的 TLS 版本。 例如，將最小的 TLS 設定版本設定為 TLS 1.0，表示您的伺服器將允許使用 TLS 1.0、1.1 和 1.2 + 的用戶端進行連接。 或者，將此值設定為1.2，表示您只允許使用 TLS 1.2 + 的用戶端連線，且 TLS 1.0 和 TLS 1.1 的所有連線都會遭到拒絕。

## <a name="prerequisites"></a>必要條件

若要完成本操作說明指南，您需要：

* [適用於 MariaDB 的 Azure 資料庫](quickstart-create-mariaDB-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mariadb"></a>設定適用於 MariaDB 的 Azure 資料庫的 TLS 設定

請遵循下列步驟來設定適用于 mariadb server 最低 TLS 版本：

1. 在 [ [Azure 入口網站](https://portal.azure.com/)中，選取您現有的適用於 MariaDB 的 Azure 資料庫伺服器。

1. 在 [適用于 mariadb 伺服器] 頁面的 [**設定**] 底下，按一下 [連線**安全性**] 以開啟 [連線安全性設定] 頁面。

1. 在 [**最低 tls 版本**] 中，選取 [ **1.2** ] 以拒絕適用于 mariadb 伺服器的 tls 版本低於 tls 1.2 的連接。

    ![適用於 MariaDB 的 Azure 資料庫 TLS 設定](./media/howto-tls-configurations/tls-configurations.png)

1. 按一下 [儲存]  儲存變更。

1. 通知會確認已成功啟用連線安全性設定。

    ![適用於 MariaDB 的 Azure 資料庫 TLS 設定成功](./media/howto-tls-configurations/tls-configurations-success.png)

## <a name="next-steps"></a>後續步驟

深入瞭解[如何建立計量警示](howto-alert-metric.md)