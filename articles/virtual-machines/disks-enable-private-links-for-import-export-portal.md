---
title: Azure 入口網站 - 使用 Private Link 限制受控磁碟的匯入/匯出存取
description: 使用 Azure 入口網站為受控磁碟啟用 Private Link (目前為預覽狀態)。 允許您在虛擬網路內安全地匯出和匯入磁碟。
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 95c4464d1ab8416f609f75f2b59fb85a578ef5b7
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979048"
---
# <a name="use-the-azure-portal-to-restrict-importexport-access-for-managed-disks-with-private-links"></a>使用 Azure 入口網站，透過 Private Link 限制受控磁碟的匯入/匯出存取

受控磁碟的 Private Links 支援目前為預覽狀態，可讓您限制受控磁碟的匯出和匯入，使其只會在您的 Azure 虛擬網路中進行。 您可以為未連結的受控磁碟和快照集產生時間繫結的共用存取簽章 (SAS) URI，以將資料匯出至其他區域進列區域擴充、災害復原，以及讀取資料以進行取證分析。 您也可以使用 SAS URI，直接將 VHD 從內部部署環境上傳至空白磁碟。 虛擬網路上的用戶端與受控磁碟之間的網路流量會流經虛擬網路和 Microsoft 骨幹網路上的私人連結，以排除對網際網路公開的風險。

您可以建立磁碟存取資源，並藉由建立私人端點，將其連結至相同訂用帳戶中的虛擬網路。 您必須將磁碟或快照集與磁碟存取相關聯，才能透過 Private Link 匯出和匯入資料。 同時也必須將磁碟或快照集的 NetworkAccessPolicy 屬性設定為 `AllowPrivate`。 

您可以將 NetworkAccessPolicy 屬性設定為 `DenyAll`，以防止任何人產生磁碟或快照集的 SAS URI。 NetworkAccessPolicy 屬性的預設值是 `AllowAll`。

## <a name="limitations"></a>限制

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../includes/virtual-machines-disks-private-links-limitations.md)]

## <a name="regional-availability"></a>區域可用性

[!INCLUDE [virtual-machines-disks-private-links-regions](../../includes/virtual-machines-disks-private-links-regions.md)]

## <a name="create-a-disk-access-resource"></a>建立磁碟存取資源

1. 登入 Azure 入口網站，然後使用[此連結](https://aka.ms/disksprivatelinks)瀏覽至 **磁碟存取**。

    > [!IMPORTANT]
    > 您必須使用[提供的連結](https://aka.ms/disksprivatelinks)瀏覽至 [磁碟存取] 刀鋒視窗。 若未使用此連結，則「磁碟存取」目前不會顯示在公用入口網站中。

1. 選取 [+新增] 以建立新的磁碟存取資源。
1. 在 [建立] 刀鋒視窗上，選取您的訂用帳戶、資源群組、輸入名稱，然後選取區域。
1. 選取 [檢閱 + 建立]。

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-create-basics.png" alt-text="磁碟存取建立刀鋒視窗的螢幕擷取畫面。填入想要的名稱、選取區域、選取資源群組，然後繼續":::

您可以直接瀏覽到已建立的資源。

:::image type="content" source="media/disks-enable-private-links-for-import-export-portal/screenshot-resource-button.png" alt-text="磁碟存取建立刀鋒視窗的螢幕擷取畫面。填入想要的名稱、選取區域、選取資源群組，然後繼續":::

## <a name="create-a-private-endpoint"></a>建立私人端點

現在您已有磁碟存取資源，可以用來處理磁碟匯出/匯入的存取權，這可透過私人端點完成。 因此，您必須建立並設定私人端點，以進行磁碟存取。

1. 從磁碟存取資源中，選取 [私人端點連線]。
1. 選取 [+私人端點]。

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-main-private-blade.png" alt-text="磁碟存取建立刀鋒視窗的螢幕擷取畫面。填入想要的名稱、選取區域、選取資源群組，然後繼續":::

1. 選取資源群組
1. 填入名稱，並選取與您磁碟存取資源建立位置相同的區域。
1. 完成時，選取 下一步:**資源 >**

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-first-blade.png" alt-text="磁碟存取建立刀鋒視窗的螢幕擷取畫面。填入想要的名稱、選取區域、選取資源群組，然後繼續":::

1. 選取 [資源] 刀鋒視窗中的 [連線到我目錄中的 Azure 資源]。
1. 在 [資源類型] 中，選取 [Microsoft.Compute/diskAccesses]
1. 針對 [資源]，選取您稍早建立的磁碟存取資源
1. 將 [目標子資源] 保留為**磁碟**
1. 選取 下一步：**組態 >** 。

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-second-blade.png" alt-text="磁碟存取建立刀鋒視窗的螢幕擷取畫面。填入想要的名稱、選取區域、選取資源群組，然後繼續":::

1. 選取要限制磁碟匯出的虛擬網路，其他虛擬網路將無法匯出您的磁碟。

    > [!NOTE]
    > 如果您已為選取的子網路啟用有網路安全性群組 (NGS)，則只會對此子網路上的私人端點停用。 子網路上的其他資源仍會強制使用 NSG。

1. 選取適當的子網路
1. 選取 [檢閱 + 建立]。

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-third-blade.png" alt-text="磁碟存取建立刀鋒視窗的螢幕擷取畫面。填入想要的名稱、選取區域、選取資源群組，然後繼續":::

## <a name="enable-private-endpoint-on-your-disk"></a>啟用磁碟上的私人端點

1. 瀏覽至您想要設定的磁碟
1. 選取 [網路功能]
1. 選取 [私人端點 (透過磁碟存取)]，並選取您稍早建立的磁碟存取。
1. 選取 [儲存]。

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-managed-disk-networking-blade.png" alt-text="磁碟存取建立刀鋒視窗的螢幕擷取畫面。填入想要的名稱、選取區域、選取資源群組，然後繼續":::

您現在已完成設定 Private Link，可在匯入/匯出受控磁碟時使用。

## <a name="next-steps"></a>後續步驟

- [Private Link 常見問題集](./faq-for-disks.md#private-links-for-securely-exporting-and-importing-managed-disks)
- [使用 PowerShell 將受控快照集匯出/複製到不同區域的儲存體帳戶當做 VHD](./scripts/virtual-machines-powershell-sample-copy-snapshot-to-storage-account.md)