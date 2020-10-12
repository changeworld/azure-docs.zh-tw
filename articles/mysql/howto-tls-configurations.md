---
title: TLS 設定-Azure 入口網站-適用於 MySQL 的 Azure 資料庫
description: 瞭解如何為您的適用於 MySQL 的 Azure 資料庫使用 Azure 入口網站設定 TLS 設定
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: d94e589a19b29e68883c0217b62b883f9d026789
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90902728"
---
# <a name="configuring-tls-settings-in-azure-database-for-mysql-using-azure-portal"></a>使用 Azure 入口網站在適用於 MySQL 的 Azure 資料庫中設定 TLS 設定

本文說明如何設定適用於 MySQL 的 Azure 資料庫伺服器來強制執行連線通過的最小 TLS 版本，以及拒絕與設定的最小 TLS 版本相同的 tls 版本連線，進而增強網路安全性。

您可以強制 TLS 版本連接至其適用於 MySQL 的 Azure 資料庫。 客戶現在可以選擇設定其資料庫伺服器的最小 TLS 版本。 例如，將此最小 TLS 版本設定為1.0，表示您應該允許使用 TLS 1.0、1.1 和1.2 連接的用戶端。 或者，將此設定為1.2，表示您只允許使用 TLS 1.2 + 連線的用戶端，以及 TLS 1.0 和 TLS 1.1 的所有連入連線都會遭到拒絕。

## <a name="prerequisites"></a>必要條件

若要完成本操作說明指南，您需要：

* [適用於 MySQL 的 Azure 資料庫](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mysql"></a>設定適用於 MySQL 的 Azure 資料庫的 TLS 設定

請遵循下列步驟來設定 MySQL server 的最低 TLS 版本：

1. 在 [ [Azure 入口網站](https://portal.azure.com/)中，選取您現有的適用於 MySQL 的 Azure 資料庫伺服器。

1. 在 [MySQL 伺服器] 頁面的 [ **設定**] 底下，按一下 [連線 **安全性** ] 以開啟 [連接安全性設定] 頁面。

1. 在 [ **最小 tls 版本**] 中，選取 [ **1.2** ] 以拒絕 MySQL 伺服器的 tls 版本小於 tls 1.2 的連接。

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value.png" alt-text="適用於 MySQL 的 Azure 資料庫 TLS 設定":::

1. 按一下 [儲存] 儲存變更。

1. 通知會確認已成功啟用連線安全性設定。

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value-success.png" alt-text="適用於 MySQL 的 Azure 資料庫 TLS 設定":::

## <a name="next-steps"></a>後續步驟

- 瞭解 [如何建立計量警示](howto-alert-on-metric.md)