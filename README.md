# pg_lightool

#### 项目介绍
作者将自己diy的pg轻量级周边工具，融合到本项目中，目前有：

1. blockrecover：依托wal日志完成闭库下的坏块修复

>当前可选的pg恢复工具（PITR、pg_rman）都需要一个基础备份。备份原理就是以基础备份为base，通过重演wal日志使数据库达到一个用户满意
>的状态。如果没有基础备份，那么数据库一旦有页损坏，那么这个页的数据会恢复困难。blockrecover工具不需要一个基础备份，只需要扫描wal日志就可
>以完成坏块的恢复（在给出的wal日志中至少有一个对于修复页的full page write）。
>
>本次更新：blockrecover工具可以依托pg_basebackup生成的基础备份完成坏块修复（添加-r参数）

2. ~~walshow：逐行显示wal日志的信息（正在开发中）~~
>~~wal日志是pg重要的日志。数据库DBA难以通过工具查询出wal记录的内容。~~
1. ~~logical decoding工具只能在线解析wal日志而且需要预先配置~~
2. ~~pg_xlogdump工具解析结果难以阅读~~
3. ~~xlogminer工具操作复杂（https://github.com/HighgoSoftware/XLogMiner）~~
>~~现在考虑依托当前的系统表文件，对每一条wal日志进行解析~~

3. 数据分布查看工具 
>数据库在经过长时间的运行之后，有效数据不规则的分散在数据文件的各个地方。如何能得知每个page，每个segment到底存储了多少数据呢？
>作者DIY了这个功能'datadis'到pg_lightool工具中。

#### 安装教程

1. 配置postgres安装bin目录的PATH环境变量
2. make
3. make install

#### 使用说明

```
pg_lightool is a light tool of postgres

Usage:
  pg_lightool OPTION blockrecover
  pg_lightool OPTION walshow
  pg_lightool OPTION datadis
  pg_lightool OPTION pageinspect

Common Options:
  -V, --version                         output version information, then exit

For blockrecover:
  -l, --log                             whether to write a debug info
  -f, --relnode=spcid/dbid/relfilenode  specify files to repair
  -b, --block=n1[,n2,n3]                specify blocks to repair(10 limit)
					-1 for whole table recover
  -w, --walpath=walpath                 wallog read from
  -D, --pgdata=datapath                 data dir of database
  -i, --immediate			            does not do a backup for old file
  -r, --backuppath			            support pg_rman $BACKUP_PATH

For datadis:
  -f, --relnode=spcid/dbid/relfilenode  specify files to dis
  -D, --pgdata=datapath                 data dir of database
  -p, --place=outPtah                   place where store the result
  -g, --grade=level                     1 to show file level(default);
                                        2 to show page level;
                                        3 to show all;
  -d, --detail		                    wether to show detail info
  -s, --small		                    show which ratio small than it

For pageinspect:
  -f, --relnode=spcid/dbid/relfilenode specify files to inspect
  -D, --pgdata=datapath                 data dir of database
  -p, --place=outPtah                   place where store the result
  -b, --block=blkno                     specify blocks to inspect

```



#### 使用限制
blockrecover
1. 当前只能解析同一个时间线的xlog
2. 当前只能完成table数据的块恢复
3. 此为个人兴趣项目，没有经过完整的测试，入参请谨慎。
4. 项目是在pg10.4上做的开发，没有对其他环境做测试

#### BUG提交
如有bug欢迎提交。
联系我lchch1990@sina.cn
