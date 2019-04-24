<template>
    <div class = 'menu-bar'>
        <!-- menu -->
        <transition name="slide-up">
            <div class="menu-wrapper" v-show='showFlag' :class="{'hide-box-shadow':ifSettingShow}">
                <div class="icon-wrapper">
                    <span class = 'icon icon-menu' @click="showSettingFlag(3)"></span>
                </div>
                <div class="icon-wrapper">
                    <span class = 'icon icon-progress' @click="showSettingFlag(2)"></span>
                </div>
                <div class="icon-wrapper">
                    <span class = 'icon icon-bright' @click="showSettingFlag(1)"></span>
                </div>
                <div class="icon-wrapper">
                    <span class = 'icon icon-a' @click="showSettingFlag(0)">A</span>
                </div>
            </div>
        </transition>
        <!-- 设置 -->
        <transition name="slide-up">
            <div class = 'setting-wrapper' v-show="ifSettingShow">
                <!-- 设置字体 -->
                <div class = 'setting-font-size' v-if="showTag == 0">
                    <div class="preview" :style="{fontSize:fontSizeList[0].fontSize + 'px'}">A</div>
                        <div class="select">
                            <div class="select-wrapper" v-for="(item,index) in fontSizeList" :key="index" @click="setFontSize(item.fontSize)">
                                <div class="line" ref = 'line'></div>
                                <div class="point-wrapper">
                                    <div class = 'point' v-show = "defalutFontSize == item.fontSize">
                                        <div class="small-point"></div>
                                    </div>
                                </div>
                                <div class="line"></div>
                            </div>
                        </div>
                    <div class="preview" :style="{fontSize:fontSizeList[fontSizeList.length - 1].fontSize + 'px'}">A</div>
                </div>
                <!-- 设置主题 -->
                <div class="setting-themem" v-else-if="showTag == 1">
                    <div class = 'setting-theme-item' v-for='(item,index) in themeList' :key="index" @click="setTheme(index)">
                        <div class = 'preview' :style="{background:item.style.body.background}" :class="{'no-border':item.style.body.background != '#fff'}"></div>
                        <div class = 'text' :class="{'selected':index == defaultTheme}">{{item.name}}</div>
                    </div>
                </div>
                <!-- 进度条 -->
                <div class = 'setting-progress' v-else-if="showTag == 2">
                    <div class = 'progress-wrapper'>
                            <input class = 'progress' 
                                type="range"
                                max = '100'
                                min = '0'
                                step = '1'
                                @change = 'onProgressChange($event.target.value)'
                                @input = 'onProgressInput($event.target.value)'
                                :value="progress"
                                :disabled = '!bookAvailable'
                                ref = 'progress'
                        >
                    </div>
                    <div class="text-wrapper">
                        <span>{{bookAvailable ? progress + '%' : '加载中...'}}</span>
                    </div>
                </div>
            </div>
        </transition>
        <!-- 目录 -->
        <content-view :ifShowContent = 'ifShowContent'
                    v-show="ifShowContent"
                    :navigation = 'navigation'
                    :bookAvailable = 'bookAvailable'
                    @jumpTo = 'jumpTo'>
        </content-view>
        <transition name = 'fade'>
            <div class="content-mask"
                v-show="ifShowContent"
                @click="hideContent"
            >
            </div>
        </transition>
    </div>
