<template>
    <transition name="slide-rihgt">
        <div class = 'content'>
            <div class="content-wrapper" v-if="bookAvailable">
                <div class="content-item" v-for="(item,index) in navigation.toc" :key="index"
                 @click="jumpTo(item.href)">
                    <span class = 'text'>{{item.label}}</span>
                </div>
            </div>
            <div class="empty" v-else>加载中...</div>
        </div>
    </transition>
</template>
<script>
export default {
    props:{
        bookAvailable:Boolean,
        navigation:Object,
        bookAvailable:Boolean,
    },
    data() {
        return {
            
        }
    },
    methods: {
        // 目录
        jumpTo(target){
            this.$emit('jumpTo',target)
        },
    },
}
</script>
<style lang="scss" scoped>
@import '../assets/styles/global';
.content{
    position: absolute;
    top: 0;
    left: 0;
    color: #333;
    z-index: 102;
    width: 80%;
    height: 100%;
    font-size: px2rem(14);
    padding: px2rem(20);
    box-sizing: border-box;
    background: #fff;
    .content-wrapper{
        display: flex;
        flex-direction: column;
        justify-content: space-around;
        height: 100%;
    }
    .empty{
        display: flex;
        align-items: center;
        flex-direction: column;
        justify-content: space-around;
        height: 100%;
    }
}
</style>
