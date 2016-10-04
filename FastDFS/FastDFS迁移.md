## fastdfs 迁移

> FastDFS集群整体迁移的问题。  
> 如果新旧IP地址一一对应，而且是一样的，那非常简单，直接将data目录拷贝过去即可。  
> 
> IP不一样的话，会比较麻烦一些。  
> 如果使用了V4的自定义server ID特性，那么比较容易，直接将tracker上的IP和ID映射文件storage_ids.conf修改好即可。  
> 
> 如果是用IP地址作为服务器标识，那么需要修改tracker和storage的data目录下的几个数据文件，将旧IP调整为新IP。  
> 注意storage的data目录下有一个.打头的隐藏文件也需要修改。  
> 另外，需要将后缀为mark的IP地址和端口命名的同步位置记录文件名改名。  
> 文件全部调整完成后才能启动集群服务。  
> 
> 将下面文件里的IP改为新的公网IP：  
>
> tracker server上需要调整的文件列表：  
> - data/storage_groups_new.dat  
> - data/storage_servers_new.dat  
> - data/storage_sync_timestamp.dat  
> 
> storage server需要调整的文件列表：  
> - data/.data_init_flag  
> - data/sync/${ip_addr}_${port}.mark：此类文件，需要将文件名中的IP地址调整过来。  

## 线上迁移

### 将数据同步至新机器上

> 原先使用scp，结果发现因为FastDFS storage目录下做了软链到当前的数据目录：  
> - ln -s /data/fastdfs/storage/data /data/fastdfs/storage/data/M00  
>
> 导致复制的时候死循环，不停的copy(M00/M00...);  
> 解决方法：后来改用 rsync -avuz 同步整个storage  

### 更改配置

> storage：  
> - 更改 .data_init_flag 里的IP（全改成公网的IP）  
> - 直接 rm 192.168.2.52_23000.mark  
> - 其他地方如果有IP也要更改  
>
> tracker：  
> - tracker下内容直接rm掉  

### 再次同步 + 切换

> 准备完成后需要再次进行同步，以免丢失准备期间的数据  
> 为了避免同步了某些文件（上面那些改动过的文件），所以简单弄了个脚本进行同步  
> - 这样是为了避免旧机器的一些配置改动了，然后同步过来又得需要进行改动（改IP）  
> - 最可靠的方法就是同步的时候忽略这些配置文件，只同步需要的数据  
>
> 同步完成之后切换DNS的解析即可  
>

**rsync迁移fastdfs脚本**  

```
#!/bin/bash

cd /data/fastdfs/storage/data/
for i in `ls |grep -oP '^..$'`;do 
    rsync -avuz root@192.168.2.59:/home/fastdfs-storage/storage_server/data/$i .
    #echo "/home/fastdfs-storage/storage_server/data/$i $i"
done
rsync -avuz root@192.168.2.59:/home/fastdfs-storage/storage_server/data/sync/binlog* .
```
