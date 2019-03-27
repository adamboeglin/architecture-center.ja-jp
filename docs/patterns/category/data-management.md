---
title: データ管理のパターン
titleSuffix: Cloud Design Patterns
description: データ管理はクラウド アプリケーションの重要な要素であり、品質属性のほとんどに影響します。 通常、パフォーマンス、スケーラビリティ、または可用性の理由から、データは複数のサーバーにまたがってさまざまな場所でホストされます。これによって、広範な課題が生じることがあります。 たとえば、データの整合性を維持する必要があります。また、通常はさまざまな場所にあるデータを同期する必要があります。
keywords: 設計パターン
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 25571a431836656856ed3f299455dfdb94ae3477
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58243053"
---
# <a name="data-management-patterns"></a><span data-ttu-id="cc540-106">データ管理のパターン</span><span class="sxs-lookup"><span data-stu-id="cc540-106">Data Management patterns</span></span>

[!INCLUDE [header](../../_includes/header.md)]

<span data-ttu-id="cc540-107">データ管理はクラウド アプリケーションの重要な要素であり、品質属性のほとんどに影響します。</span><span class="sxs-lookup"><span data-stu-id="cc540-107">Data management is the key element of cloud applications, and influences most of the quality attributes.</span></span> <span data-ttu-id="cc540-108">通常、パフォーマンス、スケーラビリティ、または可用性の理由から、データは複数のサーバーにまたがってさまざまな場所でホストされます。これによって、広範な課題が生じることがあります。</span><span class="sxs-lookup"><span data-stu-id="cc540-108">Data is typically hosted in different locations and across multiple servers for reasons such as performance, scalability or availability, and this can present a range of challenges.</span></span> <span data-ttu-id="cc540-109">たとえば、データの整合性を維持する必要があります。また、通常はさまざまな場所にあるデータを同期する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc540-109">For example, data consistency must be maintained, and data will typically need to be synchronized across different locations.</span></span>

|                        <span data-ttu-id="cc540-110">Pattern</span><span class="sxs-lookup"><span data-stu-id="cc540-110">Pattern</span></span>                         |                                                                  <span data-ttu-id="cc540-111">まとめ</span><span class="sxs-lookup"><span data-stu-id="cc540-111">Summary</span></span>                                                                  |
|--------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
|            [<span data-ttu-id="cc540-112">キャッシュ アサイド</span><span class="sxs-lookup"><span data-stu-id="cc540-112">Cache-Aside</span></span>](../cache-aside.md)            |                                            <span data-ttu-id="cc540-113">オンデマンドでデータをデータ ストアからキャッシュに読み込みます</span><span class="sxs-lookup"><span data-stu-id="cc540-113">Load data on demand into a cache from a data store</span></span>                                             |
|                   [<span data-ttu-id="cc540-114">CQRS</span><span class="sxs-lookup"><span data-stu-id="cc540-114">CQRS</span></span>](../cqrs.md)                   |                    <span data-ttu-id="cc540-115">個別のインターフェイスを使用して、データを更新する操作から、データを読み取る操作を分離します。</span><span class="sxs-lookup"><span data-stu-id="cc540-115">Segregate operations that read data from operations that update data by using separate interfaces.</span></span>                     |
|         [<span data-ttu-id="cc540-116">イベント ソース</span><span class="sxs-lookup"><span data-stu-id="cc540-116">Event Sourcing</span></span>](../event-sourcing.md)         |               <span data-ttu-id="cc540-117">追加専用のストアを使用して、ドメイン内のデータに実行されるアクションを記述する一連のイベントすべてを記録します。</span><span class="sxs-lookup"><span data-stu-id="cc540-117">Use an append-only store to record the full series of events that describe actions taken on data in a domain.</span></span>               |
|            [<span data-ttu-id="cc540-118">テーブルのインデックス作成</span><span class="sxs-lookup"><span data-stu-id="cc540-118">Index Table</span></span>](../index-table.md)            |                         <span data-ttu-id="cc540-119">クエリによって頻繁に参照されるデータ ストア内のフィールドにインデックスを作成します。</span><span class="sxs-lookup"><span data-stu-id="cc540-119">Create indexes over the fields in data stores that are frequently referenced by queries.</span></span>                          |
|      [<span data-ttu-id="cc540-120">具体化されたビュー</span><span class="sxs-lookup"><span data-stu-id="cc540-120">Materialized View</span></span>](../materialized-view.md)      | <span data-ttu-id="cc540-121">データの形式が必要なクエリ操作に適していない場合に、1 つ以上のデータ ストアのデータの事前設定されたビューを生成します。</span><span class="sxs-lookup"><span data-stu-id="cc540-121">Generate prepopulated views over the data in one or more data stores when the data isn't ideally formatted for required query operations.</span></span> |
|               [<span data-ttu-id="cc540-122">シャーディング</span><span class="sxs-lookup"><span data-stu-id="cc540-122">Sharding</span></span>](../sharding.md)               |                                    <span data-ttu-id="cc540-123">データ ストアを水平方向のパーティションまたはシャードのセットに分割します。</span><span class="sxs-lookup"><span data-stu-id="cc540-123">Divide a data store into a set of horizontal partitions or shards.</span></span>                                     |
| [<span data-ttu-id="cc540-124">静的コンテンツ ホスティング</span><span class="sxs-lookup"><span data-stu-id="cc540-124">Static Content Hosting</span></span>](../static-content-hosting.md) |                   <span data-ttu-id="cc540-125">静的なコンテンツを、クライアントに直接配信できるクラウドベースのストレージ サービスにデプロイします。</span><span class="sxs-lookup"><span data-stu-id="cc540-125">Deploy static content to a cloud-based storage service that can deliver them directly to the client.</span></span>                    |
|              [<span data-ttu-id="cc540-126">バレット キー</span><span class="sxs-lookup"><span data-stu-id="cc540-126">Valet Key</span></span>](../valet-key.md)              |                 <span data-ttu-id="cc540-127">特定のリソースまたはサービスへの限定的な直接アクセスをクライアントに提供する、トークンまたはキーを使用します。</span><span class="sxs-lookup"><span data-stu-id="cc540-127">Use a token or key that provides clients with restricted direct access to a specific resource or service.</span></span>                 |
