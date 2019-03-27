---
title: パーティション分割による制限の回避
titleSuffix: Azure Application Architecture Guide
description: パーティション分割を使用して、データベース、ネットワーク、コンピューティングの制限に対処します。
author: MikeWasson
ms.date: 08/30/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: 76590d2a0c16df9d599e7d4a856a84b5e3bdcec8
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241783"
---
# <a name="partition-around-limits"></a><span data-ttu-id="428ce-103">パーティション分割による制限の回避</span><span class="sxs-lookup"><span data-stu-id="428ce-103">Partition around limits</span></span>

## <a name="use-partitioning-to-work-around-database-network-and-compute-limits"></a><span data-ttu-id="428ce-104">パーティション分割を使用して、データベース、ネットワーク、コンピューティングの制限に対処します。</span><span class="sxs-lookup"><span data-stu-id="428ce-104">Use partitioning to work around database, network, and compute limits</span></span>

<span data-ttu-id="428ce-105">クラウドでは、すべてのサービスにスケール アップ機能の制限があります。</span><span class="sxs-lookup"><span data-stu-id="428ce-105">In the cloud, all services have limits in their ability to scale up.</span></span> <span data-ttu-id="428ce-106">Azure サービスの制限については、「[Azure サブスクリプションとサービスの制限、クォータ、制約][azure-limits]」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="428ce-106">Azure service limits are documented in [Azure subscription and service limits, quotas, and constraints][azure-limits].</span></span> <span data-ttu-id="428ce-107">制限には、コア数、データベース サイズ、クエリ スループット、ネットワーク スループットが含まれます。</span><span class="sxs-lookup"><span data-stu-id="428ce-107">Limits include number of cores, database size, query throughput, and network throughput.</span></span> <span data-ttu-id="428ce-108">システムが十分な規模になると、1 つ以上の制限に達する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="428ce-108">If your system grows sufficiently large, you may hit one or more of these limits.</span></span> <span data-ttu-id="428ce-109">これらの制限を回避するには、パーティション分割を使用します。</span><span class="sxs-lookup"><span data-stu-id="428ce-109">Use partitioning to work around these limits.</span></span>

<span data-ttu-id="428ce-110">システムをパーティション分割する場合、次のようなさまざまな方法があります。</span><span class="sxs-lookup"><span data-stu-id="428ce-110">There are many ways to partition a system, such as:</span></span>

- <span data-ttu-id="428ce-111">データベース サイズ、データ I/O、または同時セッション数の制限を回避するには、データベースをパーティション分割します。</span><span class="sxs-lookup"><span data-stu-id="428ce-111">Partition a database to avoid limits on database size, data I/O, or number of concurrent sessions.</span></span>

- <span data-ttu-id="428ce-112">要求数やコンカレント接続数の制限を回避するには、キューまたはメッセージ バスをパーティション分割します。</span><span class="sxs-lookup"><span data-stu-id="428ce-112">Partition a queue or message bus to avoid limits on the number of requests or the number of concurrent connections.</span></span>

- <span data-ttu-id="428ce-113">App Service プランごとのインスタンス数の制限を回避するには、App Service Web アプリをパーティション分割します。</span><span class="sxs-lookup"><span data-stu-id="428ce-113">Partition an App Service web app to avoid limits on the number of instances per App Service plan.</span></span>

<span data-ttu-id="428ce-114">データベースは、"*行方向*"、"*列方向*"、または "*機能的*" にパーティション分割できます。</span><span class="sxs-lookup"><span data-stu-id="428ce-114">A database can be partitioned *horizontally*, *vertically*, or *functionally*.</span></span>

