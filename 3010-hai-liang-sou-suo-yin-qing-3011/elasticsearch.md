1.query是怎么分发到每个节点的？

2.Elasticsearch使用上的优化有哪些？

3.怎么避免脑裂？

4.query和filter的区别？

5.Elasticsearch的缺点有哪些？你觉得可以在哪些地方进行改进？

6.Lucene加快查询的机制有哪些？

7.如何使用Lucene构建分布式索引？

8.说一下master节点选举算法

9.Elasticsearch出现OOM的场景遇到过吗？

10.说一下副本的作用。

11.每天2个TB的数据怎么规划集群？

12.比较一下向量空间模型和概率检索模型的优缺点。

13.DocValues的作用。

14.一个索引下多个type可能会导致什么样的问题？

15.Elasticsearch 与 Solr 的比较总结 参考：\(https://www.cnblogs.com/charlesblc/p/6233938.html\)

* 二者安装都很简单；
* Solr 利用 Zookeeper 进行分布式管理，而 Elasticsearch 自身带有分布式协调管理功能;
* Solr 支持更多格式的数据，而 Elasticsearch 仅支持json文件格式；
* Solr 官方提供的功能更多，而 Elasticsearch 本身更注重于核心功能，高级功能多有第三方插件提供；
* Solr 在传统的搜索应用中表现好于 Elasticsearch，但在处理实时搜索应用时效率明显低于 Elasticsearch。
* Solr 是传统搜索应用的有力解决方案，但 Elasticsearch 更适用于新兴的实时搜索应用。



