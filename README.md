# Cubanlytics

This repository is public for vistors to read about cubanlytics motivation, design, and current development.

## **Behind the name:**

**Cubanlytics**: cuban links + analytics = Cubanlytics

**Cuban-link**: a variation of the standard cable link chain and features oval links that intersect in a rope pattern.

**Analytics**: is the systematic computational analysis of data or statistics.

## Motivation

Late June 2021 I was introduced to the NFT space and was hooked. It all made sense. Since 2018, most of my work has delt with utlizing ledgering capabilities to keep provenance of data asset changes. In other words trust in data. Therefore, the dots connected and I needed to understand more. The best way to do this is to dive in. I bought a Cool Cat. The project boomed (still is) but I started asking questions that could not be answered easily. For example, when do top holders buy and sell and why, and can I identify users desperate to liquidate etc. etc. I was amazed at the data available and how much insight I had into daily market activity. With that said, there was a lack of advance metrics given the amount of serious cash moving through daily. I searched for products that could help assist in more insightful technical analysis but the best I found was a person on Twitter releasing daily summary excel reports of the market. No code no automation- pain. So I started building out two products to help assit traders and devs with intentions to bring more insight into the NFT marketplace.


## Design

### Ingestion
#### Ingest Layer:
A configured deployment runs hourly and collects only one type of market event (trades, bids, asks). Note there are five services deployed to fetch these different market types. Therefore, nodes are scaled based on activity and minimize failure on other collection processing. A sources configuration contains a contract address, contract name, exchange, and date. Given a new source is added, then all historical data is pulled. Data fetched on the appropriate exchange client is pushed to the transport layer to raw/ storage. Then, the service publishes the recent data location to the transportation layer.

#### Transport Layer:
This layer's design handles data transportation whether it transports locally, a database, cache, or object storage. I recognize all services have different reasons to move data around the system therefore this layer helps move everything along seamlessly.

The transport layer currently supports publishing to rabbitMQ, writing to a given configured storage client, and writing to a given configured storage client in parquet.

#### Processing Layer:
There are five queues the processing service consumes. The queues are created based on configured market types. Consumed data is directed appropriately, processed, converted into parquet, and written into the warehouse's clean storage. Given the data will be written once and accessed a lot it, makes sense to convert all clean data into parquet. From here, pipelines run to process the statistical questions of interest and POST/ to the analytic API.

#### Ingestion Diagram:
![system_architecture](https://user-images.githubusercontent.com/10743653/150014110-cda1ff43-7566-4636-8881-86b0f9416caa.png)

### UI

The UIs built on Vue and NodeJs. The backend to the UI is unplugged right now given a refactor to the API but it does work! :)
The simple page below is for users to get a taste of what's offered. Other tabs are modeled after the offered products- API and Advance UI dashboard.
Currently, the future product tabs allow users to donate with Metamask.

#### Base UI

<img width="1734" alt="screen_basic" src="https://user-images.githubusercontent.com/10743653/150013917-d4e14b99-8f9a-45ff-a71d-66c73510e6db.png">

#### Example Product Tab

<img width="1737" alt="screen_api" src="https://user-images.githubusercontent.com/10743653/150013982-63f0600b-181c-4e53-8f27-cd37af180a6e.png">

### API
The api's endpoints are currently under construction but it's written in flask and is what feeds the analytic database.

#### Example endpoints:

/api/v2/contract-address/transactions/

/api/v2/contract-address/stats/summary/

/api/v2/contract-address/holders/stats/summary/

/api/v2/contract-address/holders/stats/?top=5

/api/v2/contract-address/holders/{id}/stats/summary/

/api/v2/contract-address/holders/{id}/stats/?likely_to_liquidate=True


### Final Notes:

I do recognize the API/UI is simple at the moment but it's important for me to have those pieces running as base version before scaling the design.
With that said, the most important part of this process I beileve is first building out a robust enough scalable data ingestion processs. The rest is straightforward given data ingest is in place.







