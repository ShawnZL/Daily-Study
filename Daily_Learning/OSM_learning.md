# OSM数据结构

数据基元分为三种，Node（点）、路（ways）和关系（relations），Node定义了空间中点的位置，ways定义了线或区域，Relation定义了元素之间的关系。

| name      | Value          | Description                                                  |
| --------- | -------------- | ------------------------------------------------------------ |
| user      | String         | The display name of the user who last modified the object. A user can change their display name |
| uid       | integer        | The numeric user id. of the user who last modified the object. The user id. number will remain constant. |
| timestamp |                | time of the last modification                                |
| visible   | "true" "false" | whether the object is deleted or not in the database, if visible="false" then the object should only be returned by history calls. |
| version   | integer        | The edit version of the object. Newly created objects start at version 1 and the value is incremented by the server when a client uploads a new version of the object. The server will reject a new version of an object if the version sent by the client does not match the current version of the object in the database. |
| changeset | integer        | The [changeset](http://wiki.openstreetmap.org/wiki/Changeset) in which the object was created or updated. |

# Node

通过经纬度定义一个地理坐标点。同时，height定义海拔，通过layer=\*和level=\*，可以标示物体所在的地图层面与所在建筑物内的层数；通过place和name表示对象的名称，way也是通过多个node连接起来的。

```
<osm version="0.6" generator="OpenStreetMap server">
    <node id="483034256" lat="55.9458449" lon="-3.2035477" version="1"
        changeset="2369219" user="spytfyre" uid="166957" visible="true"
        timestamp="2009-09-04T13:35:42Z">
        <tag k="name" v="The Blue Blazer" />
        <tag k="amenity" v="pub" />
    </node>
</osm>
```

lat与lon 纬度和经度

`<tag k="amenity" v="pub" />` tag里的k，v属性，相信做Java的都可以猜得到，就是key，value。

# Way

通过2-2000个点（nodes）构成了*way。way*可表示如下3种图形事物（非闭合线、闭合线、区域）。对于超过2000 nodes的way，可以通过分割来处理。

```
<osm version="0.6" generator="OpenStreetMap server">
    <way id="43157302" visible="true" timestamp="2009-10-26T10:45:09Z"
        version="1" changeset="2954960" user="Ed Avis" uid="31257">
        <nd ref="540653724" />
        <nd ref="25507043" />
        <nd ref="107762" />
        <nd ref="25507038" />
        <nd ref="107759" />
        <tag k="highway" v="primary" />
        <tag k="lcn_ref" v="6a" />
        <tag k="name" v="Parliament Street" />
    </way>
</osm>
```

ref 属性告知走的是哪一个点，连接成点的线

## Open polyline

非闭合线：收尾不闭合的线段。通常可用于表示现实中的道路、河流、铁路等。

## Closed polyline

闭合线：收尾相连的线。例如可以表示现实中的环线地铁。

## Area

区域：闭合区域。通常使用`landuse=*` 来标示区域等。

# Relation

一个[Relation](http://wiki.openstreetmap.org/wiki/Relation)可由一系列nodes, ways 或者其他的relations来组成，相互的关系通过[role](http://wiki.openstreetmap.org/wiki/Role)来定义。一个元素可以在relation中被多次使用，而一个relation可以包含其他的relation。

```
<osm version="0.6" generator="OpenStreetMap server">
    <relation id="113421" visible="true" timestamp="2009-11-03T10:08:27Z"
        version="2" changeset="3023369" user="Jonathan Bennett" uid="5352">
        <member type="node" ref="270186" role="via" />
        <member type="way" ref="4418767" role="from" />
        <member type="way" ref="4641665" role="to" />
        <tag k="restriction" v="no_right_turn" />
        <tag k="type" v="restriction" />
    </relation>
</osm>
```



# Tag

标签不是地图基本元素，但是各元素都通过tag来记录数据信息。通过'key' and a 'value'来对数据进行记录（了解xml或者数据库的应该都比较清楚了吧？）。例如，可以通过highway=residential来定义居住区道路；同时，可以使用附加的命名空间来添加附加信息，例如：maxspeed:winter=*就表示冬天的最高限速。

具体的tag分类可参看：http://wiki.openstreetmap.org/wiki/Map_Features

# 简单的说

OSM数据由以下元素组成：

节点(Nodes): 最基本的要素,是其它要素的组成部分.
路线(Ways): 有方向性的节点序列, 在编辑器里显示成连接起来的线段.
闭合路线(Closed Ways): 闭合路线是完全闭合的路线. 一般用来表示区域如公园,湖泊,岛屿等.



todo

https://blog.csdn.net/qq_39426934/article/details/90580794

https://my.oschina.net/u/4393165/blog/4601574

https://www.jianshu.com/p/d57927bef4ff

https://blog.csdn.net/python_n/article/details/115485384?spm=1001.2101.3001.6650.2&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-2.pc_relevant_aa&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-2.pc_relevant_aa&utm_relevant_index=4

https://blog.51cto.com/lovespss/515192