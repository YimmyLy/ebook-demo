<template>
    <div>
        <transition name="slide-down">
            <div class="title-wrapper" v-show='showFlag'>
                <div class="left">
                    <span class="icon-back icon"></span>
                </div>
                <div class="right">
                    <div class='icon-wrapper'>
                        <span class="icon-cart icon"></span>
                    </div>
                    <div class='icon-wrapper'>
                        <span class="icon-person icon"></span>
                    </div>
                    <div class='icon-wrapper'>
                        <span class="icon-more icon"></span>
                    </div>
                </div>
            </div>
        </transition>
    </div>
</template>
<script>
export default {
    props:{
        showFlag:{
            type:Boolean,
            default:false
        }
    },
    data() {
        return {
            
        }
    },
}
</script>
<style lang="scss" scoped>
@import '../assets/styles/global';
.title-wrapper{
    position: absolute;
    top:0;
    left: 0;
    width: 100%;
    height: px2rem(48);
    background: #fff;
    box-shadow:0 px2rem(6) px2rem(6) rgba(0,0,0,.1);
    display: flex;
    z-index: 101;
    .left{
        flex:0 0 px2rem(60);
        @include center;
    }
    .right{
        flex:1;
        display: flex;
        justify-content: flex-end;
        .icon-wrapper{
            width: px2rem(48);
            @include center;
            .icon-cart{
                width: px2rem(22)
            }
        }
    }
    
}
</style>
