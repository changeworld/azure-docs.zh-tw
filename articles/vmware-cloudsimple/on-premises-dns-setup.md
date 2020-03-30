---
title: Azure VMware 解決方案（按雲簡單 ） - 為雲簡單私有雲配置 DNS
description: 描述如何設置 DNS 名稱解析，以便從本地工作站訪問雲簡單私有雲上的 vCenter 伺服器
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c2d69d21eb46d502a45c9df1dfaaa947d26ef7c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246106"
---
# <a name="configure-dns-for-name-resolution-for-private-cloud-vcenter-access-from-on-premises-workstations"></a>為來自本地工作站的私有雲 vCenter 訪問配置名稱解析

要從本地工作站訪問雲簡單私有雲上的 vCenter 伺服器，必須配置 DNS 位址解析，以便 vCenter 伺服器可以通過主機名稱和 IP 位址進行定址。

## <a name="obtain-the-ip-address-of-the-dns-server-for-your-private-cloud"></a>獲取私有雲的 DNS 伺服器 IP 位址

1. 登錄到[雲簡單門戶](access-cloudsimple-portal.md)。

2. 導航到**資源** > **私有雲**，然後選擇要連接到的私有雲。

3. 在 **"基本資訊**"下的私有雲**摘要**頁上，複製私有雲 DNS 伺服器 IP 位址。

    ![私有雲 DNS 伺服器](media/private-cloud-dns-server.png)


將這些選項之一用於 DNS 配置。

* [在 DNS 伺服器上為 *.cloudsimple.io 創建區域](#create-a-zone-on-a-microsoft-windows-dns-server)
* [在本地 DNS 伺服器上創建條件轉寄站以解決 *.cloudsimple.io](#create-a-conditional-forwarder)

## <a name="create-a-zone-on-the-dns-server-for-cloudsimpleio"></a>在 DNS 伺服器上為 *.cloudsimple.io 創建區域

您可以將地區設定為存根區域，並指向私有雲上的 DNS 伺服器以進行名稱解析。 本節提供有關使用 BIND DNS 伺服器或 Microsoft Windows DNS 伺服器的資訊。

### <a name="create-a-zone-on-a-bind-dns-server"></a>在 BIND DNS 伺服器上創建區域

要配置的特定檔和參數可能因您的單個 DNS 設置而異。

例如，對於預設的 BIND 伺服器配置，請在 DNS 伺服器上編輯 /etc/命名.conf 檔並添加以下區域資訊。

```
zone "az.cloudsimple.io"
{
    type stub;
    masters { IP address of DNS servers; };
    file "slaves/cloudsimple.io.db";
};
```

### <a name="create-a-zone-on-a-microsoft-windows-dns-server"></a>在 Microsoft Windows DNS 伺服器上創建區域

1. 按右鍵 DNS 伺服器並選擇 **"新區域**"。 
  
    ![新區](media/DNS01.png)
2. 選擇**存根區域**，然後按一下 **"下一步**"。

    ![新區](media/DNS02.png)
3. 根據您的環境選擇適當的選項，然後按一下 **"下一步**"。

    ![新區](media/DNS03.png)
4. 選擇 **"前進查找區域**"，然後按一下 **"下一步**"。

    ![新區](media/DNS01.png)
5. 輸入區功能變數名稱稱，然後按一下 **"下一步**"。

    ![新區](media/DNS05.png)
6. 輸入從雲簡單門戶獲取的私有雲 DNS 伺服器的 IP 位址。

    ![新區](media/DNS06.png)
7. 根據需要按一下 **"下一步**"以完成嚮導設置。

## <a name="create-a-conditional-forwarder"></a>創建條件轉寄站

條件轉寄站將所有 DNS 名稱解析請求轉發到指定的伺服器。 通過此設置，對 @.cloudsimple.io的任何請求將轉發到位於私有雲上的 DNS 伺服器。 以下示例演示如何在不同類型的 DNS 伺服器上設置轉寄站。

### <a name="create-a-conditional-forwarder-on-a-bind-dns-server"></a>在 BIND DNS 伺服器上創建條件轉寄站

要配置的特定檔和參數可能因您的單個 DNS 設置而異。

例如，對於預設的 BIND 伺服器配置，在 DNS 伺服器上編輯 /etc/命名.conf 檔，並添加以下條件轉發資訊。

```
zone "az.cloudsimple.io" {
    type forward;
    forwarders { IP address of DNS servers; };
};
```

### <a name="create-a-conditional-forwarder-on-a-microsoft-windows-dns-server"></a>在 Microsoft Windows DNS 伺服器上創建條件轉寄站

1. 打開 DNS 伺服器上的 DNS 管理器。
2. 按右鍵 **"條件轉寄站**"並選擇添加新條件轉寄站的選項。

    ![條件轉寄站 1 視窗 DNS](media/DNS08.png)
3. 在私有雲中輸入 DNS 域和 DNS 伺服器的 IP 位址，然後按一下"**確定**"。
