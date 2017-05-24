


# 1. 聚类数据集

+ [cs.joensuu.fi](http://cs.joensuu.fi/sipu/datasets/)



# 2. 社交网络数据集

+ [Newman](http://www-personal.umich.edu/~mejn/netdata/)  
+ [斯坦福网络数据SNAP](http://snap.stanford.edu/data/index.html)  
+ [LFR人工网络数据集工具](http://santo.fortunato.googlepages.com/benchmark.tgz)  
  LFR是一个Linux-C++工具，所以必须安装g++。 
  
  **LFR生成网络步骤**

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
+ 关于参数mu  
`1-mu`是节点与其同一个社区内的节点之间的共享比例，`mu`是节点与其它社区节点的共享比例。`mu`设置的小一点，社区内部更加紧凑。

   **LFR得到的网络数据**

`network.dat`文件是得到的网络结构，每一行表示一条边。文件`community.dat`是该网络的真实社区结构其每一行表示 (节点、节点所在的社区)。

从community.dat和network.dat中提取网络代码,并将结果保存在LFR-1.gml文件中：

```python
#coding=utf-8
import networkx as nx
from collections import defaultdict

def transfer_2_gml():
	"""--------------------------------------------------------------------------
	function:   将LFR的network.dat和community.dat转化为.gml文件
	parameter:  
	return:     
	-------------------------------------------------------------------------------"""
	nodes_labels={}
	k=0
	with open("community.dat","r") as f:
		for line in f.readlines():
			items=line.strip("\r\n").split("\t")
			v=items[0]
			label=items[1]
			nodes_labels[v]=label
		#end-for
	#end-with

	
	G=nx.Graph()
	for v in nodes_labels.keys():  #添加所有的节点，并且记录其真实标签
		G.add_node(v, value=nodes_labels[v])

	edges=set()
	with open("network.dat","r") as f:
		for line in f.readlines():
			items=line.strip("\r\n").split("\t")
			a=items[0]
			b=items[1]
			if (a,b) not in edges  or (b,a) not in edges:
				edges.add( (a,b) )
		#end-for
	#end-with

	for e in edges:
		a,b=e
		G.add_edge(a,b,type="Undirected")

	nx.write_gml(G,"LFR-1.gml")
	print ("transfer LFR(*.dat) data to *.gml")

	communities=defaultdict(lambda :[])
	for v in nodes_labels.keys():
		label=nodes_labels[v]
		communities[label].append(v)
	for c in communities.keys():
		print ("community ", c ,": \n", communities[c], "\n")
def main():
	transfer_2_gml()

if __name__ == '__main__':
	main()
```

# 3. 


