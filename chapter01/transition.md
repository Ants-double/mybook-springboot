
# 事务

隔离级别
隔离级别	意义	其它
ISOLATION_DEFAULT	使用后端数据库的默认隔离级别	
ISOLATION_READ_UNCOMMITTED	读未提交，允许读取未提交的数据，可能导致 脏读 不可重复读 幻读	
ISOLATION_READ_CMMMITTED	读已提交，允许一个事务读取另一个事务已经提交的数据，可以避免脏读，但无法避免不可重复读和幻读	
ISOLATION_REPEATABLE_READ	可重复读，一个事务不可能更新另一个事务修改，但尚未提交（回滚）的数据，可以避免脏读和不可重复讯，但是无法避免幻读	
ISOLATION_SERIALIZABLE	序列化，所有事务都在一个执行队列中，依次执行，可以避免脏读 不可重复读 幻读，但是效率低，非必须不使用	
