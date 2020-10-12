---
title: 防火牆規則-超大規模 (Citus) -適用於 PostgreSQL 的 Azure 資料庫
description: 本文說明適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) 的防火牆規則。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 559c5eca6fa8a6eceb37ade003d4f1983c0a1a1b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90902089"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---hyperscale-citus"></a>適用於 PostgreSQL 的 Azure 資料庫-超大規模 (Citus 中的防火牆規則) 
除非您指定哪些電腦擁有許可權，否則適用於 PostgreSQL 的 Azure 資料庫伺服器防火牆會防止對超大規模 (Citus) 協調器節點的所有存取。 此防火牆會根據每一個要求的來源 IP 位址來授與伺服器存取權。
若要設定您的防火牆，您可以建立防火牆規則，指定可接受的 IP 位址範圍。 您可以在伺服器層級建立防火牆規則。

**防火牆規則：** 這些規則可讓用戶端存取您的超大規模 (Citus) 協調器節點，也就是相同邏輯伺服器內的所有資料庫。 您可以使用 Azure 入口網站來設定伺服器層級防火牆規則。 若要建立伺服器層級的防火牆規則，您必須是訂用帳戶擁有者或訂用帳戶參與者。

## <a name="firewall-overview"></a>防火牆概觀
依預設，防火牆會封鎖對您的協調器節點的所有資料庫存取。 若要從其他電腦開始使用您的伺服器，請指定一或多個伺服器層級的防火牆規則，以便啟用對伺服器的存取。 使用防火牆規則，來指定允許從網際網路存取的 IP 位址範圍。 存取 Azure 入口網站本身，並不會受到防火牆規則所影響。
來自網際網路和 Azure 的連接嘗試必須先通過防火牆，才能到達您的 PostgreSQL 資料庫，如下圖所示：

:::image type="content" source="media/concepts-hyperscale-firewall-rules/1-firewall-concept.png" alt-text="防火牆運作方式的範例流程":::

## <a name="connecting-from-the-internet-and-from-azure"></a>從網際網路和 Azure 連接

超大規模 (Citus) 伺服器群組防火牆可控制哪些人可以連接到群組的協調器節點。 防火牆會藉由諮詢可設定的規則清單來決定存取權。 每個規則都是中允許的 IP 位址或位址範圍。

當防火牆封鎖連線時，可能會導致應用程式錯誤。 例如，使用於 postgresql JDBC 驅動程式會引發如下的錯誤：

> java.util.concurrent.ExecutionException： RuntimeException： org. 于 postgresql. util. Org.postgresql.util.psqlexception：嚴重：沒有 \_ 主機 "123.45.67.890"、使用者 "citus"、資料庫 "citus"、SSL 的 pg 專案

請參閱 [建立和管理防火牆規則](howto-hyperscale-manage-firewall-using-portal.md) ，以瞭解如何定義規則。

## <a name="troubleshooting-the-database-server-firewall"></a>針對資料庫伺服器防火牆問題進行疑難排解
當存取于 postgresql-超大規模 (Citus 的 Microsoft Azure 資料庫時，) 服務無法如預期般運作，請考慮下列幾點：

* **允許清單的變更尚未生效：** 對超大規模所做的變更可能會有最多五分鐘的延遲， (Citus) 防火牆設定才會生效。

* **使用者未獲授權或使用了不正確的密碼：** 如果使用者沒有伺服器的許可權，或所使用的密碼不正確，則會拒絕與伺服器的連接。 建立防火牆設定只是讓用戶端有機會嘗試連線到您的伺服器；每個用戶端仍然必須提供必要的安全性認證。

例如，使用 JDBC 用戶端，可能會出現下列錯誤。
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: password authentication failed for user "yourusername"

* **動態 IP 位址：** 如果您有使用動態 IP 位址的網際網路連線，並且在通過防火牆時遇到問題，您可以嘗試下列其中一個解決方案：

* 要求您的網際網路服務提供者 (ISP) 指派給用戶端電腦的 IP 位址範圍，以存取超大規模 (Citus) 協調器節點，然後將 IP 位址範圍新增為防火牆規則。

* 改為針對您的用戶端電腦取得靜態 IP 位址，然後將該靜態 IP 位址新增為防火牆規則。

## <a name="next-steps"></a>後續步驟
如需有關建立伺服器層級和資料庫層級防火牆規則的文章，請參閱：
* [使用 Azure 入口網站建立和管理適用於 PostgreSQL 的 Azure 資料庫防火牆規則](howto-hyperscale-manage-firewall-using-portal.md)
