##SQL反模式读书笔记：AS

P16

    SELECT c.product_id, COUNT(*) AS products_per_account
    FROM Contacts
    GROUP BY account_id
    
其中Contacts表是Products表和Accounts表的中间表，这个SQL查询语句的作用是查询每个账号相关的产品数量。

    SELECT c.product_id, c.accounts_per_product
    FROM (
        SELECT product_id, COUNT(*) AS accounts_per_product
        FROM Contacts
        GROUP BY product_id
    ) AS c
    HAVING c.accounts_per_product = MAX(c.accounts_per_product)
    
这个SQL查询语句的作用是查询相关账号最多的产品。在这两个查询语句中我注意到的是accounts_per_product和products_per_account这两个本来不存在的字段。很明显这两个是通过AS得到的字段。AS也就是Alias(别名)，通过Alias可以方便组织多表查询特别是在涉及到自身对应自身表的时候，比如评论表如果想要父级和子级的结果查询，同时也可以用Alias给表的字段起一个别名，便于输出，比如上面的两个SQL查询。

第一个SQL查询语句中，通过将c.product_id，COUNT(*)这个要查询的字段alias成products_per_account，这样输出的结果类似于：

    products_per_account
    7

就很容易阅读了。

第二个SQL查询语句中

    SELECT product_id, COUNT(*) AS accounts_per_product
    FROM Contacts
    GROUP BY product_id
    
这样一段查询得到结果集合被alias成了c，此外其中也将根据product_id查询得到的products数量alias成了accounts_per_product，所以c这个集合中也多了一个字段accounts_per_product，通过这样的处理，想要得到关联账号最多的产品的product_id就简单得好像以下：

    SELECT product_id, accounts_per_product
    FROM c 
    HAVING accounts_per_product = MAX(accounts_per_product)
    
这个查询语句通过AS，写得相当优雅，易懂。