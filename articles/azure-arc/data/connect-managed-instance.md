---
title: 連接到 Azure Arc 啟用的 SQL 受控執行個體
description: 連接到 Azure Arc 啟用的 SQL 受控執行個體
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 3277dc4d9c4485b117bfcfd1d6e130e7370cd8c2
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934354"
---
# <a name="connect-to-azure-arc-enabled-sql-managed-instance"></a>連接到 Azure Arc 啟用的 SQL 受控執行個體

本文說明如何連接到已啟用 Azure Arc 的 SQL 受控執行個體。 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="view-azure-arc-enabled-sql-managed-instances"></a>View Azure Arc 啟用的 SQL 受控實例

若要查看已啟用 Azure Arc 的 SQL 受控執行個體和外部端點，請使用下列命令：

```console
azdata arc sql mi list
```

輸出應該看起來像這樣︰

```console
Name    Replicas    ExternalEndpoint    State
------  ----------  ----------------  -------
sqldemo 1/1         10.240.0.4:32023  Ready
```

如果您使用 AKS 或 kubeadm 或 OpenShift 等，您可以從這裡複製外部 IP 和埠號碼，並使用您最愛的工具連接到 SQL server/Azure SQL 實例，例如 Azure Data Studio 或 SQL Server Management Studio。  不過，如果您使用快速啟動 VM，請參閱下列有關如何從 Azure 外部連線到該 VM 的特殊資訊。 

> [!NOTE]
> 您的公司原則可能會封鎖對 IP 和埠的存取，特別是在公用雲端中建立時。

## <a name="connect"></a>連線 

使用 Azure Data Studio、SQL Server Management Studio 或 SQLCMD 連接

開啟 Azure Data Studio，並使用上述的外部端點 IP 位址和埠號碼連線至您的實例。 如果您使用 Azure VM，您將需要 _公用_ IP 位址，其可使用 [azure 虛擬機器部署的特殊注意事項](#special-note-about-azure-virtual-machine-deployments)來識別。

例如：

- 伺服器：52.229.9.30、30913
- 使用者名稱： sa
- 密碼：在布建期間指定的 SQL 密碼

> [!NOTE]
> 您可以使用 Azure Data Studio [查看 SQL 受控實例儀表板](azure-data-studio-dashboards.md#view-the-sql-managed-instance-dashboards)。

若要使用 SQLCMD 或 Linux 或 Windows 進行連接，您可以使用類似的命令。 出現提示時，請輸入 SQL 密碼：

```bash
sqlcmd -S 52.229.9.30,30913 -U sa
```

## <a name="special-note-about-azure-virtual-machine-deployments"></a>關於 Azure 虛擬機器部署的特殊注意事項

如果您使用 Azure 虛擬機器，則端點 IP 位址不會顯示公用 IP 位址。 若要找出外部 IP 位址，請使用下列命令：

```console
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

然後，您可以將公用 IP 位址與埠結合，以建立您的連接。

您也可能需要透過網路安全性閘道公開 sql 實例的埠 (NSG) 。 若要允許通過 (NSG 的流量) 您必須新增規則，您可以使用下列命令來執行此動作。

若要設定規則，您必須知道您的 NSG 名稱，您可以使用下列命令來瞭解：

```console
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

擁有 NSG 的名稱之後，您就可以使用下列命令來新增防火牆規則。 此處的範例值會建立埠30913的 NSG 規則，並允許來自 **任何** 來源 IP 位址的連接。  這並不是安全性最佳做法！  您可以藉由指定您的用戶端 IP 位址專屬的-來源位址前置詞值，或是涵蓋您小組或組織 IP 位址的 IP 位址範圍，來更妥善地鎖定專案。

將下列參數的值取代 `--destination-port-ranges` 為您從 `azdata sql instance list` 上述 F 命令取得的埠號碼。

```console
az network nsg rule create -n db_port --destination-port-ranges 30913 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="next-steps"></a>下一步

- [查看 SQL 受控實例儀表板](azure-data-studio-dashboards.md#view-the-sql-managed-instance-dashboards)
- [在 Azure 入口網站中查看 SQL 受控執行個體](view-arc-data-services-inventory-in-azure-portal.md)