</template>
<script>
import ContentView from '@/components/ContentView';
export default {
    components:{
        ContentView
    },
    props:{
        showFlag:{
            type:Boolean,
            default:false
        },
        fontSizeList:Array,
        defalutFontSize:Number,
        themeList:Array,
        defaultTheme:Number,
        bookAvailable:{
            type:Boolean,
            default:false
        },
        navigation:Object,String,
    },
    data() {
        return {
            ifSettingShow:false,
            showTag:-1,//设置的模块
            progress:0,
            ifShowContent:false,
            // lineWidth:0, // 设置字体中间的线宽度
        }
    },
    methods: {
        // 展示设置模块
        showSettingFlag:function(index){
            this.showTag = index;
            if (index === 3) {
                this.ifSettingShow = false;
                this.ifShowContent = true;
            }else{
                this.ifSettingShow = true;
            }
        },
        // 隐藏设置
        hideSettingFlag:function(){
            this.ifSettingShow = false;
        },
        // 改变字体
        setFontSize:function(fontSize){
            this.$emit('setFontSize',fontSize)
        },
        // 选择主题模式
        setTheme:function(index){
            this.$emit('setTheme',index)
        },
        // 进度条
        onProgressChange:function(progress){
            this.$emit('onProgressChange',progress)
        },
        onProgressInput:function(progress) {
            this.progress = progress
            this.$refs.progress.style.backgroundSize = `${this.progress}% 100%`
        },
        // 目录
        jumpTo(href){
            this.$emit('jumpTo',href)
        },
        showContent(){
            this.ifShowContent = true
        },
        hideContent(){
            this.ifShowContent = false
        },
    },
    mounted() {
        // 设置字体中间的线宽度
        // getLineWidth(){
        //     return this.$refs.line[0].getBoundingClientRect().width;
        // }
    },
}
</script>
<style lang="scss" scoped>
@import '../assets/styles/global';
.menu-bar{
    .menu-wrapper{
        position: absolute;
        bottom:0;
        width: 100%;
        height: px2rem(48);
        z-index: 101;
        background: #fff;
        box-shadow: 0 px2rem(-6) px2rem(6) rgba(0,0,0,.1);
        &.hide-box-shadow{
            box-shadow: none;
        }
        display: flex;
        .icon-wrapper{
            flex:1;
            @include center;
            .icon-progress{
                font-size: px2rem(24)
            }
            .icon-bright{
                font-size: px2rem(24)
            }
        }
    }
    .setting-wrapper{
        position: absolute;
        bottom:px2rem(48);
        left: 0;
        width: 100%;
        height: px2rem(60);
        z-index: 101;
        background: #fff;
        box-shadow: 0 px2rem(-6) px2rem(6) rgba(0,0,0,.1);
        .setting-font-size{
            display: flex;
            height: 100%;
            .preview{
                flex:0 0 px2rem(40);
                @include center;
            }
            .select{
                display: flex;
                flex: 1;
                .select-wrapper{
                    flex: 1;
                    display: flex;
                    @include center;
                    .line{
                        flex:1;
                        height: 0;
                        border-top: px2rem(1) solid #ccc;
                    }
                    .point-wrapper{
                        position: relative;
                        flex:0 0 0;
                        width: 0;
                        height: px2rem(5);
                        border-left: px2rem(1) solid #ccc;
                        .point{
                            position: absolute;
                            top:px2rem(-4);
                            left: px2rem(-4);
                            width: px2rem(10);
                            height: px2rem(10);
                            border-radius: 50%;
                            background: #fff;
                            border:px2rem(1) solid #ccc;
                            box-shadow: 0 px2rem(2) px2rem(5) rgba(0,0,0,.1);
                            display: flex;
                            @include center; 
                            .small-point{                           
                                width: px2rem(3);
                                height: px2rem(3);
                                border-radius: 50%;
                                background: #000;
                            }
                        }
                    }
                    &:first-child{
                        .line:first-child{
                            border-top: none;
                        }
                    } 
                    &:last-child{
                        .line:last-child{
                            border-top: none;
                        }
                    } 
                }
            }
            
        }
        .setting-themem{
            display: flex;
            height: 100%;
            .setting-theme-item{
                flex: 1;
                display: flex;
                flex-direction: column;
                padding: px2rem(8);
                box-sizing: border-box;
                @include center;
                .preview{
                    flex: 1;
                    width: 100%;
                    border:px2rem(1) solid #ccc;
                    margin-bottom: px2rem(5);
                    &.no-border{
                        border: none;
                    }
                }
                .text{
                    flex: 0 0 px2rem(20);
                    font-size: px2rem(14);
                    color:#ccc;
                    &.selected{
                        color:#333;
                    }
                }
            }
        }
        .setting-progress{
            position: relative;
            width: 100%;
            height: 100%;
            .progress-wrapper{
                width: 100%;
                height: 100%;
                @include center;
                padding: 0 px2rem(30);
                box-sizing: border-box;
                .progress{
                    width: 100%;
                    -webkit-appearance: none;
                    height: px2rem(2);
                    background: -webkit-linear-gradient(#999,#999) no-repeat,#ddd;
                    background-size: 0 100%;
                    &:focus{
                        outline: none;
                    }
                    &::-webkit-slider-thumb{ //range的小手柄
                        -webkit-appearance: none;
                        width: px2rem(20);
                        height: px2rem(20);
                        border-radius: 50%;
                        background: #fff;
                        box-shadow: 0 px2rem(1) px2rem(4) 0 rgba(0,0,0,.1);
                        border: px2rem(1) solid #ccc;
                    }
                } 
            }
            .text-wrapper{
                font-size: px2rem(14);
                @include center;
                position: relative;
                top:px2rem(-8)
            }
        }

    }

    .content-mask{
        position: absolute;
        top:0;
        left: 0;
        z-index: 101;
        display: flex;
        width: 100%;
        height: 100%;
        background: rgba(51,51,51,.8);
    }
}

</style>