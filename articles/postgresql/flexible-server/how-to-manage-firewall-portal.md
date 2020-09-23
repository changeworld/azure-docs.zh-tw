---
title: 管理防火牆規則-Azure 入口網站-適用於 PostgreSQL 的 Azure 資料庫-彈性伺服器
description: 使用 Azure 入口網站建立及管理適用於 PostgreSQL 的 Azure 資料庫彈性伺服器的防火牆規則
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 8c107b88f964d8e657d6833fc3a2e8425d9053ce
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933837"
---
# <a name="create-and-manage-firewall-rules-for-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>使用 Azure 入口網站建立及管理適用於 PostgreSQL 的 Azure 資料庫彈性伺服器的防火牆規則

> [!IMPORTANT]
> 適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器目前處於公開預覽狀態。

適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器支援兩種互斥的網路連線方法，以連接到您的彈性伺服器。 這兩個選項是：

* 公用存取 (允許的 IP 位址)
* 私人存取 (VNet 整合)

在本文中，我們將著重于建立具有 **公用存取 (允許的 IP 位址) ** 使用 Azure 入口網站的于 postgresql 伺服器，並在建立具彈性的伺服器之後，提供管理防火牆規則的總覽。 使用 *公用存取 (允許的 ip 位址) *，于 postgresql 伺服器的連線會限制為僅允許的 ip 位址。 防火牆規則中必須允許用戶端 IP 位址。 若要深入瞭解，請參閱 [公用存取 (允許的 IP 位址) ](./concepts-networking.md#public-access-allowed-ip-addresses)。 您可以在建立伺服器時定義防火牆規則 (建議) 但稍後也可以新增。 在本文中，我們將概述如何使用公用存取 (允許的 IP 位址) 來建立和管理防火牆規則。

## <a name="create-a-firewall-rule-when-creating-a-server"></a>建立伺服器時建立防火牆規則

1. 在入口網站的左上角，選取 [建立資源]  (+)。
2. 選取 [資料庫]   > [Azure Database for PostgreSQL]  。 您也可以在 [搜尋] 方塊中輸入 **于 postgresql** 來尋找服務。
3. 選取 [彈性伺服器] 作為部署選項。
4. 填寫 **基本** 形式。
5. 移至 [ **網路** ] 索引標籤，設定您要連接到伺服器的方式。
6. 在 [連線 **方法**] 中，選取 [ *公用存取 (允許的 IP 位址]) *。 若要建立 **防火牆規則**，請指定防火牆規則名稱和單一 IP 位址，或位址範圍。 如果您想要將規則限制為單一 IP 位址，請在 [起始 IP 位址] 和 [結束 IP 位址] 欄位中輸入相同的位址。 開啟防火牆可讓系統管理員、使用者和應用程式存取于 postgresql 伺服器上具有有效認證的任何資料庫。
   > [!Note]
   > 適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器會在伺服器層級建立防火牆。 它會防止外部應用程式和工具連線到伺服器及伺服器上的任何資料庫，除非您建立規則以針對特定 IP 位址開啟防火牆。
7. 選取 [檢閱 + 建立]，檢閱彈性伺服器組態。
8.  選取 [建立] 以佈建伺服器。 佈建這可能需要數分鐘的時間。

## <a name="create-a-firewall-rule-after-server-is-created"></a>建立伺服器之後建立防火牆規則

1. 在 [ [Azure 入口網站](https://portal.azure.com/)中，選取您要新增防火牆規則適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器。
2. 在 [彈性伺服器] 頁面的 [ **設定** ] 標題下，按一下 [ **網路** 功能] 以開啟具有彈性伺服器的 [網路功能] 頁面。

   <!--![Azure portal - click Connection Security](./media/howto-manage-firewall-portal/1-connection-security.png)-->

3. 按一下 [ **將目前的用戶端 IP 位址新增** 至防火牆規則]。 這樣會使用 Azure 系統發現的電腦公用 IP 位址自動建立防火牆規則。

   <!--![Azure portal - click Add My IP](./media/howto-manage-firewall-portal/2-add-my-ip.png)-->

4. 儲存設定之前，請確認您的 IP 位址。 在某些情況下，Azure 入口網站觀察到的 IP 位址，不同於用來存取網際網路和 Azure 伺服器的 IP 位址。 因此，您可能需要變更起始 IP 位址和結束 IP 位址，才能讓規則如預期般運作。

   您可以使用搜尋引擎或其他線上工具來檢查您自己的 IP 位址。 例如，搜尋「我的 IP 是什麼」。

   <!--![Bing search for What is my IP](./media/howto-manage-firewall-portal/3-what-is-my-ip.png)-->

5. 新增其他位址範圍。 在適用於 PostgreSQL 的 Azure 資料庫彈性伺服器的防火牆規則中，您可以指定單一 IP 位址或位址範圍。 如果您想要將規則限制為單一 IP 位址，請在 [起始 IP 位址] 和 [結束 IP 位址] 欄位中輸入相同的位址。 開啟防火牆可讓系統管理員、使用者和應用程式存取于 postgresql 伺服器上具有有效認證的任何資料庫。

   <!--![Azure portal - firewall rules](./media/howto-manage-firewall-portal/4-specify-addresses.png)-->

6. 按一下工具列上的 [ **儲存** ]，儲存此防火牆規則。 等待確認已成功更新防火牆規則。

   <!--![Azure portal - click Save](./media/howto-manage-firewall-portal/5-save-firewall-rule.png)-->

## <a name="connecting-from-azure"></a>從 Azure 連線

您可能會想要啟用在 Azure 中部署的資源或應用程式，以連線到您的彈性伺服器。 這包括在 Azure App Service 中裝載的 web 應用程式，在 Azure VM 上執行、Azure Data Factory 資料管理閘道，以及其他更多。 

當 Azure 內的應用程式嘗試連線到您的伺服器時，防火牆會驗證是否允許 Azure 連接。 您可以在入口網站的 [**網路**] 索引標籤中，選取 [**允許從 Azure 中的 azure 服務和資源存取此伺服器**] 選項來啟用此設定，然後按 [**儲存**]。

資源不需要位於相同的虛擬網路中 (VNet) 或資源群組，防火牆規則就可以啟用這些連線。 如果不允許連線嘗試，則要求不會到達適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器。

> [!IMPORTANT]
> 這個選項會設定防火牆，以允許所有來自 Azure 的連線，包括來自其他客戶訂用帳戶的連線。 選取這個選項時，請確定您的登入和使用者權限會限制為只有授權的使用者才能存取。
>
> 我們建議您選擇 **私人存取 (VNet 整合) ** 安全地存取有彈性的伺服器。
>
## <a name="manage-existing-firewall-rules-through-the-azure-portal"></a>透過 Azure 入口網站管理現有的防火牆規則

請重複下列步驟來管理防火牆規則。

- 若要新增目前的電腦，請按一下 [防火牆規則] 中的 [+ **新增目前的用戶端 IP 位址** ]。 按一下 [儲存]  儲存變更。
- 若要新增其他 IP 位址，請輸入「規則名稱」、「起始 IP 位址」和「結束 IP 位址」。 按一下 [儲存]  儲存變更。
- 若要修改現有的規則，請按一下和修改規則中的任何欄位。 按一下 [儲存]  儲存變更。
- 若要刪除現有的規則，請按一下省略符號 [...]，然後按一下 [刪除] 移除規則。 按一下 [儲存]  儲存變更。

## <a name="next-steps"></a>後續步驟
- 深入瞭解 [適用於 PostgreSQL 的 Azure 資料庫彈性伺服器中的網路功能](./concepts-networking.md)
- 深入瞭解 [適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器防火牆規則](./concepts-networking.md#public-access-allowed-ip-addresses)
- [使用 Azure CLI 建立及管理適用於 PostgreSQL 的 Azure 資料庫防火牆規則](./how-to-manage-firewall-cli.md)。