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



