


# 1. 聚类数据集

+ [cs.joensuu.fi](http://cs.joensuu.fi/sipu/datasets/)



# 2. 社交网络数据集

+ [Newman](http://www-personal.umich.edu/~mejn/netdata/)  
+ [斯坦福网络数据SNAP](http://snap.stanford.edu/data/index.html)  
+ [LFR人工网络数据集工具](http://santo.fortunato.googlepages.com/benchmark.tgz)  
  LFR是一个Linux-C++工具，所以必须安装g++。 
  
  ** LFR生成网络步骤 **

首选跳转到LFR的binary_network目录中compile一下。
```
$ make
```

这样会生成一个benchmark文件，它就是我们构建网络的工具。使用benchmark生成一个网络数据的简单的例子：

```
$ ./benchmark -f flags.dat -t1 3
```

这里的flags.dat文件在解压缩包后就有的，它表示参数的设置。如果要生成其他的网络，要修改这个文件的参数。

```
$ ./benchmark [FLAG] [P]

[FLAG]		[P]
-N		number of nodes
-k		average degree
-maxk	maximum degree
-mu	mixing parameter
-t1		minus exponent for the degree sequence
-t2		minus exponent for the community size distribution
-minc	minimum for the community sizes
-maxc	maximum for the community sizes
-on		number of overlapping nodes
-om		number of memberships of the overlapping nodes
-C		[average clustering coefficient]
```
   ** LFR得到的网络数据 **

文件community.dat就是LFR得到的网络数据，它是一个类似于netowrk.dat的文件，每一行表示一条边。它保存社区内部的边，舍弃社区之间的边。

从community.dat提取网络代码,并将结果保存在LFR-1.gml文件中：

```python

communities=dict()
k=0
with open("community.dat","r") as f:
	for line in f.readlines():
		items=line.strip("\r\n").split("\t")
		a=items[0]
		b=items[1]

		flag=False
		for key in communities.keys():
			if a in communities[key] or b in communities[key]:
				communities[key].add(a)
				communities[key].add(b)
				flag=True
				break
		if flag is False:
			k+=1
			communities[k]=set()
			communities[k].add(a)
			communities[k].add(b)
	#end-for
#end-with
import networkx as nx
G=nx.Graph()
for key in communities.keys():
	print ("community ",key," : ", end="")
	for v in communities[key]:
		print (v,", ",end="")
		G.add_node(v, value=key)
	print ("\n----------------------------------------------------------------------------")

edges=[]
with open("network.dat","r") as f:
	for line in f.readlines():
		items=line.strip("\r\n").split("\t")
		a=items[0]
		b=items[1]
		edges.append([a,b])
	#end-for
#end-with

for e in edges:
	a,b=e
	G.add_edge(a,b,type="Undirected")

nx.write_gml(G,"LFR-1.gml")
```

# 3. 


