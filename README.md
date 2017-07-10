# H5_EgretEUI-Lab

## 本例是egret的demo

## PS:下面是我封装的一个下拉菜单控件：

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
