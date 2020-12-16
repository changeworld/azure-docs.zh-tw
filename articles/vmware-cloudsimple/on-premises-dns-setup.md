---
title: Azure VMware Solution by CloudSimple-設定 CloudSimple 私用雲端的 DNS
description: 說明如何設定 DNS 名稱解析，以從內部部署工作站存取 CloudSimple 私人雲端上的 vCenter server
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7a2b2344bbb110cd4b35cc1f6428f61e48552b01
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97563002"
---
# <a name="configure-dns-for-name-resolution-for-private-cloud-vcenter-access-from-on-premises-workstations"></a>針對從內部部署工作站私人雲端 vCenter 存取設定 DNS 進行名稱解析

若要從內部部署工作站存取 CloudSimple 私人雲端上的 vCenter server，您必須設定 DNS 位址解析，以便主機名稱和 IP 位址可以定址 vCenter server。

## <a name="obtain-the-ip-address-of-the-dns-server-for-your-private-cloud"></a>取得私人雲端的 DNS 伺服器 IP 位址

1. 登入 [CloudSimple 入口網站](access-cloudsimple-portal.md)。

2. 流覽至 **資源**  >  **私** 用雲端，然後選取您想要連線的私人雲端。

3. 在私人雲端 [**基本資訊**] 的 [**摘要**] 頁面上，複製私人雲端 DNS 伺服器 IP 位址。

    ![私人雲端 DNS 伺服器](media/private-cloud-dns-server.png)


針對 DNS 設定，請使用下列其中一個選項。

* [在 DNS 伺服器上建立 *. cloudsimple.io 的區域](#create-a-zone-on-a-microsoft-windows-dns-server)
* [在您的內部部署 DNS 伺服器上建立條件轉寄站，以解析 *. cloudsimple.io](#create-a-conditional-forwarder)

## <a name="create-a-zone-on-the-dns-server-for-cloudsimpleio"></a>在 DNS 伺服器上建立 *. cloudsimple.io 的區域

您可以將區域設定為存根區域，並指向私人雲端上的 DNS 伺服器以進行名稱解析。 本節提供有關使用 BIND DNS 伺服器或 Microsoft Windows DNS 伺服器的資訊。

### <a name="create-a-zone-on-a-bind-dns-server"></a>在 BIND DNS 伺服器上建立區域

要設定的特定檔案和參數，會根據您的個別 DNS 設定而有所不同。

例如，針對預設系結伺服器設定，請在您的 DNS 伺服器上編輯/etc/named.conf 檔案，並新增下欄區域資訊。

> [!NOTE]
>本文包含詞彙從屬的參考，這是 Microsoft 不再使用的詞彙。 從軟體移除字詞時，我們會將它從本文中移除。

```
zone "az.cloudsimple.io"
{
    type stub;
    masters { IP address of DNS servers; };
    file "slaves/cloudsimple.io.db";
};
```

### <a name="create-a-zone-on-a-microsoft-windows-dns-server"></a>在 Microsoft Windows DNS 伺服器上建立區域

1. 在 DNS 伺服器上按一下滑鼠右鍵，然後選取 [ **新增區域**]。 
  
    ![反白顯示 [新增區域] 功能表選項的螢幕擷取畫面。](media/DNS01.png)
2. 選取 **存根區域** ，然後按 **[下一步]**。

    ![醒目顯示 [存根區域] 選項的螢幕擷取畫面。](media/DNS02.png)
3. 根據您的環境選取適當的選項，然後按 **[下一步]**。

    ![顯示區域資料複寫選項的螢幕擷取畫面。](media/DNS03.png)
4. 選取 **正向對應區域** ，然後按 **[下一步]**。

    ![醒目顯示 [正向對應區域] 選項的螢幕擷取畫面。](media/DNS01.png)
5. 輸入區功能變數名稱稱，然後按 **[下一步]**。

    ![顯示要在哪裡輸入區功能變數名稱稱的螢幕擷取畫面。](media/DNS05.png)
6. 輸入您從 CloudSimple 入口網站取得之私人雲端的 DNS 伺服器 IP 位址。

    ![新增區域](media/DNS06.png)
7. 視需要按一下 **[下一步** ] 以完成嚮導設定。

## <a name="create-a-conditional-forwarder"></a>建立條件式轉寄站

條件式轉寄站會將所有 DNS 名稱解析要求轉送到指定的伺服器。 使用此設定時，任何對 *. cloudsimple.io 的要求都會轉送到位於私人雲端上的 DNS 伺服器。 下列範例示範如何在不同類型的 DNS 伺服器上設定轉寄站。

### <a name="create-a-conditional-forwarder-on-a-bind-dns-server"></a>在 BIND DNS 伺服器上建立條件轉寄站

要設定的特定檔案和參數，會根據您的個別 DNS 設定而有所不同。

例如，針對預設系結伺服器設定，請在您的 DNS 伺服器上編輯/etc/named.conf 檔案，並新增下列條件式轉送資訊。

```
zone "az.cloudsimple.io" {
    type forward;
    forwarders { IP address of DNS servers; };
};
```

### <a name="create-a-conditional-forwarder-on-a-microsoft-windows-dns-server"></a>在 Microsoft Windows DNS 伺服器上建立條件轉寄站

1. 在 DNS 伺服器上開啟 [DNS 管理員]。
2. 以滑鼠右鍵按一下 [ **條件** 轉寄站]，然後選取選項來加入新的條件式轉寄站。

    ![條件轉寄站 1 Windows DNS](media/DNS08.png)
3. 輸入私人雲端中 DNS 伺服器的 DNS 網域和 IP 位址，然後按一下 **[確定]**。
