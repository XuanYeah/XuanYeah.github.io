####  什么是LinkedList  ####
链表（Linked list）是一种常见的基础数据结构，是一种线性表，但是并不会按线性的顺序存储数据，而是在每一个节点里存到下一个节点的指针(Pointer)。
#### 如何实现LinkedList ####
首先选取链表操作中的一个切面进行静态分析

![linked list](https://wx1.sinaimg.cn/mw690/b17c16cbly1fnrhbvjzjtj215o0f8myb.jpg)

可以看到，新的表节点会包含一个指向前一个表节点的指针，`LinkedList`是一个指向整条链上的第一个表节点的指针。对于表的遍历而言，当某一个表节点的`Next`指针为空时，则该表节点为链上最后一个节点。
```java
public class Node {
    private String name ;    
    private Node nextNode ;    //表节点中包括一个指向前一节点的指针
   
    /*省略getter & setter & toString*/
   
    }
```
LinkedList 的`instance variable`为名为`firstNode`的指针，该指针永远指向链上的最新表节点
````java
public void addNode(String name){
    if(firstNode == null){
        firstNode = new Node();
        firstNode.setName(name);
        //如果链表还未初始化，则初始化firstNode
        firstNode.setNextNode(null);    
    }else{
        Node node = new Node();
        node.setName(name);
        //如果链表已经初始化，则firstNode指向新加入节点，新加入
        //节点指向前一节点
        node.setNextNode(firstNode);
        firstNode = node ;
    }
}
````
现在可以通过增加节点构建链表，如何遍历链表，通过打印整个链表功能来解析
````java
public void display(){
	  //建立游标
    Node currentNode = firstNode ;
    while(currentNode!=null){
    //当某个链表节点的Next指针为空的时候，该节点为链上最后的节点
        System.out.println(currentNode.toString());
        currentNode = currentNode.getNextNode();
    }
}
````
当想要删除某个节点的时候，将指向它的Next指针，改为指向该节点的Next指针指向的节点就可以实现链上移除。首先建立两个游表，以`firstNode`为初始节点，分别指向当前节点以及当前节点的前一节点。如果当前节点的`name`与目标不匹配，则两个游标节点分别向前移一位。当匹配时，如果当前节点是`firstNode`，向前偏移一位，如果不是`firstNode`的话，则变前一节点的Next指针指向为当前节点的下一节点。
````java

public Node remove(String name){
    Node currentNode = firstNode ;
    Node previousNode = firstNode ;
    while(!currentNode.getName().equals(name)){
        if(currentNode.getNextNode()==null){
            System.out.println("Sorry the node does not exist");
        }else{
            previousNode = currentNode ;
            currentNode = currentNode.getNextNode();
        }
    }
    if(currentNode.equals(firstNode)){
        firstNode = firstNode.getNextNode();
    }else{
        previousNode.setNextNode(currentNode.getNextNode());
    }

    return currentNode ;
}

````

#### 延伸 ####
并非只有这一种实现链表的方式，借由数组及其`Random Access`的特性，依然可以实现链表，只是由于数组需要在初始化时给定大小，失去了一部分灵活性。`Stack`与`Queue`与`LinkedList`有诸多相似之处，后面的文章中会提及。


