---
title: 防火牆規則 - 超大規模（Citus） - 用於後格雷SQL的 Azure 資料庫
description: 本文介紹了後格雷SQL - 超大規模 （Citus） 的 Azure 資料庫的防火牆規則。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: b843cd1528630a21255053f623356a0379daacf6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975562"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure 資料庫中的防火牆規則，用於後格雷SQL - 超大規模（Citus）
用於 PostgreSQL 伺服器防火牆的 Azure 資料庫阻止對超大規模 （Citus） 協調器節點的所有訪問，直到指定哪些電腦具有許可權。 此防火牆會根據每一個要求的來源 IP 位址來授與伺服器存取權。
若要設定您的防火牆，您可以建立防火牆規則，指定可接受的 IP 位址範圍。 您可以在伺服器層級建立防火牆規則。

**防火牆規則：** 這些規則使用戶端能夠訪問超量程 （Citus） 協調節點，即同一邏輯伺服器中的所有資料庫。 可以使用 Azure 門戶佈建服務器級防火牆規則。 若要建立伺服器層級的防火牆規則，您必須是訂用帳戶擁有者或訂用帳戶參與者。

## <a name="firewall-overview"></a>防火牆概觀
預設情況下，防火牆會阻止對協調器節點的所有資料庫訪問。 若要從其他電腦開始使用您的伺服器，請指定一或多個伺服器層級的防火牆規則，以便啟用對伺服器的存取。 使用防火牆規則，來指定允許從網際網路存取的 IP 位址範圍。 存取 Azure 入口網站本身，並不會受到防火牆規則所影響。
來自網際網路和 Azure 的連接嘗試必須先通過防火牆，才能到達您的 PostgreSQL 資料庫，如下圖所示：

![防火牆運作方式的範例流程](media/concepts-hyperscale-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet-and-from-azure"></a>從 Internet 和 Azure 連接

Hyperscale （Citus） 伺服器組防火牆控制誰可以連接到組的協調器節點。 防火牆通過查閱可配置的規則清單來確定訪問。 每個規則都是允許進入的 IP 位址或位址範圍。

當防火牆阻止連接時，它可能會導致應用程式錯誤。 例如，使用 PostgreSQL JDBC 驅動程式會引發這樣的錯誤：

> java.util.併發.執行例外：java.lang.RuntimeException：org.postgresql.util.PSQLException：FATAL：主機\_"123.45.67.890"沒有pg hba.conf條目，使用者"citus"，資料庫"citus"，SSL

請參閱[創建和管理防火牆規則](howto-hyperscale-manage-firewall-using-portal.md)以瞭解如何定義規則。

## <a name="troubleshooting-the-database-server-firewall"></a>針對資料庫伺服器防火牆問題進行疑難排解
當訪問 Microsoft Azure 資料庫的 PostgreSQL - 超大規模 （Citus） 服務不按預期運行時，請考慮以下要點：

* **對允許清單的更改尚未生效：** Hyperscale （Citus） 防火牆配置的更改可能延遲多達五分鐘。"

* **使用者未獲得授權或使用不正確的密碼：** 如果使用者對伺服器沒有許可權，或者使用的密碼不正確，則與伺服器的連接將被拒絕。 建立防火牆設定只是讓用戶端有機會嘗試連線到您的伺服器；每個用戶端仍然必須提供必要的安全性認證。

例如，使用 JDBC 用戶端，可能會出現下列錯誤。
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: password authentication failed for user "yourusername"

* **動態 IP 位址：** 如果您有使用動態 IP 位址的網際網路連線，並且在通過防火牆時遇到問題，您可以嘗試下列其中一個解決方案：

* 詢問 Internet 服務提供者 （ISP） 分配給訪問超量（Citus） 協調器節點的用戶端電腦的 IP 位址範圍，然後添加 IP 位址範圍作為防火牆規則。

* 改為針對您的用戶端電腦取得靜態 IP 位址，然後將該靜態 IP 位址新增為防火牆規則。

## <a name="next-steps"></a>後續步驟
如需有關建立伺服器層級和資料庫層級防火牆規則的文章，請參閱：
* [使用 Azure 入口網站建立和管理適用於 PostgreSQL 的 Azure 資料庫防火牆規則](howto-hyperscale-manage-firewall-using-portal.md)
