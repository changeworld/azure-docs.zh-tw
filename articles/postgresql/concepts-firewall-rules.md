---
title: 防火牆規則-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 本文說明如何使用防火牆規則來連接到適用於 PostgreSQL 的 Azure 資料庫單一伺服器。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 7048fac4804e16ae70145c8fadc1cd6e0859fcfc
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90903906"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---single-server"></a>適用於 PostgreSQL 的 Azure 資料庫中的防火牆規則-單一伺服器
除非您指定哪些電腦擁有許可權，否則適用於 PostgreSQL 的 Azure 資料庫伺服器防火牆會防止對您資料庫伺服器的所有存取。 此防火牆會根據每一個要求的來源 IP 位址來授與伺服器存取權。
若要設定您的防火牆，您可以建立防火牆規則，指定可接受的 IP 位址範圍。 您可以在伺服器層級建立防火牆規則。

**防火牆規則：** 這些規則可讓用戶端完整存取適用於 PostgreSQL 的 Azure 資料庫伺服器，也就是相同邏輯伺服器內的所有資料庫。 使用 Azure 入口網站或使用 Azure CLI 命令，即可設定伺服器層級的防火牆規則。 若要建立伺服器層級的防火牆規則，您必須是訂用帳戶擁有者或訂用帳戶參與者。

## <a name="firewall-overview"></a>防火牆概觀
根據預設，防火牆會封鎖對適用於 PostgreSQL 之 Azure 資料庫伺服器的所有資料庫存取。 若要從其他電腦開始使用您的伺服器，請指定一或多個伺服器層級的防火牆規則，以便啟用對伺服器的存取。 使用防火牆規則，來指定允許從網際網路存取的 IP 位址範圍。 存取 Azure 入口網站本身，並不會受到防火牆規則所影響。
來自網際網路和 Azure 的連接嘗試必須先通過防火牆，才能到達您的 PostgreSQL 資料庫，如下圖所示：

:::image type="content" source="media/concepts-firewall-rules/1-firewall-concept.png" alt-text="防火牆運作方式的範例流程":::

## <a name="connecting-from-the-internet"></a>從網際網路連線
伺服器層級的防火牆規則可套用至相同「適用於 PostgreSQL 的 Azure 資料庫」伺服器上的所有資料庫。 如果要求的 IP 位址在伺服器層級防火牆規則中指定的其中一個範圍內，就會允許連線。
如果要求的 IP 位址不在任何伺服器層級防火牆規則中指定的範圍內，連線要求就會失敗。
例如，如果您的應用程式透過適用於 PostgreSQL 的 JDBC 驅動程式來連接，若您在防火牆封鎖連接期間嘗試進行連接，則可能會遇到此錯誤。
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: no pg\_hba.conf entry for host "123.45.67.890", user "adminuser", database "postgresql", SSL

## <a name="connecting-from-azure"></a>從 Azure 連線
建議您尋找任何應用程式或服務的連出 IP 位址，並明確地允許存取這些個別的 IP 位址或範圍。 例如，您可以找到 Azure App Service 的連出 IP 位址，或使用系結至虛擬機器或其他資源的公用 IP (請參閱以下資訊，以取得透過服務端點) 的虛擬機器私人 IP 連接的相關資訊。 

如果您的 Azure 服務無法使用固定的連出 IP 位址，您可以考慮啟用來自所有 Azure 資料中心 IP 位址的連線。 您可以從 [連線**安全性**] 窗格中，將 [**允許存取 Azure 服務**] 選項設定為 [**開啟**]，以從 Azure 入口網站啟用此設定，然後按 [**儲存**]。 從 Azure CLI 中，啟動和結束位址等於0.0.0.0 的防火牆規則設定會執行相同的設定。 如果不允許連線嘗試，要求就不會到達適用於 PostgreSQL 的 Azure 資料庫伺服器。

