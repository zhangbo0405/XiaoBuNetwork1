// 虚拟滚动可以理解为三个盒子外层为视窗, 中间层为整个数据列表的高度盒子没有渲染任何数据只是做撑开高度和滚动的作用, 
// 内层盒子进行数据渲染和展示并且滚动的时候内层盒子不断的替换数据并且模拟滚动效果;
// scrollTop的运用以及滚动的事件处理
<template>
    <div :style="`height:${viewH}px;overflow-y:scroll`" @scroll="handleScroll" class="out">
        <div :style="`height:${scrollH}px`" class="list">
            <div class="item_box" :style="`transform:translateY(${offsetY}px)`">
                <div class="item"
                     :style="`height: ${itemH}px`"
                     v-for="(item, index) of list"
                     :key="index">
                    {{ item }}
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        name: "virtualList",
        props: {
            data: Array,   // 列表总数据
            viewH: Number, // 外部高度
            itemH: Number, // 单项高度
        },
        data() {
            return {
                scrollH: '', // 整个滚动列表高度
                list: [],    // 每次显示的数据
                showNum: '',
                offsetY: '',// 动态偏移量- 外层的盒子进行滚动设置
                lastTime: '',
            }
        },
        mounted() {
            // 初始化计算
            this.scrollH = this.data.length * this.itemH;
            // 计算可视化高度一次能装几个列表, 多设置几个防止滚动时候直接替换
            this.showNum = Math.floor(this.viewH / this.itemH) + 4;
            // 默认展示几个
            this.list = this.data.slice(0, this.showNum);
            this.lastTime = new Date().getTime();
        },
        methods: {
            handleScroll(e) {
                if (new Date().getTime() - this.lastTime > 10) {
                    let scrollTop = e.target.scrollTop; // 滚动条的宽度
                    // 每一次滚动后根据scrollTop值获取一个可以整除itemH结果进行偏移
                    // 例如: 滚动的scrllTop = 1220  1220 % this.itemH = 20  offsetY = 1200
                    this.offsetY = scrollTop - (scrollTop % this.itemH);
                    console.log(scrollTop, scrollTop % this.itemH);
                    this.list = this.data.slice(
                        Math.floor(scrollTop / this.itemH),  // 计算卷入了多少条
                        Math.floor(scrollTop / this.itemH) + this.showNum
                    )
                    this.lastTime = new Date().getTime();
                }
            }
        }
    }
</script>
