# H5_EgretEUI-Lab

## 本例是egret的demo

## PS:
## 1、下面是封装的一个下拉菜单控件：

* [控件链接地址](http://bbs.egret.com/thread-27076-1-1.html)

```typescript
module euiextendsion {
    export class DropDwonList extends eui.Component {
        //展开、收回列表的按钮
        private btn: eui.Button;
        //滚动的列表
        private scroller: eui.Scroller;
        //滚动列表上的遮罩
        private spMask: egret.Shape;
        //列表是否展开了
        private isShow: boolean = false;
        //滚动区域消失位置的 y 坐标
        private posScrollerClose: number;
        //list
        private list:eui.List;
        //提供给外部调用：获取当前list所选的值
        public selectedValue:string;
        //提供给外层的当前选中的项目的index值
        public selectedIndex:number;

        // 构造函数参数说明：
        // x：下拉菜单的x坐标
        // y：下拉菜单的y坐标
        // skin：下拉菜单各菜单项的皮肤，皮肤高度就是各菜单项的高度   传入皮肤名字字符串
        // BtnValue：按钮文字
        // scrollWidth：下拉框显示的宽度
        // scrollHeight：下拉框显示的高度
        // ...rest：下拉菜单各项值
        constructor(x:number,y:number,skin:string = "",BtnValue:string,scrollWidth:number,scrollHeight:number,...rest) {
            super();
            //创建滚动区域和列表
            var arr = [];
            for (var i = 0; i < rest.length; i++) {
                arr.push(rest);
            }
            this.list = new eui.List();
            this.list.width = scrollWidth;
            this.list.itemRendererSkinName = skin; //ButtonSkin
            this.list.dataProvider = new eui.ArrayCollection(arr);
            var scroller = new eui.Scroller();
            scroller.height = scrollHeight;
            scroller.width = scrollWidth;
            scroller.viewport = this.list;
            this.addChild(scroller);
            // scroller.verticalScrollBar.autoVisibility = false;
            // scroller.verticalScrollBar.visible = true;
            // this.scroller.autoHideScrollBars = false;
            // scroller.horizontalScrollBar


            //列表上面的遮罩
            var spMask = new egret.Shape();
            spMask.graphics.beginFill(0x000000);
            spMask.graphics.drawRect(0, 0, scrollWidth, scrollHeight);
            spMask.graphics.endFill();
            this.addChild(spMask);
            scroller.mask = spMask;

            var btn = new eui.Button();
            btn.label = BtnValue;
            btn.width = scrollWidth;
            btn.height = 35;
            this.addChild(btn);

            this.btn = btn;
            this.scroller = scroller;
            this.spMask = spMask;
            this.x = x;
            this.y = y;
            this.list.addEventListener(eui.ItemTapEvent.ITEM_TAP,this.onClickList,this);
        }
        protected createChildren() {
            super.createChildren();
            this.spMask.y = this.btn.height;
            //设置消失点坐标
            this.posScrollerClose = this.scroller.y = -this.scroller.height;
            this.btn.addEventListener(egret.TouchEvent.TOUCH_TAP, this.changeListType, this);
        }
        private changeListType(): void {
            if (!this.isShow) {
                egret.Tween.get(this.scroller).to({y:this.btn.height}, 300);
                this.isShow = true;
            } else {
                egret.Tween.get(this.scroller).to({y:this.posScrollerClose}, 300);
                this.isShow = false;
            }
        }

        public onClickList(evt:eui.UIEvent) : void
        {
            // console.log(this.list.selectedItem,this.list.selectedIndex)
            this.selectedValue = this.btn.label = this.list.selectedItem;
            if (!this.isShow) {
                egret.Tween.get(this.scroller).to({y:this.btn.height}, 300);
                this.isShow = true;
            } else {
                egret.Tween.get(this.scroller).to({y:this.posScrollerClose}, 300);
                this.isShow = false;
            }
            this.selectedIndex = this.list.selectedIndex;   // this.selectedIndex 暴露给外部调用以获取当前选择的菜单项目序号。
        }

    }
}

```

## 2、屏幕适配策略的一点研究
* [适配策略地址](http://bbs.egret.com/thread-27676-1-1.html)

## 3、笔记：
```
1、所有的容器全部继承自 DisplayObjectContainer 类，这个名称为 DisplayObjectContainer 的类又继承自 DisplayObject 。也就是说，在Egret中，所有的容器其实也继承自 DisplayObject。
在Egret中,DisplayObjectContainer 封装了一些显示列表中常用的功能。在后面的内容中，我们将详细介绍显示列表的操作。这些常用操作主要分为四类：
  ● 添加、删除子对象
  ● 访问子对象
  ● 检测子对象
  ● 设置叠放次序
在Egret中，我们还有一个其他的容器：Sprite。
如果你查看Sprite类的内容,你会发现，Sprite仅仅是继承 DisplayObjectContainer。同时添加了一个Graphics功能。

2、自定义显示对象类需要继承自 DisplayObject的具体子类，例如Shape或者TextField。
class MyGrid extends egret.Shape{
想要自定义一个容器非常容易，我们编写一个类，并且继承 DisplayObjectContainer 即可。当然，如果你想实现相关的Graphics绘图功能，你也可以继承 Sprite。

3、声明 Shape 对象：
 let  shp:egret.Shape = new egret.Shape();
 
 4、深度(层级)
每一个容器都会有一个自己的深度管理功能.这个深度值实际上就是一个显示对象的叠放次序。我们也称之为 “z-次序”。
Egret中容器的深度都是从0开始的，当一个显示对象第一个被添加到容器中时，它的深度值为0。这个显示对象也处于容器的最底层。当我们添加第二个显示对象的时候，他的深度值为1，并且在第一个显示对象上方。从0开始，每次深度加1
获取当前容器的子对象数量:：容器.numChildren
容器内添加对象：容器.addChild( 显示对象 );
将某一个显示对象添加到一个指定深度：容器.addChildAt( 显示对象, 深度值 )
2个深度值的容器进行交换深度值：父容器.swapChildrenAt( 深度值数字1,深度值数字2 );
将一个显示对象移除显示列表：容器.removeChild( 显示对象 )           //remove方法  只是不参与渲染，但还是存在于内存中的。
删除一个指定深度的显示对象：容器.removeChildAt( 深度值 ) 
将当前容器内的所有子对象全部移除显示列表：容器.removeChildren();
重设显示对象的深度：容器.setChildIndex( 显示对象, 新的深度值 );
容器.parent  可以获取当前容器的父容器(父级)

5、要获取一个容器的子对象：
第一种是通过子对象的深度值来获取子对象：父容器.getChildAt( 深度值 );     例：
let _spr:egret.DisplayObject = sprcon.getChildAt( 1 );
第二种是通过子对象的 name 属性来获取：
let _spr:egret.DisplayObject = sprcon.getChildByName( "sprite2" );  //前提是某个容器我们要设置好name属性：    容器.name = "sprite2";
对比：第一种方式性能更优，推荐。

6、文本：
Egret 提供三种不同的文本类型，不同类型具有以下特点：
普通文本：用于显示标准文本内容的文本类型。
输入文本：允许用户输入的文本类型。
位图文本：借助位图字体渲染的文本类型。

7、创建文本：
var label:egret.TextField = new egret.TextField(); 
label.text = "这是一个文本"; 
this.addChild( label );
1）egret.TextField 类包含一个 text 的属性，该属性为当前文本显示内容。
2）显示文本： egret.TextField 类继承自 egret.DisplayObject 。
displayObject.addChild( textField_Object );
3）字号：egret.TextField 类包含一个 size 的属性
还可以使用 egret.TextField.default_size 设置全局的默认文本字号大小。
PS：egret.TextField 对象自身尺寸会根据首次设置的文本内容自动计算。所以，当 text 属性被赋予不同内容时，其尺寸也会不同。

8、文本超链接事件
1）情况一：针对整个TextField的：TextField本身可以响应Touch事件
2）在一大段文字中，对部分文字超链接：
var tx:egret.TextField = new egret.TextField;
tx.textFlow = new Array<egret.ITextElement>(
            { text:"这段文字有链接", style: { "href" : "event:text event triggered" } }
            ,{ text:"\n这段文字没链接", style: {} }
);
tx.touchEnabled = true;
tx.addEventListener( egret.TextEvent.LINK, function( evt:egret.TextEvent ){
            console.log( evt.text );
}, this );
其中 href 属性的内容以 event: 开头，后边跟随一个字符串，用于输出相应的文字或用于识别包含该链接的文字段。
然后侦听 TextEvent.LINK 事件，在事件处理函数中通过事件对象的 text 属性来获取该段文字所设置的字符串。

9、exml使用背景：
背景可以使用exml的皮肤，同级下直接使用  exml文件名不带后缀写在skinname属性内
```

### 省略一大坨，有需要完整笔记的童鞋可以私信我 7012490
