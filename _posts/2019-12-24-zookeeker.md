# what is zookeeper
zookeeper提供可靠的分布式协调服务，其本身也是分布式的，来避免单点失败。可以利用zookeeper提供的协调功能来实现诸如选择Master server， 管理配置信息。

# zookeeper mission
现在的大部分系统都是分布式的， 需要依靠各个组件的相互合作来实现功能。zookeeper的目标就是提供一个易于使用的模型来帮助开发人员来解决组件之间的协调问题， 让开发人员的精力更多的关注在业务逻辑。

# zookeeper basics

## zookeeper模型

zookeeper受文件系统启发，其模型类似于文件系统。

/
|-----/Master
|-----/Workers
         |-----worker1
         |-----worker2

每个节点称之为znode，每个节点都可以关联数据也可以拥有子节点。 有四种类型的节点类型
1. persistent
2. ephemeral
3. persist sequential
4. ephemeral sequential

ephemeral node在下面两种情况下会被删掉
1. session 过期或者session主动关闭
2. 其他client发送delete指令删除

## API
1. create /path data
2. delete /path
3. exists /path
4. set /path data
5. getData /path 
6. getChildren /path

zookeeper 对znode数据的读写是原子性的，或者成功或者失败。
## watch and notification
为了能够感知到znode的变化， zookeeper支持在znode上设置watch来接收数据变化的通知。watch是一次性的，一旦watch被触发， watch就会从znode上移除。如果要继续监听，需要重新注册。

## version
每一个znode都有个version与之相关联，每次znode数据变化，version也会随着改变。zookeeper利用这个特性来解决多个client并发更新同一个znode的问题。

## zookeeper架构