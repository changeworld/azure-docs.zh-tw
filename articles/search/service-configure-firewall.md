---
title: 設定 Azure 認知搜尋服務的 IP 防火牆
titleSuffix: Azure Cognitive Search
description: 設定 IP 控制原則，以限制對您的 Azure 認知搜尋服務的存取。
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 699715e1188616c2d6bda47016ec1ea7b05cef83
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "83125571"
---
# <a name="configure-ip-firewall-for-azure-cognitive-search"></a>設定 Azure 認知搜尋的 IP 防火牆

Azure 認知搜尋支援輸入防火牆支援的 IP 規則。 此模型為您的搜尋服務提供一層額外的安全性，與您在 Azure 虛擬網路安全性群組中找到的 IP 規則類似。 使用這些 IP 規則，您可以將搜尋服務設定為只能從一組核准的電腦和/或雲端服務存取。 從這些已核准的機器和服務集合存取儲存在搜尋服務中的資料，仍會要求呼叫者出示有效的授權權杖。

> [!Important]
> 您可以使用 Azure 入口網站或[Management REST API 2020-03-13 版](https://docs.microsoft.com/rest/api/searchmanagement/)來設定 Azure 認知搜尋服務的 IP 規則。

## <a name="configure-an-ip-firewall-using-the-azure-portal"></a><a id="configure-ip-policy"></a>使用 Azure 入口網站設定 IP 防火牆

若要在 Azure 入口網站中設定 IP 存取控制原則，請移至您的 Azure 認知搜尋服務頁面，然後選取流覽功能表上的 [**網路**]。 端點網路連線能力必須是**公用**的。 如果您的連線設定為 [**私人**]，則只能透過私人端點存取您的搜尋服務。

![螢幕擷取畫面，顯示如何在 Azure 入口網站中設定 IP 防火牆](./media/service-configure-firewall/azure-portal-firewall.png)

Azure 入口網站提供以 CIDR 格式指定 IP 位址和 IP 位址範圍的能力。 CIDR 標記法的範例是 8.8.8.0/24，代表從8.8.8.0 到8.8.8.255 的 Ip 範圍。

> [!NOTE]
> 啟用 Azure 認知搜尋服務的 IP 存取控制原則之後，所有從允許的 IP 位址範圍清單外部的電腦對資料平面提出的要求都會遭到拒絕。 設定 IP 規則時，會停用 Azure 入口網站的某些功能。 您將能夠查看和管理服務層級資訊，但基於安全性考慮，會限制入口網站存取索引資料和服務中的各種元件（例如索引、索引子和技能集定義）。

### <a name="requests-from-your-current-ip"></a>來自您目前 IP 的要求

為了簡化開發工作，Azure 入口網站協助您識別用戶端電腦的 IP 並新增至允許清單。 然後，在您的電腦上執行的應用程式就可以存取您的 Azure 認知搜尋服務。

入口網站會自動偵測您的用戶端 IP 位址。 它可能是您電腦或網路閘道的用戶端 IP 位址。 將您的工作負載帶到生產環境之前，請務必移除此 IP 位址。

若要將目前的 IP 新增至 Ip 清單，請選取 [**新增您的用戶端 IP 位址**]。 接著，選取 [儲存]。

![螢幕擷取畫面，顯示如何設定 IP 防火牆設定，以允許目前的 IP](./media/service-configure-firewall/enable-current-ip.png)

## <a name="troubleshoot-issues-with-an-ip-access-control-policy"></a><a id="troubleshoot-ip-firewall"></a>針對 IP 存取控制原則問題進行疑難排解

您可以使用下列選項，針對 IP 存取控制原則問題進行疑難排解：

### <a name="azure-portal"></a>Azure 入口網站

啟用 Azure 認知搜尋服務的 IP 存取控制原則，會封鎖來自允許的 IP 位址範圍清單以外電腦的所有要求，包括 Azure 入口網站。  您將能夠查看和管理服務層級資訊，但基於安全性考慮，會限制入口網站存取索引資料和服務中的各種元件（例如索引、索引子和技能集定義）。 

### <a name="sdks"></a>SDK

當您從不在允許清單中的電腦使用 SDK 來存取 Azure 認知搜尋服務時，會傳回一般的**403 禁止**回應，且不會有其他詳細資料。 請確認您帳戶的允許 IP 清單，並確定已針對您的搜尋服務更新正確的設定。

## <a name="next-steps"></a>後續步驟

如需透過私人連結存取搜尋服務的詳細資訊，請參閱下列文章：

* [建立私人端點以進行 Azure 認知搜尋的安全連線](service-create-private-endpoint.md)
