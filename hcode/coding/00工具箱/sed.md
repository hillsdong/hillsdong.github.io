- sed 具有过滤和替换功能的流文本处理器
- 用法：sed [OPTION]... {script-only-if-no-other-script} [input-file]...
- 描述：sed 是一款流编辑器，用来对文件或流水线中的文本做基本的文本转换。相较于其他可编程的编辑器，sed 每次只做一次编辑，而且更高效。sed 还有文本过滤的能力，这也是它和其他编辑器最大的不同。
- -n, --quiet, --silent
- -e script, --expression script 默认可不写
- -f script-file, --file script-file 将脚本放在文件中
- --flow-symlinks
- -i[SUFFIX], --in-place[=SUFFIx] 就地正法，可备份
- -c, --copy 正法时，通过复制而不是重命名备份，不可用
- -b, --binary 二进制模式处理文本
- -l N, --line-lenght=N ??
- --pois 禁用 GNU 扩展
- -r, --regexp-extended 脚本中使用正则
- -u, --unbuffered 少加载数据，多清空缓存
- -z, --null-data 通过 NUL 换行
- --version
- 如果 -e/-f 没给，则把第一个非配置参数用做脚本，其他参数用作输入文件，若没有输入文件，则等待基本输入


http://linux.51yip.com/search/sed