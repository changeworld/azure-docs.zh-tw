---
title: TLS 設定-Azure 入口網站-適用於 MariaDB 的 Azure 資料庫
description: 瞭解如何為您的適用於 MariaDB 的 Azure 資料庫使用 Azure 入口網站設定 TLS 設定
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: 55a664ceb3524a0c545e037dcba7a2af90034ad4
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241979"
---
# <a name="configuring-tls-settings-in-azure-database-for-mariadb-using-azure-portal"></a>使用 Azure 入口網站在適用於 MariaDB 的 Azure 資料庫中設定 TLS 設定

本文說明如何設定適用於 MariaDB 的 Azure 資料庫伺服器來強制執行連線通過的最小 TLS 版本，以及拒絕與設定的最低 TLS 版本的 tls 版本的所有連線，進而增強網路安全性。

您可以藉由設定其資料庫伺服器的最小 TLS 版本，來強制連線至其適用於 MariaDB 的 Azure 資料庫的 TLS 版本。 例如，將最低的 TLS 設定版本設定為 TLS 1.0，表示您的伺服器將允許來自使用 TLS 1.0、1.1 和 1.2 + 之用戶端的連接。 或者，將此設定為1.2，表示您只允許使用 TLS 1.2 + 的用戶端連線，以及 TLS 1.0 和 TLS 1.1 的所有連線將會遭到拒絕。

## <a name="prerequisites"></a>先決條件

若要完成本操作說明指南，您需要：

* [適用於 MariaDB 的 Azure 資料庫](quickstart-create-mariaDB-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mariadb"></a>設定適用於 MariaDB 的 Azure 資料庫的 TLS 設定

請遵循下列步驟來設定適用于 mariadb server 最小 TLS 版本：

1. 在 [ [Azure 入口網站](https://portal.azure.com/)中，選取您現有的適用於 MariaDB 的 Azure 資料庫伺服器。

1. 在 [適用于 mariadb 伺服器] 頁面的 [ **設定** ] 底下，按一下 [連線 **安全性** ] 以開啟 [連接安全性設定] 頁面。

1. 在 [ **最小 tls 版本** ] 中，選取 [ **1.2** ] 以拒絕適用于 mariadb 伺服器的 tls 版本小於 tls 1.2 的連線。

    ![適用於 MariaDB 的 Azure 資料庫 TLS 設定](./media/howto-tls-configurations/tls-configurations.png)

1. 按一下 [儲存] 儲存變更。

1. 通知會確認已成功啟用連線安全性設定。

    ![適用於 MariaDB 的 Azure 資料庫 TLS 設定成功](./media/howto-tls-configurations/tls-configurations-success.png)

## <a name="next-steps"></a>下一步

瞭解 [如何建立計量警示](howto-alert-metric.md)