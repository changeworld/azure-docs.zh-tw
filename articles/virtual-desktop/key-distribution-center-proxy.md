---
title: 設定 Kerberos 金鑰發佈中心 proxy Windows 虛擬桌面-Azure
description: 如何設定 Windows 虛擬桌面主機集區以使用 Kerberos 金鑰發佈中心 proxy。
author: Heidilohr
ms.topic: how-to
ms.date: 01/26/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: f123659941eaeb3b8ceeb6a999abf836eb9cf5ea
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98798366"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy"></a>設定 Kerberos 金鑰發佈中心 proxy

本文將說明如何為您的主機集區設定 Kerberos 金鑰 Distribiution 中心 (KDC) proxy。 此 proxy 可讓組織在其企業界限之外向 Kerberos 進行驗證。 例如，您可以使用 KDC proxy 來啟用外部用戶端的智慧卡驗證。

## <a name="how-to-configure-the-kdc-proxy"></a>如何設定 KDC proxy

若要設定 KDC proxy：

1. 以系統管理員身分登入 Azure 入口網站。

2. 移至 [Windows 虛擬桌面] 頁面。

3. 選取您要啟用 KDC proxy 的主機集區，然後選取 [ **RDP 屬性**]。

    > [!div class="mx-imgBorder"]
    > ![Azure 入口網站頁面的螢幕擷取畫面，其中顯示使用者選取主機集區、範例主機集區的名稱，然後是 RDP 屬性。](media/rdp-properties.png)

4. 選取 [ **Advanced （Advanced** ）] 索引標籤，然後以下列格式輸入不含空格的值：

    > kdcproxyname： s：\<fqdn\>

    > [!div class="mx-imgBorder"]
    > ![螢幕擷取畫面，其中顯示已選取的 [Advanced] 索引標籤，其中包含如步驟4所述的輸入值。](media/advanced-tab-selected.png)

5. 選取 [儲存]。

6. 選取的主機集區現在應該會開始使用您輸入的 kdcproxyname 欄位來發出 RDP 連接檔案。

## <a name="next-steps"></a>後續步驟

遠端桌面服務中的 RDGateway 角色包括 KDC proxy 服務。 請參閱 [在 Windows 虛擬桌面中部署 RD 閘道角色](rd-gateway-role.md) ，以瞭解如何針對 Windows 虛擬桌面設定一個目標。
