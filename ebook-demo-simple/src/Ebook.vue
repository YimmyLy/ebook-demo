<template>
    <div class = 'ebook'>
        <!-- 标题状态栏 -->
        <title-bar :showFlag = 'showFlag'></title-bar>
        <div class="read-wrapper">
            <div id="read-content"></div>
            <div class = 'mask'>
                <div class="left" @click="prevPage"></div>
                <div class="center" @click="toggleShow"></div>
                <div class="right" @click="nextPage"></div>
            </div>
        </div>
        <!-- 底部菜单栏 -->
        <menu-bar 
            :showFlag = 'showFlag' 
            :defalutFontSize = 'defalutFontSize' 
            :fontSizeList = 'fontSizeList'
            @setFontSize = 'setFontSize'
            :themeList = 'themeList'
            :defaultTheme = 'defaultTheme'
            @setTheme = 'setTheme'
            :bookAvailable = 'bookAvailable'
            @onProgressChange = 'onProgressChange'
            :navigation = 'navigation'
            @jumpTo = 'jumpTo'
            ref="menuBar" 
        ></menu-bar>
    </div>
</template>

<script>
import TitleBar from '@/components/TitleBar';
import MenuBar from '@/components/MenuBar';
import Epub from 'epubjs';
const DOWNLOAD_URL = '/static/1543T6109-2607.epub';

global.ePub = Epub;

export default {
    components:{TitleBar,MenuBar},
    data() {
        return {
            showFlag: false,
            defalutFontSize:16,//默认选中字体
            fontSizeList:[
                {fontSize:12},
                {fontSize:14},
                {fontSize:16},
                {fontSize:18},
                {fontSize:20},
                {fontSize:22},
                {fontSize:24},
            ],
            defaultTheme:0,
            themeList:[ //主题列表
                {
                    name:'default',
                    style:{
                        body:{
                            'color':'#000',
                            'background':'#fff',
                        }
                    }
                },
                {
                    name:'eye',
                    style:{
                        body:{
                            'color':'#000',
                            'background':'#ceeaba',
                        }
                    }
                },
                {
                    name:'night',
                    style:{
                        body:{
                            'color':'#fff',
                            'background':'#000',
                        }
                    }
                },
                {
                    name:'gold',
                    style:{
                        body:{
                            'color':'#000',
                            'background':'rgb(241,236,266)',
                        }
                    }
                }
            ],
            bookAvailable:false, // 图书的可用状态
        }
    },
    methods: {
        // 切换标题栏和menu显示/隐藏
        toggleShow:function(){
            this.showFlag = !this.showFlag;
            if (!this.showFlag) {  //menu栏隐藏时 设置也隐藏
                this.$refs.menuBar.hideSettingFlag();
            }
        },
        // 电子书的解析和渲染
        showEpub:function(){
            // 生成book对象
            this.book = new Epub(DOWNLOAD_URL);
            console.log(this.book)
            // 生成rendition对象  通过book.renderTo
            this.rendition = this.book.renderTo('read-content',{
                width: window.innerWidth,
                height: window.innerHeight
            })
            // 通过rendition.display渲染电子书
            this.rendition.display();
            // 设置themt主题
            this.theme = this.rendition.themes;
            // 设置默认字体d大小
            this.setFontSize(this.defalutFontSize);

            // 注册主题
            // this.theme.register(name,styles);  主题名称 样式
            // this.theme.select(name)
            this.registerTheme();
            this.setTheme(this.defaultTheme)

            // 获取location对象 费性能 默认不获取
            console.log(this.book.locations); 
            // 通过epubjs的钩子函数
            this.book.ready.then(() => {
                this.navigation = this.book.navigation; //获取目录
                return this.book.locations.generate(); //获取进度条
            }).then(result => {
                this.locations = this.book.locations;
                this.bookAvailable = true;
            })
        },
        // 上一页
        prevPage:function(){
            if (this.rendition) {
                this.rendition.prev();
            }
        },
        // 下一页
        nextPage:function(){
            if (this.rendition) {
                this.rendition.next();
            }
        },
        // 改变字体
        setFontSize:function(fontSize){
            this.defalutFontSize = fontSize;
            if (this.theme) {
                this.theme.fontSize(fontSize + 'px');
            }
        },
        // 注册主题
        registerTheme:function(){
            this.themeList.forEach(theme => {
                this.theme.register(theme.name,theme.style)
            })
        },
        // 设置当前主题
        setTheme:function(index){
            this.theme.select(this.themeList[index].name);
            this.defaultTheme = index;
        },
        // 进度条
        onProgressChange(progress){
            const percentage = progress / 100;
            // this.locations.cfiFromPercentage(percentage) 自动生成location定位到percentage位置的
            const location = percentage > 0 ? this.locations.cfiFromPercentage(percentage) : 0;
            this.rendition.display(location);
        },
        // 目录 根据链接跳转到指定的位置
        jumpTo(href){
            this.rendition.display(href);
            this.hideTitleAndMenu(); //隐藏标题菜单栏
        },
        hideTitleAndMenu:function(){
            // 隐藏标题栏和菜单栏
            this.showFlag = false;
            // 隐藏菜单栏弹出的设置栏
            this.$refs.menuBar.hideSettingFlag();
            // 隐藏目录
            this.$refs.menuBar.hideContent()

        },

    },
    mounted() {
        this.showEpub()
    },
}
</script>
<style lang="scss" scoped>
    @import 'assets/styles/global';
    .ebook{
        position: relative;
        .read-wrapper{
            .mask{
                position: absolute;
                top:0;
                left: 0;
                width: 100%;
                height: 100%;
                z-index: 100;
                display: flex;
                .left{
                    flex: 0 0 px2rem(100)
                }
                .center{
                    flex: 1;
                }
                .right{
                    flex: 0 0 px2rem(100)
                }
            }
        }
    }
</style>

