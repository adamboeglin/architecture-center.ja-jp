---
title: データ レイク
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.openlocfilehash: d433867886ce0afc219fcc9f35eb7f8b3ce6bee1
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58244093"
---
# <a name="data-lakes"></a><span data-ttu-id="e47b8-102">データ レイク</span><span class="sxs-lookup"><span data-stu-id="e47b8-102">Data lakes</span></span>

<span data-ttu-id="e47b8-103">データ レイクは、大量のデータを未加工のネイティブ形式で保持するストレージ リポジトリです。</span><span class="sxs-lookup"><span data-stu-id="e47b8-103">A data lake is a storage repository that holds a large amount of data in its native, raw format.</span></span> <span data-ttu-id="e47b8-104">データ レイク ストアは、テラバイト級およびペタバイト級のデータにスケーリングできるように最適化されています。</span><span class="sxs-lookup"><span data-stu-id="e47b8-104">Data lake stores are optimized for scaling to terabytes and petabytes of data.</span></span> <span data-ttu-id="e47b8-105">通常、データは、構造化データ、半構造化データ、または非構造化データを含む複数の異種ソースから取得されます。</span><span class="sxs-lookup"><span data-stu-id="e47b8-105">The data typically comes from multiple heterogeneous sources, and may be structured, semi-structured, or unstructured.</span></span> <span data-ttu-id="e47b8-106">データ レイクの着想は、それらすべてを元の未変換状態で格納するという点にあります。</span><span class="sxs-lookup"><span data-stu-id="e47b8-106">The idea with a data lake is to store everything in its original, untransformed state.</span></span> <span data-ttu-id="e47b8-107">このアプローチは、データを取り込んだ時点で変換と処理を行う、従来の[データ ウェアハウス](../relational-data/data-warehousing.md)とは異なります。</span><span class="sxs-lookup"><span data-stu-id="e47b8-107">This approach differs from a traditional [data warehouse](../relational-data/data-warehousing.md), which transforms and processes the data at the time of ingestion.</span></span>

<span data-ttu-id="e47b8-108">データ レイクの利点は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="e47b8-108">Advantages of a data lake:</span></span>

- <span data-ttu-id="e47b8-109">データは未加工の形式で格納されるため、データが破棄されることはありません。</span><span class="sxs-lookup"><span data-stu-id="e47b8-109">Data is never thrown away, because the data is stored in its raw format.</span></span> <span data-ttu-id="e47b8-110">これは特にビッグ データ環境で有用です。ビッグ データ環境では、データからどのような洞察が得られるか、前もってわからないからです。</span><span class="sxs-lookup"><span data-stu-id="e47b8-110">This is especially useful in a big data environment, when you may not know in advance what insights are available from the data.</span></span>
- <span data-ttu-id="e47b8-111">ユーザーは、データを探索したり、独自のクエリを作成したりできます。</span><span class="sxs-lookup"><span data-stu-id="e47b8-111">Users can explore the data and create their own queries.</span></span>
- <span data-ttu-id="e47b8-112">従来の ETL ツールよりも高速に動作する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e47b8-112">May be faster than traditional ETL tools.</span></span>
- <span data-ttu-id="e47b8-113">非構造化および半構造化データを格納できるため、データ ウェアハウスよりも柔軟です。</span><span class="sxs-lookup"><span data-stu-id="e47b8-113">More flexible than a data warehouse, because it can store unstructured and semi-structured data.</span></span>

<span data-ttu-id="e47b8-114">完全なデータ レイク ソリューションは、ストレージと処理の両方で構成されます。</span><span class="sxs-lookup"><span data-stu-id="e47b8-114">A complete data lake solution consists of both storage and processing.</span></span> <span data-ttu-id="e47b8-115">データ レイク ストレージは、フォールト トレランス、無限のスケーラビリティ、さまざまな形状とサイズのデータ​​の高スループットの取り込みなどを目的としています。</span><span class="sxs-lookup"><span data-stu-id="e47b8-115">Data lake storage is designed for fault-tolerance, infinite scalability, and high-throughput ingestion of data with varying shapes and sizes.</span></span> <span data-ttu-id="e47b8-116">Data Lake 処理には、これらの目標を念頭に置いて構築された 1 つ以上の処理エンジンが関係し、Data Lake に格納された大規模なデータを処理することができます。</span><span class="sxs-lookup"><span data-stu-id="e47b8-116">Data lake processing involves one or more processing engines built with these goals in mind, and can operate on data stored in a data lake at scale.</span></span>

## <a name="when-to-use-a-data-lake"></a><span data-ttu-id="e47b8-117">データ レイクが適したシナリオ</span><span class="sxs-lookup"><span data-stu-id="e47b8-117">When to use a data lake</span></span>

<span data-ttu-id="e47b8-118">データ レイクの一般的な用途には、[データ探索](./interactive-data-exploration.md)、データ分析、機械学習などがあります。</span><span class="sxs-lookup"><span data-stu-id="e47b8-118">Typical uses for a data lake include [data exploration](./interactive-data-exploration.md), data analytics, and machine learning.</span></span>

