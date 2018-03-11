名词解释
　　cron--服务名；crond--linux下用来周期性的执行某种任务或等待处理某些事件的一个守护进程，与windows下的计划任务类似；crontab--是定制好的计划任务表

软件包安装
　　sudo apt install cron 



查看crond服务是否运行
　　pgrep crond 或 /sbin/service crond status 或 ps -elf|grep crond|grep -v "grep"


crond服务操作命令
　　/sbin/service crond start //启动服务  
　　/sbin/service crond stop //关闭服务  
　　/sbin/service crond restart //重启服务  
　　/sbin/service crond reload //重新载入配置
service 有些可能没有其实它只是一个脚本文件(vim/sbin/ 或者什么编辑文件都行（最好进入root） service)
然后复制最下面的到里面就ok了 保存

配置定时任务
　　cron有两个配置文件，一个是一个全局配置文件（/etc/crontab），是针对系统任务的；一组是crontab命令生成的配置文件（/var/spool/cron下的文件），是针对某个用户的.定时任务配置到任意一个中都可以。

　　查看全局配置文件配置情况: cat /etc/crontab

　　---------------------------------------------
　　SHELL=/bin/bash
　　PATH=/sbin:/bin:/usr/sbin:/usr/bin
　　MAILTO=root
　　HOME=/

　　# run-parts
　　01 * * * * root run-parts /etc/cron.hourly
　　02 4 * * * root run-parts /etc/cron.daily
　　22 4 * * 0 root run-parts /etc/cron.weekly
　　42 4 1 * * root run-parts /etc/cron.monthly
　　----------------------------------------------

　　查看用户下的定时任务:crontab -l或cat /var/spool/cron/用户名

crontab任务配置基本格式
　　

 

　　*   *　 *　 *　 *　　command(换行)
　　分钟(0-59)　小时(0-23)　日期(1-31)　月份(1-12)　星期(0-6,0代表星期天)　 命令
　　第1列表示分钟1～59 每分钟用*或者 */1表示
　　第2列表示小时1～23（0表示0点）
　　第3列表示日期1～31
　　第4列表示月份1～12
　　第5列标识号星期0～6（0表示星期天）
　　第6列要运行的命令

　　在以上任何值中，星号（*）可以用来代表所有有效的值。譬如，月份值中的星号意味着在满足其它制约条件后每月都执行该命令。
　　整数间的短线（-）指定一个整数范围。譬如，1-4 意味着整数 1、2、3、4。
　　用逗号（,）隔开的一系列值指定一个列表。譬如，3, 4, 6, 8 标明这四个指定的整数。
　　正斜线（/）可以用来指定间隔频率。在范围后加上 /<integer> 意味着在范围内可以跳过 integer。譬如，0-59/2 可以用来在分钟字段定义每两分钟。间隔频率值还可以和星号一起使用。例如，*/3 的值可以用在月份字段中表示每三个月运行一次任务。
　　开头为井号（#）的行是注释，不会被处理
	注意conmand 最好用绝对路径,运行python 程序的话,要设置环境变量
如:* * * * * export PATH=包路劲:$PATH;cd 文件路径;python3 xxx.py 注意是绝对路径
使用实例
 　　实例1：每1分钟执行一次command

　　命令：* * * * * command

　　

　　实例2：每小时的第3和第15分钟执行

　　命令：3,15 * * * * command

 

　　实例3：在上午8点到11点的第3和第15分钟执行

　　命令：3,15 8-11 * * * command

 

　　实例4：每隔两天的上午8点到11点的第3和第15分钟执行

　　命令：3,15 8-11 */2 * * command

 

　　实例5：每个星期一的上午8点到11点的第3和第15分钟执行

　　命令：3,15 8-11 * * 1 command

 

　　实例6：每晚的21:30重启smb 

　　命令：30 21 * * * /etc/init.d/smb restart

 

　　实例7：每月1、10、22日的4 : 45重启smb 

　　命令：45 4 1,10,22 * * /etc/init.d/smb restart

 

　　实例8：每周六、周日的1 : 10重启smb

　　命令：10 1 * * 6,0 /etc/init.d/smb restart

 

　　实例9：每天18 : 00至23 : 00之间每隔30分钟重启smb 

　　命令：0,30 18-23 * * * /etc/init.d/smb restart

 

　　实例10：每星期六的晚上11 : 00 pm重启smb 

　　命令：0 23 * * 6 /etc/init.d/smb restart

 

　　实例11：每一小时重启smb 

　　命令：* */1 * * * /etc/init.d/smb restart

 

　　实例12：晚上11点到早上7点之间，每隔一小时重启smb 

　　命令：* 23-7/1 * * * /etc/init.d/smb restart

 

　　实例13：每月的4号与每周一到周三的11点重启smb 

　　命令：0 11 4 * mon-wed /etc/init.d/smb restart

 

　　实例14：一月一号的4点重启smb 

　　命令：0 4 1 jan * /etc/init.d/smb restart

 

　　实例15：每小时执行/etc/cron.hourly目录内的脚本

　　命令：01   *   *   *   *     root run-parts /etc/cron.hourly

　　说明：

　　run-parts这个参数了，如果去掉这个参数的话，后面就可以写要运行的某个脚本名，而不是目录名了

>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

#!/bin/sh
. /etc/init.d/functions
VERSION="`basename $0` ver. 0.91"
USAGE="Usage: `basename $0` < option > | --status-all | \
[ service_name [ command | --full-restart ] ]"
SERVICE=
SERVICEDIR="/etc/init.d"
OPTIONS=
if [ $# -eq 0 ]; then
   echo "${USAGE}" >&2
   exit 1
fi
cd /
while [ $# -gt 0 ]; do
  case "${1}" in
    --help | -h | --h* )
       echo "${USAGE}" >&2
       exit 0
       ;;
    --version | -V )
       echo "${VERSION}" >&2
       exit 0
       ;;
    *)
       if [ -z "${SERVICE}" -a $# -eq 1 -a "${1}" = "--status-all" ]; then
          cd ${SERVICEDIR}
          for SERVICE in * ; do
            case "${SERVICE}" in
              functions | halt | killall | single| linuxconf| kudzu)
                  ;;
              *)
                if ! is_ignored_file "${SERVICE}" \
                    && [ -x "${SERVICEDIR}/${SERVICE}" ]; then
                  env -i LANG="$LANG" PATH="$PATH" TERM="$TERM" "${SERVICEDIR}/${SERVICE}" status
                fi
                ;;
            esac
          done
          exit 0
       elif [ $# -eq 2 -a "${2}" = "--full-restart" ]; then
          SERVICE="${1}"
          if [ -x "${SERVICEDIR}/${SERVICE}" ]; then
            env -i LANG="$LANG" PATH="$PATH" TERM="$TERM" "${SERVICEDIR}/${SERVICE}" stop
            env -i LANG="$LANG" PATH="$PATH" TERM="$TERM" "${SERVICEDIR}/${SERVICE}" start
            exit $?
          fi
       elif [ -z "${SERVICE}" ]; then
         SERVICE="${1}"
       else
         OPTIONS="${OPTIONS} ${1}"
       fi
       shift
       ;;
   esac
done
if [ -x "${SERVICEDIR}/${SERVICE}" ]; then
   env -i LANG="$LANG" PATH="$PATH" TERM="$TERM" "${SERVICEDIR}/${SERVICE}" ${OPTIONS}
else
   echo $"${SERVICE}: unrecognized service" >&2
   exit 1
fi