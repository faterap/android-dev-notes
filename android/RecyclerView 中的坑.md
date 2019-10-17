# RecyclerView 中碰过的坑

1. ViewHolder views must not be attached when created. Ensure that you are not passing ‘true’ to the attachToRoot parameter of LayoutInflater.inflate(…, boolean attachToRoot) 

解决：

虽然网上有众多和这个 exception 相关的文章，但是和我们遇到的情况都不一样。这个问题源自 onCreateViewHolder 在创建 holder 的时候将一个已经 attach 到 recyclerview 中的 view 又传给了新 new 出来的 holder 导致了这个问题。

解决方法有两种：

- 通过 layoutmanager 将 view remove 后交给新的 holder 
- new 一个新的 view 交给 holder





[]: https://lber19535.github.io/2019/02/28/2019-2019-2-bug汇总/