> [!IMPORTANT]
> [ **允許存取 azure 服務** ] 選項會設定防火牆，以允許所有來自 Azure 的連線，包括來自其他客戶訂用帳戶的連接。 選取這個選項時，請確定您的登入和使用者權限會限制為只有授權的使用者才能存取。
> 

:::image type="content" source="media/concepts-firewall-rules/allow-azure-services.png" alt-text="在入口網站中設定 [允許存取 Azure 服務]":::

### <a name="connecting-from-a-vnet"></a>從 VNet 連接
若要從 VNet 安全地連接到您的適用於 PostgreSQL 的 Azure 資料庫伺服器，請考慮使用 [vnet 服務端點](./concepts-data-access-and-security-vnet.md)。 

## <a name="programmatically-managing-firewall-rules"></a>以程式設計方式管理防火牆規則
除了 Azure 入口網站之外，防火牆規則還可以程式設計方式使用 Azure CLI 來管理。
另請參閱[使用 Azure CLI 建立和管理適用於 PostgreSQL 的 Azure 資料庫防火牆規則](howto-manage-firewall-using-cli.md)。

## <a name="troubleshooting-firewall-issues"></a>針對防火牆問題進行疑難排解
對於適用於 PostgreSQL 的 Microsoft Azure 資料庫伺服器服務的存取未如預期般運作時，請考慮下列幾點：

* **對允許清單的變更尚未生效：** 對適用於 PostgreSQL 之 Azure 資料庫伺服器防火牆組態的變更最多可能會延遲 5 分鐘才能生效。

* **登入未獲授權或使用不正確的密碼：** 如果適用於 PostgreSQL 的 Azure 資料庫伺服器上的登入沒有權限，或使用的密碼不正確，與適用於 PostgreSQL 的 Azure 資料庫伺服器連接就會遭到拒絕。 建立防火牆設定只是讓用戶端有機會嘗試連線到您的伺服器；每個用戶端仍然必須提供必要的安全性認證。

   例如，使用 JDBC 用戶端，可能會出現下列錯誤。
   > java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: password authentication failed for user "yourusername"

* **動態 IP 位址：** 如果您有使用動態 IP 位址的網際網路連線，並且在通過防火牆時遇到問題，您可以嘗試下列其中一個解決方案：

   * 要求您的網際網路服務提供者 (ISP) 將可存取適用於 PostgreSQL 的 Azure 資料庫伺服器之 IP 位址範圍指派給您的用戶端電腦，然後將 IP 位址範圍新增為防火牆規則。

   * 改為針對您的用戶端電腦取得靜態 IP 位址，然後將該靜態 IP 位址新增為防火牆規則。

* **伺服器的 IP 似乎是公用的：** 適用於 PostgreSQL 的 Azure 資料庫伺服器的連線會透過可公開存取的 Azure 閘道進行路由傳送。 不過，實際的伺服器 IP 會受到防火牆保護。 如需詳細資訊，請瀏覽[連線架構文章](concepts-connectivity-architecture.md)。

* **無法從 Azure 資源連線到允許的 IP：** 檢查您要連接的子網是否已啟用 **Microsoft Sql** 服務端點。 如果已啟用 **Sql** ，則表示您只想要在該子網上使用 [VNet 服務端點規則](concepts-data-access-and-security-vnet.md) 。

   例如，如果您從已啟用 **Microsoft .sql** 但沒有對應 VNet 規則的子網中的 Azure VM 連線，您可能會看到下列錯誤：  `FATAL: Client from Azure Virtual Networks is not allowed to access the server`

## <a name="next-steps"></a>下一步
* [使用 Azure 入口網站建立和管理適用於 PostgreSQL 的 Azure 資料庫防火牆規則](howto-manage-firewall-using-portal.md)
* [使用 Azure CLI 建立和管理適用於 PostgreSQL 的 Azure 資料庫防火牆規則](howto-manage-firewall-using-cli.md)
* [適用於 PostgreSQL 的 Azure 資料庫中的 VNet 服務端點](./concepts-data-access-and-security-vnet.md)
