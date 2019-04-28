# 笔记
每次读分析redis的源码。都会写一点注释在代码里。这里回来总结
## ae.c
事件循环主函数。linux下使用epoll lt模式
## networking.c
处理客户端读写事件。
相关命令格式
- 命令类型：PROTO_REQ_MULTIBULK PROTO_REQ_INLINE。
  如果命令的以 `*`开头，则是PROTO_REQ_MULTIBULK命令。否则是PROTO_REQ_INLINE命令
- PROTO_REQ_MULTIBULK 命令格式：
 `*` + `num1` num1代表参数个数。每一个都是下面的格式
  `\r\n` + `$` + `num2` + `\r\n` + `COMMAND` + `\r\n` num2代表下一个参数的字符数。如command就是7
  
 协议：
- 客户端tcp3次握手连上来。首先会发送一个`COMMAND`命令，根据上面的格式，数据是`*1\r\n$7\r\nCOMMAND\r\n`,
共17个字符
