---
title: 帳戶管理-Azure VMware Solution by CloudSimple 入口網站
description: 說明如何透過 CloudSimple 入口網站管理 Azure VMware 解決方案上的帳戶
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 00348d36c38fdbfdf69a2e4e80a4c720268b40c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "77025362"
---
# <a name="manage-accounts-on-the-azure-vmware-solution-by-cloudsimple-portal"></a>使用 CloudSimple 入口網站管理 Azure VMware 解決方案上的帳戶

當您建立 CloudSimple 服務時，它會在 CloudSimple 上建立帳戶。 此帳戶與服務所在的 Azure 訂用帳戶相關聯。 訂用帳戶中具有擁有者和參與者角色的所有使用者都可以存取 CloudSimple 入口網站。 您可以在 [帳戶] 頁面上找到與 CloudSimple 服務相關聯的 Azure 訂用帳戶識別碼和租使用者識別碼。

若要在 CloudSimple 入口網站中管理帳戶，請 [存取入口網站](access-cloudsimple-portal.md) ，然後選取側邊功能表上的 [ **帳戶** ]。

選取 [ **摘要** ] 以查看公司 CloudSimple 設定的相關資訊。 您的雲端設定目前容量會顯示出來，包括私用雲端的數目、總儲存體、vSphere 叢集設定、節點數目，以及計算核心數目。 如果目前的設定不符合您的需求，則會包含一個連結，以購買額外的節點。

## <a name="email-alerts"></a>電子郵件警示

您可以針對私人雲端設定的變更，新增任何您想要通知之人員的電子郵件地址。

1. 在 [ **其他電子郵件警示** ] 區域中，按一下 [ **加入新**的]。
2. 輸入電子郵件地址。
3. 按下 Return。  

若要移除專案，請按一下 [ **X**]。

## <a name="cloudsimple-operator-access"></a>CloudSimple 操作員存取

操作員存取設定可讓支援工程師登入 CloudSimple 入口網站，讓 CloudSimple 協助您進行疑難排解。  預設會啟用此設定。 當您登入客戶帳戶時，支援工程師所執行的所有動作都會記錄下來並可供您在**活動**  >  **審核**頁面上查看。

按一下 [ **啟用 CloudSimple 操作員存取** ] 開關以開啟或關閉存取。
