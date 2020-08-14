---
title: 針對登錄效能進行疑難排解
description: 登錄效能常見問題的徵兆、原因和解決方式
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 414e3a92b8ebd4ff58528fc5e9ec4794471bd775
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227108"
---
# <a name="troubleshoot-registry-performance"></a>針對登錄效能進行疑難排解

本文可協助您針對 Azure container registry 的效能來疑難排解可能會遇到的問題。 

## <a name="symptoms"></a>徵狀

可能包含下列一或多項：

* 使用 Docker CLI 提取或推送映射所花費的時間超過預期
* 將映射部署至服務（例如 Azure Kubernetes Service）所需的時間超過預期
* 您無法在預期的時間內完成大量的並行提取或推送作業
* 異地複寫登錄中的提取或推送作業所花費的時間超出預期，或推播失敗，發生錯誤 `Error writing blob` 或 `Error writing manifest`

## <a name="causes"></a>原因

* 您的網路連接速度可能會緩慢的登錄作業- [解決方案](#check-expected-network-speed)
* 用戶端[解決方案](#check-client-hardware)上的影像層壓縮或解壓縮可能變慢  
* 您在登錄服務層或環境中達到設定的限制- [解決方案](#review-configured-limits)
* 您的異地複寫登錄在附近的區域中有複本- [解決方案](#configure-geo-replicated-registry)
* 您正在從地理位置較遠的登錄複本進行提取- [解決方案](#configure-dns-for-geo-replicated-registry)

如果您未在此處解決問題，請參閱 [Advanced 疑難排解](#advanced-troubleshooting) 和 [後續步驟](#next-steps) 以取得其他選項。

## <a name="potential-solutions"></a>可能的解決方案

### <a name="check-expected-network-speed"></a>檢查預期的網路速度

檢查您的網際網路上傳和下載速度，或使用 AzureSpeed 之類的工具，從 Azure blob 儲存體測試 [上傳](https://www.azurespeed.com/Azure/Uploadß) 和 [下載](https://www.azurespeed.com/Azure/Download) ，以裝載登錄映射層。

請檢查您的映射大小是否符合支援的大小上限，以及您的登錄服務層支援的下載或上傳頻寬。 如果您的登錄是在「基本」或「標準」層中，請考慮升級以改善效能。 

若要將映射部署至其他服務，請檢查登錄和目標所在的區域。 請考慮在相同或網路關閉區域中尋找登錄和部署目標，以改善效能。

相關連結：

* [Azure Container Registry 服務層級](container-registry-skus.md)    
* [Container registry 常見問題](container-registry-faq.md)
* [Azure Blob 儲存體的效能和擴充性目標](../storage/blobs/scalability-targets.md)

### <a name="check-client-hardware"></a>檢查用戶端硬體

Docker 用戶端上的磁片類型和 CPU，可能會影響在提取或推送作業過程中，將用戶端上的映射層解壓縮或壓縮的速度。 例如，硬碟上的圖層解壓縮需要比固態磁片更長的時間。 比較 Azure container registry 和公用登錄（例如 Docker Hub）中可比較映射的提取作業。

### <a name="review-configured-limits"></a>審查設定的限制

如果您同時將多個或多個多層式映射推送或提取到您的登錄，請參閱支援的 ReadOps 和 WriteOps 的登錄服務層級限制。 如果您的登錄是在「基本」或「標準」層中，請考慮升級以增加限制。 同時也請洽詢您的網路服務提供者，瞭解有許多並行作業可能發生的網路節流。 

針對用戶端上的每個推送或提取作業，檢查您的 Docker daemon 設定，以取得最大並行上傳或下載。 如有需要，設定更高的限制。

由於每個影像層都需要個別的登錄讀取或寫入作業，因此請檢查映射中的圖層數目。 請考慮減少影像層數的策略。

相關連結：

* [Azure Container Registry 服務層級](container-registry-skus.md)
* [dockerd](https://docs.docker.com/engine/reference/commandline/dockerd/)

### <a name="configure-geo-replicated-registry"></a>設定異地複寫的登錄

將映射推送至異地複寫登錄的 Docker 用戶端，可能不會將所有映射層及其資訊清單推送至單一複寫區域。 發生這種情況的原因可能是 Azure 流量管理員會將登錄要求路由至最接近網路的已複寫登錄。 如果登錄有兩個「鄰近」複寫區域，則映像層和資訊清單可能會散發至兩個網站，因而在驗證資訊清單時造成推送作業失敗。

若要將推送映像時的最接近複本 DNS 解析最佳化，請在與推送作業來源相同的 Azure 區域中設定異地複寫登錄，如果是在 Azure 外部工作，則請在最接近的區域設定。

若要使用異地複寫的登錄進行作業疑難排解，您也可以暫時停用流量管理員路由傳送至一或多個複寫。

相關連結：

* [Azure 容器登錄中的異地複寫](container-registry-geo-replication.md)

### <a name="configure-dns-for-geo-replicated-registry"></a>設定異地複寫登錄的 DNS

如果來自異地複寫登錄的提取作業顯示速度變慢，則用戶端上的 DNS 設定可能會解析成地理位置較遠的 DNS 伺服器。 在此情況下，流量管理員可能會將要求路由傳送至接近 DNS 伺服器但遠超出用戶端的複本。 `nslookup`在 Linux) 上執行諸如或 (之類的工具 `dig` ，以判斷流量管理員將登錄要求路由至其中的複本。 例如：

```console
nslookup myregistry.azurecr.io
```

可能的解決方法是設定較接近的 DNS 伺服器。

相關連結：

* [Azure 容器登錄中的異地複寫](container-registry-geo-replication.md)
* [針對使用異地複寫登錄的推送作業進行疑難排解](container-registry-geo-replication.md#troubleshoot-push-operations-with-geo-replicated-registries)
* [暫時停用路由至複寫](container-registry-geo-replication.md#temporarily-disable-routing-to-replication)
* [流量管理員常見問題](../traffic-manager/traffic-manager-faqs.md)

### <a name="advanced-troubleshooting"></a>進階疑難排解

如果您的登錄資源許可權允許，請 [檢查登錄環境的健全狀況](container-registry-check-health.md)。 如果報告錯誤，請參閱可能解決方案的 [錯誤參考](container-registry-health-error-reference.md) 。

如果已在登錄中啟用 [資源記錄的收集](container-registry-diagnostics-audit-logs.md) ，請檢查 ContainterRegistryRepositoryEvents 記錄。 此記錄會儲存推播或提取事件等作業的資訊。 查詢記錄檔中的存放 [庫層級作業失敗](container-registry-diagnostics-audit-logs.md#repository-level-operation-failures)。 

相關連結：

* [診斷評估與審核的記錄](container-registry-diagnostics-audit-logs.md)
* [Container registry 常見問題](container-registry-faq.md)
* [Azure Container Registry 的最佳做法](container-registry-best-practices.md)

## <a name="next-steps"></a>後續步驟

如果您沒有在這裡解決問題，請參閱下列選項。

* 其他登錄疑難排解主題包括：
  * [針對登錄登入進行疑難排解](container-registry-troubleshoot-login.md)
  * [針對登錄的網路問題進行疑難排解](container-registry-troubleshoot-access.md)
* [社區支援](https://azure.microsoft.com/support/community/) 選項
* [Microsoft Q&A](https://docs.microsoft.com/answers/products/)
* [開啟支援票證](https://azure.microsoft.com/support/create-ticket/)


