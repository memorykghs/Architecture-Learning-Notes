# 2 - Overview of Aggregates

## 本章大綱
**Overview of Aggregates**
* So what is Aggreate?
    * Rule #1 Reference other aggregate roots via identity ( primary key )
    * Rule #2 Transaction = processing **one** command by one aggregate
* Aggregate Granularity 集成粒度

## 終於進入正題!!!
> 🤪**Domain Driven Design** read it!!!

超多東西都跟 DDD 有關的!!!

![](/images/microservices/2-1.png)

☝一直被孤立而且遺忘的那個人：Aggregates

其他的部分其實我們多少在設計系統的時候會用到，但我們總是會遺忘 Aggregates🤣

## So what is Aggreate?
* Aggregate is a cluster of objects that can be treated as a unit
* Graph consisting of a **root entity** and one or more other entities and value objects
* Typically business entities are Aggregates, e.g. Customer, Account, Order, Product...

![](/images/microservices/2-2.png)

#### Rule #1 Reference other aggregate roots via identity ( primary key )

那你可能會想說 What!? 那我的 Foreign key 呢??? 當你回頭去看時，你會發現這個設計將會打破一些傳統的 OO Design ( Object-oriented Design )，因為我們在設計時不會出現 Foreign key。

好處：

**Easily partition into microservices.**
每個 entity 之間的關聯就只有 primary key，彼此比較鬆散也比較好做切割。

![](/images/microservices/2-3.png)

#### Rule #2 Transaction = processing **one** command by one aggregate

* Transaction scope = service
    
    Transaction should only create or update one aggregate at the same time.
    
* Transaction scope = NoSQL database "transaction"
    
    另外一個這樣做的優點是，這樣劃分的單一 scope 就符合典型的 NoSQL database 的能力範疇，因為 NoSQL database 一次只能依照給定的 `key` 更新單一一個 row 或 value

## Aggregate Granularity 集成粒度

If an update must be atomic then it must be handled by a single aggregate,
**THEREFORE**
***__Aggregate granularity is important!__***

* choose aggregate boundaries

![](/images/microservices/2-4.png)