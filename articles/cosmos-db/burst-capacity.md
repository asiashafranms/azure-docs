---
title: Burst capacity in Azure Cosmos DB (preview)
description: Learn more about burst capacity in Azure Cosmos DB
author: seesharprun
ms.author: sidandrews
ms.service: cosmos-db
ms.custom: event-tier1-build-2022, ignite-2022
ms.topic: conceptual
ms.reviewer: dech
ms.date: 10/26/2022
---

# Burst capacity in Azure Cosmos DB (preview)

[!INCLUDE[NoSQL, MongoDB, Cassandra, Gremlin, Table](includes/appliesto-nosql-mongodb-cassandra-gremlin-table.md)]

Azure Cosmos DB burst capacity (preview) allows you to take advantage of your database or container's idle throughput capacity to handle spikes of traffic. With burst capacity, each physical partition can accumulate up to 5 minutes of idle capacity, which can be consumed at a rate up to 3000 RU/s. With burst capacity, requests that would have otherwise been rate limited can now be served with burst capacity while it's available.

Burst capacity applies only to Azure Cosmos DB accounts using provisioned throughput (manual and autoscale) and doesn't apply to serverless containers. The feature is configured at the Azure Cosmos DB account level and will automatically apply to all databases and containers in the account that have physical partitions with less than 3000 RU/s of provisioned throughput. Resources that have greater than or equal to 3000 RU/s per physical partition won't benefit from or be able to use burst capacity.

## How burst capacity works

> [!NOTE]
> The current implementation of burst capacity is subject to change in the future. Usage of burst capacity is subject to system resource availability and is not guaranteed. Azure Cosmos DB may also use burst capacity for background maintenance tasks. If your workload requires consistent throughput beyond what you have provisioned, it's recommended to provision your RU/s accordingly without relying on burst capacity. Before enabling burst capacity, it is also recommended to evaluate if your partition layout can be [merged](merge.md) to permanently give more RU/s per physical partition without relying on burst capacity.

Let's take an example of a physical partition that has 100 RU/s of provisioned throughput and is idle for 5 minutes. With burst capacity, it can accumulate a maximum of 100 RU/s * 300 seconds = 30,000 RU of burst capacity. The capacity can be consumed at a maximum rate of 3000 RU/s, so if there's a sudden spike in request volume, the partition can burst up to 3000 RU/s for up 30,000 RU / 3000 RU/s = 10 seconds. Without burst capacity, any requests that are consumed beyond the provisioned 100 RU/s would have been rate limited (429).

After the 10 seconds is over, the burst capacity has been used up. If the workload continues to exceed the provisioned 100 RU/s, any requests that are consumed beyond the provisioned 100 RU/s would now be rate limited (429). The maximum amount of burst capacity a physical partition can accumulate at any point in time is equal to 300 seconds * the provisioned RU/s of the physical partition.

## Getting started

To get started using burst capacity, navigate to the **Features** page in your Azure Cosmos DB account. Select and enable the **Burst Capacity (preview)** feature.

Before enabling the feature, verify that your Azure Cosmos DB account(s) meet all the [preview eligibility criteria](#limitations-preview-eligibility-criteria). Once you've enabled the feature, it will take 15-20 minutes to take effect.

:::image type="content" source="media/burst-capacity/burst-capacity-enable-feature.png" alt-text="Screenshot of Burst Capacity feature in Preview Features page in Subscriptions overview in Azure portal.":::

## Limitations (preview eligibility criteria)

To enroll in the preview, your Azure Cosmos DB account must meet all the following criteria:

- Your Azure Cosmos DB account is using provisioned throughput (manual or autoscale). Burst capacity doesn't apply to serverless accounts.
- Your Azure Cosmos DB account is using API for NoSQL, Cassandra, Gremlin, MongoDB, or Table.

## Next steps

- See the FAQ on [burst capacity.](burst-capacity-faq.yml)
- Learn more about [provisioned throughput.](set-throughput.md)
- Learn more about [request units.](request-units.md)
- Trying to decide between provisioned throughput and serverless? See [choose between provisioned throughput and serverless.](throughput-serverless.md)
- Want to learn the best practices? See [best practices for scaling provisioned throughput.](scaling-provisioned-throughput-best-practices.md)
