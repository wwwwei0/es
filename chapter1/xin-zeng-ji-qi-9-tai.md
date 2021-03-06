第一步、`ROOT` 用户挂载 `SSD`，[参考](https://help.aliyun.com/document_detail/25426.html?spm=5176.doc25446.2.4.5NxhED)

```
sudo fdisk -l 
fdisk /dev/vdb 对数据盘进行分区。根据提示，依次输入 n，p，1，两次回车，wq，分区就开始了。
fdisk -l 
mkfs.ext3 /dev/vdb1
echo /dev/vdb1 /mnt ext3 defaults 0 0 >> /etc/fstab
cat /etc/fstab
mount /dev/vdb1 /mnt
df -h

后四条
echo /dev/vdb1 /mnt ext3 defaults 0 0 >> /etc/fstab && cat /etc/fstab && mount /dev/vdb1 /mnt && df -h
```

第二步、登录`9`台新机器，创建 `idatage` 用户，`root` 权限

创建用户`sudo adduser idatage`

添加`root`权限`sudo vim /etc/sudoers`

`idatage ALL=NOPASSWD:ALL`

第三步、`ROOT` 用户配置私钥免密码登录，[参考](/chapter1.md)

第四步、修改 `hosts` 文件，[参考](/chapter1.md)

第五步、执行 `ansible-playbook -s site.yml`

第六步、执行 `ansible all -m ping` 检查

第七步、修改系统配置，[参考](/system-pei-zhi.md)

```
ansible all -s -m raw -a '
grep "* - nofile 512000" /etc/security/limits.conf || echo "* - nofile 512000" >> /etc/security/limits.conf
grep "elasticsearch - nproc unlimited" /etc/security/limits.conf || echo "elasticsearch - nproc unlimited" >> /etc/security/limits.conf
grep "fs.file-max = 1024000" /etc/sysctl.conf || echo "fs.file-max = 1024000" >> /etc/sysctl.conf
grep "vm.max_map_count = 262144" /etc/sysctl.conf || echo "vm.max_map_count = 262144" >> /etc/sysctl.conf
grep "vm.swappiness = 1" /etc/sysctl.conf || echo "vm.swappiness = 1" >> /etc/sysctl.conf #禁用 swapping
sysctl -p'
```

第八步、新增 `elasticsearch_32g_9tai.yml`，主要两个方面，修改 `discovery.zen.ping.unicast.hosts` 只加入三个 `master` 节点。

```
- hosts: elasticsearch_data_nodes_32g_9tai
  become: yes
  roles:
    - { role: elasticsearch, es_instance_name: "node",
    es_config: {
        cluster.name: "tarantula",
        discovery.zen.ping.unicast.hosts: "node03,node04,node05",
        network.host: "_eth0_, _local_",
        http.port: 9222,
        transport.tcp.port: 9333,
        node.data: true,
        node.master: false,
        bootstrap.memory_lock: true,
        discovery.zen.minimum_master_nodes: 2,
  #      gateway.expected_nodes: 15,
  #      gateway.recover_after_nodes: 15,
        action.destructive_requires_name: true,
        indices.breaker.total.limit: 70%,
        indices.breaker.fielddata.limit: 24%,
        indices.breaker.request.limit: 40%,
        indices.fielddata.cache.size: 20%,
        indices.queries.cache.size: 40%,
        indices.memory.index_buffer_size: 10%
        }
    }
  vars:
    es_templates: false
    es_scripts: true
    es_java_install: true
    es_major_version: "5.x"
    es_version: "5.4.0"
    es_heap_size: "16g"
    es_api_port: 9222
    es_max_map_count: 262144
    es_max_open_files: 512000
    es_pid_dir: "/mnt/run/elasticsearch"
    es_data_dirs: "/mnt/lib/elasticsearch"
    es_log_dir: "/mnt/log/elasticsearch"
#    es_enable_xpack: true
#    es_xpack_license: "{{ lookup('file', '/tmp/peng-qiuyuan-a8b6beab-10ff-480f-9eff-7295efd58242-v5.json')  }}"
#    es_xpack_features:
#      - monitoring
```

第九步、停止分片自动分配

```
curl -XPUT http://127.0.0.1:9222/_cluster/settings -d '{
  "transient" : {
    "cluster.routing.allocation.enable" : "none"
  }
}'
```

第十步、执行 `ansible` 的 `elasticsearch_32g_9tai.yml`，等待新增节点全部启动。之后需要手动添加 `IK` 和 `xpack` 插件

```
ansible-playbook -s elasticsearch_32g_9tai.yml
```

第十一步、先手动禁用分片分配，待新添加机器全部加入集群

```
curl  -XGET http://127.0.0.1:9222/_cat/health?v

curl -XGET http://127.0.0.1:9222/_cat/nodes?v

curl  -XGET http://127.0.0.1:9222/_cluster/settings?pretty

curl -XGET '127.0.0.1:9222/_recovery?pretty&human&detailed=true&active_only=true' 

curl -XGET '127.0.0.1:9222/_cat/recovery?v&pretty&human&active_only=true&detailed=true'

curl -XPUT http://127.0.0.1:9222/_cluster/settings -d '{
  "transient" : {
    "cluster.routing.allocation.enable" : "none"
  }
}'

新机器都加入集群之后执行

curl -XPUT http://127.0.0.1:9222/_cluster/settings -d '{
  "transient" : {
    "cluster.routing.allocation.enable" : "all"
  }
}'

ansible all -s -m raw -a 'tail -50 /mnt/log/elasticsearch/node*-node/tarantula.log'
```

第十二步、在这里的时候，新机器就已经加入到集群里了，只是没有安装 `IK` 分词插件导致，分片不平衡 。安装 `IK`

```
ansible node31,node32,node33,node34,node35,node36,node37,node38,node39 -s -m copy -a 'src=/home/idatage/plugins/elasticsearch-analysis-ik-5.4.0.zip dest=/usr/share/elasticsearch/elasticsearch-analysis-ik-5.4.0.zip'

ansible node31,node32,node33,node34,node35,node36,node37,node38,node39 -s -m raw -a 'ls /usr/share/elasticsearch'

ansible node31,node32,node33,node34,node35,node36,node37,node38,node39 -s -m raw -a 'apt-get install unzip'

ansible node31,node32,node33,node34,node35,node36,node37,node38,node39 -s -m raw -a 'unzip /usr/share/elasticsearch/elasticsearch-analysis-ik-5.4.0.zip -d /usr/share/elasticsearch/plugins/ik'

ansible node31,node32,node33,node34,node35,node36,node37,node38,node39 -s -m raw -a 'ls /usr/share/elasticsearch/plugins/ik'

ansible node31,node32,node33,node34,node35,node36,node37,node38,node39 -s -m raw -a 'service node_elasticsearch restart'
```

第十三步、等待分片再平衡，10T 数据 1 个多小时

```
cluster.routing.allocation.cluster_concurrent_rebalance:22

indices.recovery.max_bytes_per_sec:3072mb

查看分片分配情况

curl -XGET '127.0.0.1:9222/_recovery?pretty&human&detailed=true&active_only=true' 

curl -XGET '127.0.0.1:9222/_cat/recovery?v&pretty&human&active_only=true&detailed=true'
```

第十四步、安装 `xpack` （`ansible` 启动`xpack plugins`会被移除），重启

```
#在node01（安装ansible的机器）上执行

ansible node31,node32,node33,node34,node35,node36,node37,node38,node39 -s -m raw -a 'mkdir /home/idatage/download && mkdir /home/idatage/plugins'

# copy x-pack-5.4.0.zip 到每一台待安装的机器

ansible node31,node32,node33,node34,node35,node36,node37,node38,node39 -s -m copy -a 'src=/home/idatage/download/x-pack-5.4.0.zip dest=/home/idatage/download/x-pack-5.4.0.zip'

#修改 /usr/share/elasticsearch/bin/elasticsearch-plugin ansible安装es节点为ES_ENV_FILE="/etc/default/node_elasticsearch"

ansible node31,node32,node33,node34,node35,node36,node37,node38,node39 -s -m copy -a 'src=/usr/share/elasticsearch/bin/elasticsearch-plugin dest=/usr/share/elasticsearch/bin/elasticsearch-plugin'

#使用command 一次安装

ansible node31,node32,node33,node34,node35,node36,node37,node38,node39 -s -m command -a '/usr/share/elasticsearch/bin/elasticsearch-plugin install file:///home/idatage/download/x-pack-5.4.0.zip'

#在node01（安装ansible的机器）上执行，特殊：替换自己编译的 x-pack-5.4.0.jar

ansible node31,node32,node33,node34,node35,node36,node37,node38,node39 -s -m copy -a 'src=/home/idatage/plugins/x-pack-5.4.0.jar dest=/usr/share/elasticsearch/plugins/x-pack/x-pack-5.4.0.jar'
```

第十五步、重启新增集群

```
ansible node31,node32,node33,node34,node35,node36,node37,node38,node39 -s -m raw -a 'service node_elasticsearch restart'

ansible all -s -m raw -a 'tail -50 /mnt/log/elasticsearch/node*-node/tarantula.log'
```