- <span data-ttu-id="428ce-115">シャーディングとも呼ばれる行方向のパーティション分割では、各パーティションがデータ セット全体のサブセットのデータを保持します。</span><span class="sxs-lookup"><span data-stu-id="428ce-115">In horizontal partitioning, also called sharding, each partition holds data for a subset of the total data set.</span></span> <span data-ttu-id="428ce-116">パーティションは同じデータ スキーマを共有します。</span><span class="sxs-lookup"><span data-stu-id="428ce-116">The partitions share the same data schema.</span></span> <span data-ttu-id="428ce-117">たとえば、名前が A ～ M で始まる顧客と N ～ Z で始まる顧客を、それぞれ異なるパーティションに配置します。</span><span class="sxs-lookup"><span data-stu-id="428ce-117">For example, customers whose names start with A&ndash;M go into one partition, N&ndash;Z into another partition.</span></span>

- <span data-ttu-id="428ce-118">列方向のパーティション分割では、各パーティションがデータ ストアに含まれる項目のフィールドのサブセットを保持します。</span><span class="sxs-lookup"><span data-stu-id="428ce-118">In vertical partitioning, each partition holds a subset of the fields for the items in the data store.</span></span> <span data-ttu-id="428ce-119">たとえば、アクセス頻度の高いフィールドとアクセス頻度の低いフィールドを、それぞれ異なるパーティションに配置します。</span><span class="sxs-lookup"><span data-stu-id="428ce-119">For example, put frequently accessed fields in one partition, and less frequently accessed fields in another.</span></span>

- <span data-ttu-id="428ce-120">機能的パーティション分割では、システム内の各有界コンテキストによって使用される方法に従ってデータをパーティション分割します。</span><span class="sxs-lookup"><span data-stu-id="428ce-120">In functional partitioning, data is partitioned according to how it is used by each bounded context in the system.</span></span> <span data-ttu-id="428ce-121">たとえば、請求書データと製品在庫データを、それぞれ異なるパーティションに格納します。</span><span class="sxs-lookup"><span data-stu-id="428ce-121">For example, store invoice data in one partition and product inventory data in another.</span></span> <span data-ttu-id="428ce-122">スキーマは独立しています。</span><span class="sxs-lookup"><span data-stu-id="428ce-122">The schemas are independent.</span></span>

<span data-ttu-id="428ce-123">詳細なガイダンスについては、「[Data partitioning (データのパーティション分割)][data-partitioning-guidance]」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="428ce-123">For more detailed guidance, see [Data partitioning][data-partitioning-guidance].</span></span>

## <a name="recommendations"></a><span data-ttu-id="428ce-124">Recommendations</span><span class="sxs-lookup"><span data-stu-id="428ce-124">Recommendations</span></span>

<span data-ttu-id="428ce-125">**アプリケーションのさまざまな部分をパーティション分割する**: </span><span class="sxs-lookup"><span data-stu-id="428ce-125">**Partition different parts of the application**.</span></span> <span data-ttu-id="428ce-126">データベースがパーティション分割の候補の 1 つであることは明らかですが、ストレージ、キャッシュ、キュー、コンピューティング インスタンスも検討します。</span><span class="sxs-lookup"><span data-stu-id="428ce-126">Databases are one obvious candidate for partitioning, but also consider storage, cache, queues, and compute instances.</span></span>

<span data-ttu-id="428ce-127">**ホット スポットを回避するようにパーティション キーを設計する**: </span><span class="sxs-lookup"><span data-stu-id="428ce-127">**Design the partition key to avoid hot spots**.</span></span> <span data-ttu-id="428ce-128">データベースをパーティション分割しても、引き続き 1 つのシャードで要求の大半を取得している場合、、問題は解決していません。</span><span class="sxs-lookup"><span data-stu-id="428ce-128">If you partition a database, but one shard still gets the majority of the requests, then you haven't solved your problem.</span></span> <span data-ttu-id="428ce-129">すべてのパーティションに負荷を均等に分散させるのが理想的です。</span><span class="sxs-lookup"><span data-stu-id="428ce-129">Ideally, load gets distributed evenly across all the partitions.</span></span> <span data-ttu-id="428ce-130">たとえば、一部の文字が頻出するため、顧客名の頭文字ではなく顧客 ID でハッシュします。</span><span class="sxs-lookup"><span data-stu-id="428ce-130">For example, hash by customer ID and not the first letter of the customer name, because some letters are more frequent.</span></span> <span data-ttu-id="428ce-131">メッセージ キューをパーティション分割するときにも同じ原則が適用されます。</span><span class="sxs-lookup"><span data-stu-id="428ce-131">The same principle applies when partitioning a message queue.</span></span> <span data-ttu-id="428ce-132">キューのセット全体にメッセージを均等に分散させるパーティション キーを選択します。</span><span class="sxs-lookup"><span data-stu-id="428ce-132">Pick a partition key that leads to an even distribution of messages across the set of queues.</span></span> <span data-ttu-id="428ce-133">詳細については、[シャーディング][sharding]に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="428ce-133">For more information, see [Sharding][sharding].</span></span>

