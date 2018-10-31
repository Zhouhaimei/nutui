<template>

    <div class="nut-parallaxscroll">        
        <slot></slot>       
    </div>
</template>
<script>
//是否出现在视口内（垂直方向）
function verInViewport(el, viewHeight) {
    var rect = el.getBoundingClientRect();    
    let top = viewHeight-40-rect.top;   
    console.log(top,rect.top) 
    return (rect.top > 0 && rect.top < viewHeight);
}
//是否出现在视口内（水平方向）
function horInViewport(el, viewWidth) {
    var rect = el.getBoundingClientRect();
    return (rect.left > 0 && rect.left < viewWidth);
}
export default {
    name:'nut-parallaxscroll',
    props: {
        viewport: {
            type: typeof window !== 'undefined' ? window.HTMLElement : Object,
            default: () => null
        },
    },
    data() {
        
        let clientHeight = document.documentElement.clientHeight||document.body.clientHeight;  
        let clientWidth = document.documentElement.clientWidth||document.body.clientWidth;  
        return {
           // slots:newSlots
              viewHeight:clientHeight,
              viewWidth:clientWidth
        };
    },
    methods: {
        //视差滚动模块得位置
        //当模块
        NewIntersectionObserver(options){      
            //判断是否可见   
            let isShow = null;   
            switch (options.direction) {
                case 'vertical':
                    isShow = verInViewport(this.$el,this.viewHeight);                  
                    break;
                case 'horizontal':
                    isShow = horInViewport(this.$el,this.viewWidth);                    
                    break;
            }
            return isShow;
        },
        listenElm(){
            let that = this;  
            this.requestAnimationFrame(() => {
                that.NewIntersectionObserver({direction:'vertical'})
            })              
        },
        //定时
        requestAnimationFrame (callback) {
        // 防止等待太久没有执行回调
        // 设置最大等待时间
            setTimeout(() => {
                if (this.isInit ) {
                    return
                }                
                callback()
            }, this.maxWaitingTime)
        // 兼容不支持requestAnimationFrame 的浏览器
            return (window.requestAnimationFrame || ((callback) => setTimeout(callback, 1000 / 60)))(callback)
        },
    },
    mounted(){
        let that = this;
        let slots = that.$slots.default;
        let newSlots = [];
        //这里是处理完的，忽略空白元素字节  
        for(let i=0,item;item=slots[i];i++){           
                item.tag&&newSlots.push(item);          
        }  
         
      
        
         window.addEventListener("scroll",that.listenElm)
    }
}
</script>
<style lang="scss">
.nut-parallaxscroll{
    position: relative;
}
[speed="0"]{
    // position: absolute;
    // top:0;
    // left: 0;
}
</style>