# oracleQA

## oracle数据库打补丁
1. 需要注意的问题
  1) 应该先打完补丁之后再进行创建实例操作，原因：大部分的oracle漏洞扫描工具进行深度扫描的时候需要登录到一个实例中进行扫描，应该有将打完的补丁应用在实例中，但是我们没有实际操作，只是将现有的实例重新在打完补丁之后创建了一份。
  2）需要找到对应的补丁，这个应该是找专业人士

## 常见操作问题
1. 在导入/导出对应用户的时候出现以下问题，可能是由于导出语句中的directory=dir_dp放在后面的原因
```
  ORA-39002: 操作无效
  ORA-39070: 无法打开日志文件。
  ORA-39087: 目录名 DATA_PUMP_DIR; 无效
```
正确语句应该是
> expdp test/test@127.0.0.1:1521/test schemas=test directory=dir_dp dumpfile=test.dmp logfile=test.log;

2. 在长时间使用oracle之后，可能会出现oracle连接非常缓慢，还有可能造成数据库监听失败的原因，但是重启了之后又可以连接上，过一会就又不能正常使用了，此时应该检查是否是日志过多(最大4G)
  1）如果不需要记录日志，可以直接通过设置将oracle日志关闭
  2）如果需要，则将日志文件删除即可

3. oracle数据库在做rac集群的时候，在创建表空间的时候不能按照单节点创建，必须要用特有的语句创建

4. oracle数据库用户被锁定
> 登录system as sysdba之后，alter user system account unlock;即可解锁
> 用户解锁之后可以通过下列语句进行修改密码：alter user system identified by +新的密码;即可重置密码

5. oracle数据库system默认密码是manager，sys密码:change_on_install，scott密码:tiger

6. oracle修改字符集问题
> 通常我们在创建完oracle后，可能在安装过程中忘记设置字符集，导致后续程序中出现了汉字为？的情况，解决方法如下
```
1. 我们首先需要确定是否数据库里面数据也为？等数据
2. 如果不是，直接找程序上的问题；如果是，则需要排查是否oracle服务端的编码集是否正确
3. 如果不正确，则需要下方的解决方案进行解决
```
字符集修改步骤：
1. 确定数据库没有活动的连接。
```
1. 关闭数据库
SQL> shutdown immediate;
2. 以挂接方式启动数据库
SQL> startup mount;
```
2. 将数据库置于restricted mode下，并打开数据库。
```
SQL> alter system enable restricted session;
SQL> alter database open;
```
3. 直接通过INTERNAL_USE跳过超集的检查更改字符集。
```
SQL> ALTER DATABASE character set INTERNAL_USE ZHS16GBK; 
```
4. 关闭数据库restricted mode
```
SQL> alter system disable restricted session;
```
5. 修改完成，查看数据库字符集即可
```
SQL> select userenv('language') from dual;
```
