---
title: Azure 序列主控台錯誤 |Microsoft Docs
description: Azure 序列主控台中的常見錯誤
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: 61ae0ef92fe522a2a038a6076a5e0c0a10ee47b6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80060697"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Azure 序列主控台中的常見錯誤
Azure 序列主控台內有一組已知錯誤。 這是這些錯誤和風險降低步驟的清單。

## <a name="common-errors"></a>常見錯誤

錯誤                             |   降低
:---------------------------------|:--------------------------------------------|
「Azure 序列主控台需要啟用開機診斷。 按一下這裡，為您的虛擬機器設定開機診斷。」 | 請確認 VM 或虛擬機器擴展集已啟用[開機診斷](boot-diagnostics.md)。 如果您在虛擬機器擴展集實例上使用序列主控台，請確定您的實例具有最新的模型。
「Azure 序列主控台需要虛擬機器正在執行。 使用上方的 [啟動] 按鈕來啟動您的虛擬機器。  | VM 或虛擬機器擴展集實例必須處於已啟動狀態，才能存取序列主控台（您的 VM 不得停止或解除配置）。 請確認您的 VM 或虛擬機器擴展集實例正在執行中，然後再試一次。
「此訂用帳戶未啟用 Azure 序列主控台，請聯絡您的訂用帳戶管理員以啟用。」 | Azure 序列主控台可以在訂用帳戶層級停用。 如果您是訂用帳戶管理員，您可以[啟用和停用 Azure 序列主控台](./serial-console-enable-disable.md)。 如果您不是訂用帳戶管理員，您應該與您的訂用帳戶管理員聯繫，以進行後續步驟。
存取此 VM 的開機診斷儲存體帳戶時，遇到「禁止」回應。 | 請確定開機診斷沒有帳戶防火牆。 必須要有可存取的開機診斷儲存體帳戶，序列主控台才能運作。 根據設計，序列主控台無法與開機診斷儲存體帳戶上啟用的儲存體帳戶防火牆搭配使用。
您沒有可使用此 VM 與序列主控台的必要權限。 請確定您至少具有「虛擬機器參與者」角色權限。| 若要存取序列主控台，您必須在 VM 或虛擬機器擴展集上具有參與者層級的存取權或更新版本。 如需詳細資訊，請參閱[總覽頁面](serial-console-overview.md)。
找不到此 VM 上用於開機診斷的儲存體帳戶 ' '。 請確認此 VM 已啟用開機診斷，此儲存體帳戶尚未刪除，而且您可以存取此儲存體帳戶。 | 再次確認您未刪除 VM 或虛擬機器擴展集的開機診斷儲存體帳戶
與 VM 的序列主控台連線發生錯誤：「不正確的要求」（400） | 如果您的開機診斷 URI 不正確，就可能發生這種情況。 例如，使用 "HTTP://"，而不是 "HTTPs://"。 您可以使用下列命令來修正開機診斷 URI：`az vm boot-diagnostics enable --name vmName --resource-group rgName --storage https://<storageAccountUri>.blob.core.windows.net/`
您沒有必要的許可權，無法寫入此 VM 的開機診斷儲存體帳戶。 請確定您至少有 VM 參與者許可權 | 序列主控台存取需要開機診斷儲存體帳戶的參與者層級存取權。 如需詳細資訊，請參閱[總覽頁面](serial-console-overview.md)。
無法判斷開機診斷儲存體帳戶* &lt; STORAGEACCOUNTNAME &gt; *的資源群組。 請確認已針對此 VM 啟用開機診斷，且您具有此儲存體帳戶的存取權。 | 序列主控台存取需要開機診斷儲存體帳戶的參與者層級存取權。 如需詳細資訊，請參閱[總覽頁面](serial-console-overview.md)。
此 VM 的布建尚未成功。 請確定已完全部署 VM，然後重試序列主控台連線。 | 您的 VM 或虛擬機器擴展集可能仍在布建中。 請稍候片刻，然後再試一次。
Web 通訊端已關閉或無法開啟。 | 您可能需要將防火牆存取新增至 `*.console.azure.com` 。 更詳細但較長的方法是允許防火牆存取 Microsoft Azure 的[資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)，這會定期變更。
序列主控台無法搭配使用具有階層命名空間之 Azure Data Lake Storage Gen2 的儲存體帳戶使用。 | 這是階層命名空間的已知問題。 若要減輕問題，請確定您的 VM 開機診斷儲存體帳戶不是使用 Azure Data Lake Storage Gen2 建立的。 只有在建立儲存體帳戶時，才能設定此選項。 您可能必須在未啟用 Azure Data Lake Storage Gen2 的情況下，建立個別的開機診斷儲存體帳戶來減輕此問題。
與 VM 的序列主控台連線發生錯誤：「禁止」（SubscriptionNotEnabled）-未定義訂用帳戶名稱，識別碼 \<subscription id> 處於未啟用狀態（未定義） | 如果使用者已在中建立其 Cloud Shell 儲存體帳戶的訂用帳戶已停用，則可能會發生此問題。 若要減輕問題，請啟動 Cloud Shell 並[執行必要步驟](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage#unmount-clouddrive-1)，以在目前的訂用帳戶中重新布建 Cloud Shell 的備份儲存體帳戶。

## <a name="next-steps"></a>後續步驟
* 深入瞭解[適用于 Linux vm 的 Azure 序列主控台](./serial-console-linux.md)
* 深入瞭解[適用于 Windows vm 的 Azure 序列主控台](./serial-console-windows.md)