---
title: 為您的 Azure 認知搜尋服務設定 IP 防火牆
titleSuffix: Azure Cognitive Search
description: 設定 IP 控制原則，以限制對您 Azure 認知搜尋服務的存取。
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 3e8a94b6b9b71d2d71b634edd70ea4150652b143
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88932792"
---
# <a name="configure-ip-firewall-for-azure-cognitive-search"></a>設定 Azure 認知搜尋的 IP 防火牆

Azure 認知搜尋支援輸入防火牆支援的 IP 規則。 此模型為您的搜尋服務提供額外一層安全性，類似于您在 Azure 虛擬網路安全性群組中找到的 IP 規則。 使用這些 IP 規則，您就可以將搜尋服務設定為只能從一組核准的電腦和/或雲端服務存取。 從這些經過核准的電腦和服務組合存取儲存在搜尋服務中的資料，仍然需要呼叫者呈現有效的授權權杖。

> [!Important]
> 您可以使用 Azure 入口網站或 [管理 REST API 2020-03-13 版](/rest/api/searchmanagement/)，設定 Azure 認知搜尋服務的 IP 規則。

## <a name="configure-an-ip-firewall-using-the-azure-portal"></a><a id="configure-ip-policy"></a> 使用 Azure 入口網站設定 IP 防火牆

若要在 Azure 入口網站中設定 IP 存取控制原則，請移至您的 Azure 認知搜尋服務頁面，並選取導覽功能表上的 [ **網路** ]。 端點網路連線必須是 **公用**的。 如果您的連線設定為 [ **私人**]，則只能透過私人端點存取您的搜尋服務。

![顯示如何在 Azure 入口網站中設定 IP 防火牆的螢幕擷取畫面](./media/service-configure-firewall/azure-portal-firewall.png)

Azure 入口網站提供以 CIDR 格式指定 IP 位址和 IP 位址範圍的功能。 CIDR 標記法的範例是 8.8.8.0/24，代表範圍從8.8.8.0 到8.8.8.255 的 Ip。

> [!NOTE]
> 啟用 Azure 認知搜尋服務的 IP 存取控制原則之後，就會拒絕從允許的 IP 位址範圍清單外部的電腦對資料平面發出的所有要求。 設定 IP 規則時，會停用 Azure 入口網站的某些功能。 您將能夠查看和管理服務等級資訊，但基於安全性考慮，會限制入口網站存取索引資料和服務中的各種元件，例如索引、索引子和技能集定義。

### <a name="requests-from-your-current-ip"></a>來自您目前 IP 的要求

為了簡化開發工作，Azure 入口網站協助您識別用戶端電腦的 IP 並新增至允許清單。 然後，在您的電腦上執行的應用程式就可以存取您的 Azure 認知搜尋服務。

入口網站會自動偵測您的用戶端 IP 位址。 它可能是您電腦或網路閘道的用戶端 IP 位址。 將您的工作負載投入生產環境之前，請務必移除此 IP 位址。

若要將目前的 IP 新增至 Ip 清單，請核取 [ **新增您的用戶端 IP 位址**]。 然後選取 [儲存]。

![顯示如何設定 IP 防火牆設定以允許目前 IP 的螢幕擷取畫面](./media/service-configure-firewall/enable-current-ip.png)

## <a name="troubleshoot-issues-with-an-ip-access-control-policy"></a><a id="troubleshoot-ip-firewall"></a>針對 IP 存取控制原則問題進行疑難排解

您可以使用下列選項，針對 IP 存取控制原則問題進行疑難排解：

### <a name="azure-portal"></a>Azure 入口網站

為您的 Azure 認知搜尋服務啟用 IP 存取控制原則，會封鎖來自允許的 IP 位址範圍清單以外之電腦的所有要求，包括 Azure 入口網站。  您將能夠查看和管理服務等級資訊，但基於安全性考慮，會限制入口網站存取索引資料和服務中的各種元件，例如索引、索引子和技能集定義。 

### <a name="sdks"></a>SDK

當您使用 SDK 從不在允許清單中的電腦存取 Azure 認知搜尋服務時，會傳回一般的 **403 禁止** 回應，而且沒有其他詳細資料。 確認您帳戶的允許 IP 清單，並確定已針對您的搜尋服務更新正確的設定。

## <a name="next-steps"></a>後續步驟

如需透過 Private Link 存取搜尋服務的詳細資訊，請參閱下列文章：

* [建立私人端點，以 Azure 認知搜尋的安全連線](service-create-private-endpoint.md)