# SVN源码安装

## 1.SVN和依赖源码包下载
```
# 提供两个地址
#   - 前一个是网上找的地址（可能失效）
#   - 另一个是自己github的下载地址

# svn 源码包下载地址
https://subversion.apache.org/download.cgi
https://raw.githubusercontent.com/chenshaorong/resource01/master/SVN/subversion-1.9.4.tar.gz

# arp、arp-util、arp-iconv
https://apr.apache.org/download.cgi
https://raw.githubusercontent.com/chenshaorong/resource01/master/SVN/apr-1.5.2.tar.gz
https://raw.githubusercontent.com/chenshaorong/resource01/master/SVN/apr-util-1.5.4.tar.gz
https://raw.githubusercontent.com/chenshaorong/resource01/master/SVN/apr-iconv-1.2.1.tar.gz

# sqlite-amalgamation
http://www.sqlite.org/sqlite-amalgamation-3071501.zip
https://raw.githubusercontent.com/chenshaorong/resource01/master/SVN/sqlite-amalgamation-3071501.zip

# zlib
http://zlib.net/zlib-1.2.8.tar.gz
https://raw.githubusercontent.com/chenshaorong/resource01/master/FastDFS/zlib-1.2.8.tar.gz
```

## 2.编译安装
```
# 下载源码到 /usr/local/src
cd /usr/local/src
# 解压并进入相应目录

# apr 安装
./configure --prefix=/usr/local/apr
make && make install

# apr-util 安装
./configure --prefix=/usr/local/apr-util --with-apr=/usr/local/apr/
make && make install

# zlib安装
./configure --prefix=/usr/local/zlib
make && make install

# 编译安装SVN
./configure --prefix /usr/local/svn --with-apr=/usr/local/apr --with-apr-util=/usr/local/apr-util --with-zlib=/usr/local/zlib
# 出现下图错误
```
!["error"](https://raw.githubusercontent.com/chenshaorong/resource01/master/SVN/01.png "csr")
```
# 按照提示下载解压sqlite-amalgamation并重命名+移动到指定位置
unzip sqlite-amalgamation-3071501.zip
mv sqlite-amalgamation-3071501 subversion-1.9.4/sqlite-amalgamation

# 再次 configure 则会编译通过
make && make install

# 最后 vim /etc/profile加入：
export PATH=/usr/local/svn/bin/:$PATH
```
