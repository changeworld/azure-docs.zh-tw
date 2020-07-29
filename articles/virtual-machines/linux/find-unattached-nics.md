---
title: 尋找及刪除未連結的 Azure Nic
description: 如何使用 Azure CLI 尋找並刪除未連結至 VM 的 Azure NIC
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: 5b28226cfe6bc51a2619c4dd63e666ddd51dad2a
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87268504"
---
# <a name="how-to-find-and-delete-unattached-network-interface-cards-nics-for-azure-vms"></a>如何尋找並刪除 Azure VM 未連結的網路介面卡 (NIC)
當您在 Azure 中刪除虛擬機器 (VM) 時，預設不會刪除網路介面卡 (NIC)。 如果您建立和刪除多個 VM，未使用的 NIC 會繼續使用內部 IP 位址租用。 當您建立其他 VM NIC 時，這些 NIC 可能無法取得子網路位址空間內的 IP 租用。 本文示範如何尋找並刪除未連結的 NIC。

## <a name="find-and-delete-unattached-nics"></a>尋找及刪除未連結的 NIC

NIC 的 virtualMachine** 屬性會儲存 NIC 所連結 VM 的識別碼和資源群組。 下列指令碼會針對訂用帳戶中的所有 NIC 重複執行，並檢查 virtualMachine** 屬性是否為 Null。 如果這個屬性為 Null，則 NIC 不會連結到 VM。

若要檢視所有未連結的 NIC，強烈建議您先執行指令碼並將 deleteUnattachedNics** 變數設為 0**。 若要在檢閱清單輸出後刪除所有未連結的 NIC，請執行指令碼並將 deleteUnattachedNics** 設為 1**。

```azurecli
# Set deleteUnattachedNics=1 if you want to delete unattached NICs
# Set deleteUnattachedNics=0 if you want to see the Id(s) of the unattached NICs
deleteUnattachedNics=0

unattachedNicsIds=$(az network nic list --query '[?virtualMachine==`null`].[id]' -o tsv)
for id in ${unattachedNicsIds[@]}
do
   if (( $deleteUnattachedNics == 1 ))
   then

       echo "Deleting unattached NIC with Id: "$id
       az network nic delete --ids $id
       echo "Deleted unattached NIC with Id: "$id
   else
       echo $id
   fi
done
```

## <a name="next-steps"></a>後續步驟

如需如何在 Azure 中建立和管理虛擬網路的詳細資訊，請參閱[建立和管理 VM 網路](tutorial-virtual-network.md)。
