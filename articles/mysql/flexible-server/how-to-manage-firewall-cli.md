---
title: 管理防火牆規則-Azure CLI-適用於 MySQL 的 Azure 資料庫-彈性伺服器
description: 使用 Azure CLI 命令列建立和管理適用於 MySQL 的 Azure 資料庫彈性伺服器的防火牆規則。
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 9/21/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4279da10de92bc8bf9cd564eaae02db2fef76a64
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936640"
---
# <a name="create-and-manage-azure-database-for-mysql---flexible-server-firewall-rules-using-the-azure-cli"></a>使用 Azure CLI 建立及管理適用於 MySQL 的 Azure 資料庫具彈性的伺服器防火牆規則

> [!IMPORTANT]
> 適用於 MySQL 的 Azure 資料庫彈性伺服器目前處於公開預覽狀態

「適用於 MySQL 的 Azure 資料庫」彈性伺服器支援兩種連線至彈性伺服器的網路連線方法，且兩者互斥。 這兩個選項是：

- 公用存取 (允許的 IP 位址)
- 私人存取 (VNet 整合)

在本文中，我們將著重于建立具有 **公用存取 (允許的 IP 位址) ** 使用 Azure CLI 的 MySQL 伺服器，並提供 Azure CLI 命令的總覽，讓您可以在建立、更新、刪除、列出及顯示在建立伺服器之後，建立、更新、刪除、列出及顯示防火牆規則。 使用 *公用存取 (允許的 ip 位址) *，MySQL 伺服器的連線會限制為僅允許的 ip 位址。 防火牆規則中必須允許用戶端 IP 位址。 若要深入瞭解，請參閱 [公用存取 (允許的 IP 位址) ](./concepts-networking.md#public-access-allowed-ip-addresses)。 您可以在建立伺服器時定義防火牆規則 (建議) 但稍後也可以新增。

## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

[Azure Cloud Shell](../../cloud-shell/overview.md) 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。

若要開啟 Cloud Shell，只要選取程式碼區塊右上角的 [試試看]  即可。 您也可以移至 [https://shell.azure.com/bash](https://shell.azure.com/bash)，從另一個瀏覽器索引標籤開啟 Cloud Shell。 選取 [複製]  即可複製程式碼區塊，將它貼到 Cloud Shell 中，然後選取 **Enter** 鍵加以執行。

如果您偏好在本機安裝和使用 CLI，本快速入門需要有 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="prerequisites"></a>必要條件

您必須使用 [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) 命令登入您的帳戶。 請注意 **id** 屬性，它會參考您 Azure 帳戶的訂用帳戶 **識別碼** 。

```azurecli-interactive
az login
```

使用 [az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set) 命令來選取您帳戶底下的特定訂用帳戶。 記下**az 登**入輸出中的**識別碼**值，以做為命令中**訂閱**引數的值。 如果您有多個訂用帳戶，請選擇資源計費的適當訂用帳戶。 若要取得您的所有訂用帳戶，請使用 [az account list](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list)。

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-firewall-rule-during-flexible-server-create-using-azure-cli"></a>使用 Azure CLI 在彈性伺服器建立期間建立防火牆規則

您可以使用 `az mysql flexible-server --public access` 命令來建立具有 *公用存取 (允許的 IP 位址) * 的彈性伺服器，並在建立有彈性的伺服器期間設定防火牆規則。 您可以使用 **--public-存取** 切換參數來提供可連接到伺服器的允許 IP 位址。 您可以提供要包含在允許的 ip 清單中的單一或 IP 位址範圍。 IP 位址範圍必須以破折號分隔，而且不包含任何空格。 使用 CLI 建立有彈性的伺服器有各種不同的選項，如下列範例所示。

請參閱 Azure CLI 參考檔 <!--FIXME --> 以取得可設定的 CLI 參數完整清單。 例如，在下列命令中，您可以選擇性地指定資源群組。

- 建立具有公用存取權的彈性伺服器，並新增用戶端 IP 位址以存取伺服器
    ```azurecli-interactive
    az mysql flexible-server create --public-access <my_client_ip>
    ```
- 建立具有公用存取權的彈性伺服器，並新增 IP 位址範圍，以存取此伺服器

    ```azurecli-interactive
    az mysql flexible-server create --public-access <start_ip_address-end_ip_address>
    ```
- 建立具有公用存取權的彈性伺服器，並允許來自 Azure IP 位址的應用程式連接到您的彈性伺服器
    ```azurecli-interactive
    az mysql flexible-server create --public-access 0.0.0.0
    ```
    > [!IMPORTANT]
    > 此選項會設定防火牆，以允許從 azure 中的 Azure 服務和資源存取此伺服器，包括來自其他客戶訂用帳戶的連接。 選取這個選項時，請確定您的登入和使用者權限會限制為只有授權的使用者才能存取。
    >
- 建立具有公用存取權的彈性伺服器，並允許所有 IP 位址
    ```azurecli-interactive
    az mysql flexible-server create --public-access all
    ```
    >[!Note]
    > 上述命令會建立具有起始 IP 位址 = 0.0.0.0、結束 IP 位址 = 255.255.255.255 的防火牆規則，且不會封鎖任何 IP 位址。 網際網路上的任何主機都可以存取此伺服器。 強烈建議您只在不包含敏感性資料的測試伺服器上暫時使用此規則。

- 建立具有公用存取且沒有 IP 位址的彈性伺服器
    ```azurecli-interactive
    az mysql flexible-server create --public-access none
    ```
    >[!Note]
    > 我們不建議您建立沒有任何防火牆規則的伺服器。 如果您未新增任何防火牆規則，則用戶端將無法連線至伺服器。

## <a name="create-and-manage-firewall-rule-after-server-create"></a>在伺服器建立之後建立和管理防火牆規則
**Az mysql 有彈性的伺服器防火牆規則**命令是從 Azure CLI 用來建立、刪除、列出、顯示及更新防火牆規則。

命令：
- **建立**：建立彈性的伺服器防火牆規則。
- **清單**：列出彈性的伺服器防火牆規則。
- **更新**：更新有彈性的伺服器防火牆規則。
- **顯示**：顯示有彈性的伺服器防火牆規則的詳細資料。
- **刪除**：刪除有彈性的伺服器防火牆規則。

請參閱 Azure CLI 參考檔 <!--FIXME --> 以取得可設定的 CLI 參數完整清單。 例如，在下列命令中，您可以選擇性地指定資源群組。

### <a name="create-a-firewall-rule"></a>建立防火牆規則
使用 `az mysql flexible-server firewall-rule create` 命令在伺服器上建立新的防火牆規則。
若要允許存取某個 IP 位址範圍，請提供 IP 位址作為起始 IP 位址和結束 IP 位址，如下列範例所示。
```azurecli-interactive
az mysql flexible-server firewall-rule create --name mydemoserver --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

若要允許單一 IP 位址的存取，請只提供單一 IP 位址，如下列範例所示。
```azurecli-interactive
az mysql flexible-server firewall-rule create --name mydemoserver --start-ip-address 1.1.1.1
```

若要允許來自 Azure IP 位址的應用程式連線到您的彈性伺服器，請提供 IP 位址0.0.0.0 作為起始 IP，如此範例所示。
```azurecli-interactive
az mysql flexible-server firewall-rule create --name mydemoserver --start-ip-address 0.0.0.0
```

> [!IMPORTANT]
> 此選項會設定防火牆，以允許從 azure 中的 Azure 服務和資源存取此伺服器，包括來自其他客戶訂用帳戶的連接。 選取這個選項時，請確定您的登入和使用者權限會限制為只有授權的使用者才能存取。
> 

成功時，每個建立命令輸出會以 JSON 格式 (依預設) 列出您已建立之防火牆規則的詳細資料。 如果失敗，輸出就會改為顯示錯誤訊息文字。

### <a name="list-firewall-rules"></a>列出防火牆規則 
使用 `az mysql flexible-server firewall-rule list` 命令可列出伺服器上現有的伺服器防火牆規則。 請注意，伺服器名稱屬性是在 **--name** 參數中指定。
```azurecli-interactive
az mysql flexible-server firewall-rule list --name mydemoserver
```
輸出會以 JSON 格式 (依預設) 列出規則 (若有的話)。 您可以使用--output table * * 參數，以更容易閱讀的資料表格式輸出結果。
```azurecli-interactive
az mysql flexible-server firewall-rule list --name mydemoserver --output table
```

### <a name="update-a-firewall-rule"></a>更新防火牆規則
使用 `az mysql flexible-server firewall-rule update` 命令來補救伺服器上的現有防火牆規則。 提供現有防火牆規則的名稱作為輸入，以及要更新的起始 IP 位址和結束 IP 位址屬性。
```azurecli-interactive
az mysql flexible-server firewall-rule update --name mydemoserver --rule-name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
成功時，命令輸出會以 JSON 格式 (依預設) 列出您已更新之防火牆規則的詳細資料。 如果失敗，輸出就會改為顯示錯誤訊息文字。

> [!NOTE]
> 如果防火牆規則不存在，更新命令就會建立規則。

### <a name="show-firewall-rule-details"></a>顯示防火牆規則詳細資料
使用 `az mysql flexible-server firewall-rule show` 命令以顯示伺服器上現有的防火牆規則詳細資料。 提供現有防火牆規則的名稱作為輸入。
```azurecli-interactive
az mysql flexible-server firewall-rule show --name mydemoserver --rule-name FirewallRule1
```
成功時，命令輸出會以 JSON 格式 (依預設) 列出您已指定之防火牆規則的詳細資料。 如果失敗，輸出就會改為顯示錯誤訊息文字。

### <a name="delete-a-firewall-rule"></a>刪除防火牆規則
使用 `az mysql flexible-server firewall-rule delete` 命令從伺服器刪除現有的防火牆規則。 提供現有防火牆規則的名稱。
```azurecli-interactive
az mysql flexible-server firewall-rule delete --name mydemoserver --rule-name FirewallRule1
```
成功時，沒有任何輸出。 發生錯誤時，就會顯示錯誤訊息文字。

## <a name="next-steps"></a>下一步
- 深入瞭解[適用於 MySQL 的 Azure 資料庫彈性伺服器中的網路](./concepts-networking.md)功能
- 深入瞭解 [適用於 MySQL 的 Azure 資料庫彈性的伺服器防火牆規則](./concepts-networking.md#public-access-allowed-ip-addresses)
- [使用 Azure 入口網站建立和管理適用於 MySQL 的 Azure 資料庫彈性伺服器防火牆規則](./how-to-manage-firewall-portal.md)。
