---
title: TLS 設定-Azure 入口網站-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 瞭解如何使用 Azure 入口網站適用於 PostgreSQL 的 Azure 資料庫單一伺服器設定 TLS 設定
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: 26470709b37c2623c581499ec55572da402e96cb
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906463"
---
# <a name="configuring-tls-settings-in-azure-database-for-postgresql-single---server-using-azure-portal"></a>使用 Azure 入口網站在適用於 PostgreSQL 的 Azure 資料庫單一伺服器中設定 TLS 設定

本文說明如何設定適用於 PostgreSQL 的 Azure 資料庫，以強制執行連線所允許的最小 TLS 版本，以及拒絕與設定的最小 TLS 版本（較低 tls 版本）的所有連線，進而增強網路安全性。

您可以強制 TLS 版本連接至其適用於 PostgreSQL 的 Azure 資料庫。 客戶現在可以選擇設定其資料庫伺服器的最小 TLS 版本。 例如，將最低的 TLS 設定版本設定為 TLS 1.0，表示您的伺服器將允許來自使用 TLS 1.0、1.1 和 1.2 + 之用戶端的連接。 相反地，將最小 tls 版本設定為 1.2 + 表示您只允許使用 TLS 1.2 的用戶端連線，以及 TLS 1.0 和 TLS 1.1 的所有連線將會遭到拒絕。

## <a name="prerequisites"></a>必要條件

若要完成本操作說明指南，您需要：

* [適用於 PostgreSQL 的 Azure 資料庫](quickstart-create-server-database-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-postgresql---single-server"></a>設定適用於 PostgreSQL 的 Azure 資料庫-單一伺服器的 TLS 設定

請遵循下列步驟來設定于 postgresql 的最小 TLS 版本：

1. 在 [ [Azure 入口網站](https://portal.azure.com/)中，選取您現有的適用於 PostgreSQL 的 Azure 資料庫。

1.  在 [適用於 PostgreSQL 的 Azure 資料庫-單一伺服器] 頁面的 [ **設定**] 底下，按一下 [連線 **安全性** ] 以開啟 [連接安全性設定] 頁面。

1. 在 [ **最小 tls 版本**] 中，選取 [ **1.2** ] 以拒絕于 POSTGRESQL 單一伺服器的 tls 版本小於 tls 1.2 的連線。

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value.png" alt-text="適用於 PostgreSQL 的 Azure 資料庫單一伺服器的 TLS 設定":::

1. 按一下 [儲存] 儲存變更。

1. 通知會確認已成功啟用連線安全性設定。

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value-success.png" alt-text="適用於 PostgreSQL 的 Azure 資料庫-單一伺服器 TLS 設定成功":::

## <a name="next-steps"></a>下一步

瞭解 [如何建立計量警示](howto-alert-on-metric.md)