<span data-ttu-id="e47b8-119">データ レイクは、データ ウェアハウスのデータ ソースとして機能させることもできます。</span><span class="sxs-lookup"><span data-stu-id="e47b8-119">A data lake can also act as the data source for a data warehouse.</span></span> <span data-ttu-id="e47b8-120">このアプローチでは、生データをデータ レイクに取り込み、次にクエリ可能な構造化された形式に変換します。</span><span class="sxs-lookup"><span data-stu-id="e47b8-120">With this approach, the raw data is ingested into the data lake and then transformed into a structured queryable format.</span></span> <span data-ttu-id="e47b8-121">通常、この変換において [ELT](../relational-data/etl.md#extract-load-and-transform-elt) (抽出 - 読み込み - 変換) パイプラインが使用されます。このパイプラインにはデータが取り込まれ、適宜変換されます。</span><span class="sxs-lookup"><span data-stu-id="e47b8-121">Typically this transformation uses an [ELT](../relational-data/etl.md#extract-load-and-transform-elt) (extract-load-transform) pipeline, where the data is ingested and transformed in place.</span></span> <span data-ttu-id="e47b8-122">既にリレーショナルになっているソース データは、ETL プロセスを使用して、データ レイクをスキップしてデータ ウェアハウスに直接取り込まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="e47b8-122">Source data that is already relational may go directly into the data warehouse, using an ETL process, skipping the data lake.</span></span>

<span data-ttu-id="e47b8-123">Data Lake ストアは、イベント ストリーミングや IoT のシナリオでよく使用されます。変換やスキーマ定義を使用せずに、大量のリレーショナル データと非リレーショナル データを永続化できるためです。</span><span class="sxs-lookup"><span data-stu-id="e47b8-123">Data lake stores are often used in event streaming or IoT scenarios, because they can persist large amounts of relational and nonrelational data without transformation or schema definition.</span></span> <span data-ttu-id="e47b8-124">短い待機時間で小規模な書き込みを大量に処理するために構築されており、大量のスループットに合わせて最適化されています。</span><span class="sxs-lookup"><span data-stu-id="e47b8-124">They are built to handle high volumes of small writes at low latency, and are optimized for massive throughput.</span></span>

## <a name="challenges"></a><span data-ttu-id="e47b8-125">課題</span><span class="sxs-lookup"><span data-stu-id="e47b8-125">Challenges</span></span>

- <span data-ttu-id="e47b8-126">スキーマまたは記述メタデータがない場合、データの使用やクエリが困難になることがあります。</span><span class="sxs-lookup"><span data-stu-id="e47b8-126">Lack of a schema or descriptive metadata can make the data hard to consume or query.</span></span>
- <span data-ttu-id="e47b8-127">データ全体に意味論的な一貫性が欠けている場合、ユーザーにデータ分析の高度なスキルがない限り、データ分析の実行が困難になることがあります。</span><span class="sxs-lookup"><span data-stu-id="e47b8-127">Lack of semantic consistency across the data can make it challenging to perform analysis on the data, unless users are highly skilled at data analytics.</span></span>
- <span data-ttu-id="e47b8-128">データ レイクに取り込まれるデータの質を保証することが困難な場合があります。</span><span class="sxs-lookup"><span data-stu-id="e47b8-128">It can be hard to guarantee the quality of the data going into the data lake.</span></span>
- <span data-ttu-id="e47b8-129">適切に管理しないと、アクセス制御およびプライバシーに関する問題が発生することがあります。</span><span class="sxs-lookup"><span data-stu-id="e47b8-129">Without proper governance, access control and privacy issues can be problems.</span></span> <span data-ttu-id="e47b8-130">どの情報をデータ レイクに取り込むか、どのユーザーがそのデータにアクセスできるか、また、どの目的で使用するのかを検討する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e47b8-130">What information is going into the data lake, who can access that data, and for what uses?</span></span>
- <span data-ttu-id="e47b8-131">既にリレーショナルになっているデータを統合する方法としては、データ レイクは最善とは言えない場合があります。</span><span class="sxs-lookup"><span data-stu-id="e47b8-131">A data lake may not be the best way to integrate data that is already relational.</span></span>
- <span data-ttu-id="e47b8-132">データ レイクだけで、組織全体に渡る包括的で統合された視野が提供されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="e47b8-132">By itself, a data lake does not provide integrated or holistic views across the organization.</span></span>
- <span data-ttu-id="e47b8-133">データ レイクは、洞察を得るために実際に分析やマイニングが行われることのないデータの廃棄場になってしまう可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e47b8-133">A data lake may become a dumping ground for data that is never actually analyzed or mined for insights.</span></span>

## <a name="relevant-azure-services"></a><span data-ttu-id="e47b8-134">関連 Azure サービス</span><span class="sxs-lookup"><span data-stu-id="e47b8-134">Relevant Azure services</span></span>

- <span data-ttu-id="e47b8-135">[Data Lake Store](/azure/data-lake-store/) は、Hadoop 互換のハイパースケール リポジトリです。</span><span class="sxs-lookup"><span data-stu-id="e47b8-135">[Data Lake Store](/azure/data-lake-store/) is a hyper-scale, Hadoop-compatible repository.</span></span>
- <span data-ttu-id="e47b8-136">[Data Lake Analytics](/azure/data-lake-analytics/) は、ビッグ データ分析を簡略化するオンデマンド分析ジョブ サービスです。</span><span class="sxs-lookup"><span data-stu-id="e47b8-136">[Data Lake Analytics](/azure/data-lake-analytics/) is an on-demand analytics job service to simplify big data analytics.</span></span>