<span data-ttu-id="428ce-134">**パーティション分割によって Azure サブスクリプションとサービスの制限を回避する**: </span><span class="sxs-lookup"><span data-stu-id="428ce-134">**Partition around Azure subscription and service limits**.</span></span> <span data-ttu-id="428ce-135">個々 のコンポーネントとサービスには制限がありますが、サブスクリプションとリソース グループにも制限があります。</span><span class="sxs-lookup"><span data-stu-id="428ce-135">Individual components and services have limits, but there are also limits for subscriptions and resource groups.</span></span> <span data-ttu-id="428ce-136">非常に大規模なアプリケーションでは、これらの制限を回避するためにパーティション分割することが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="428ce-136">For very large applications, you might need to partition around those limits.</span></span>

<span data-ttu-id="428ce-137">**さまざまなレベルでパーティション分割する**: </span><span class="sxs-lookup"><span data-stu-id="428ce-137">**Partition at different levels**.</span></span> <span data-ttu-id="428ce-138">VM にデプロイされたデータベース サーバーについて考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="428ce-138">Consider a database server deployed on a VM.</span></span> <span data-ttu-id="428ce-139">VM には、Azure Storage によってサポートされる VHD があります。</span><span class="sxs-lookup"><span data-stu-id="428ce-139">The VM has a VHD that is backed by Azure Storage.</span></span> <span data-ttu-id="428ce-140">ストレージ アカウントは Azure サブスクリプションに属しています。</span><span class="sxs-lookup"><span data-stu-id="428ce-140">The storage account belongs to an Azure subscription.</span></span> <span data-ttu-id="428ce-141">階層の段階ごとに制限があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="428ce-141">Notice that each step in the hierarchy has limits.</span></span> <span data-ttu-id="428ce-142">データベース サーバーには、接続プールの制限がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="428ce-142">The database server may have a connection pool limit.</span></span> <span data-ttu-id="428ce-143">VM には CPU とネットワークの制限があります。</span><span class="sxs-lookup"><span data-stu-id="428ce-143">VMs have CPU and network limits.</span></span> <span data-ttu-id="428ce-144">ストレージには IOPS の制限があります。</span><span class="sxs-lookup"><span data-stu-id="428ce-144">Storage has IOPS limits.</span></span> <span data-ttu-id="428ce-145">サブスクリプションには、VM のコア数の制限があります。</span><span class="sxs-lookup"><span data-stu-id="428ce-145">The subscription has limits on the number of VM cores.</span></span> <span data-ttu-id="428ce-146">一般に、階層の下位でパーティション分割する方が簡単です。</span><span class="sxs-lookup"><span data-stu-id="428ce-146">Generally, it's easier to partition lower in the hierarchy.</span></span> <span data-ttu-id="428ce-147">サブスクリプション レベルでパーティション分割する必要があるのは、大規模なアプリケーションだけです。</span><span class="sxs-lookup"><span data-stu-id="428ce-147">Only large applications should need to partition at the subscription level.</span></span>

<!-- links -->

[azure-limits]: /azure/azure-subscription-service-limits
[data-partitioning-guidance]: ../../best-practices/data-partitioning.md
[sharding]: ../../patterns/sharding.md
