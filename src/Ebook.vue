<template>
  <div class="ebook">
    <title-bar :isTitleAndMenuShow="isTitleAndMenuShow"></title-bar>
    <div class="read-wrapper">
      <div id="read"></div>
      <div class="mask">
        <div class="left" @click="prevPage"></div>
        <div class="center" @click="toggleTitleAndMenu"></div>
        <div class="right" @click="nextPage"></div>
      </div>
    </div>
    <menu-bar
      :isTitleAndMenuShow="isTitleAndMenuShow"
      ref="menuBar"
      :fontSizeList="fontSizeList"
      :defaultFontSize="defaultFontSize"
      @setFontSize="setFontSize"
      :themeList="themeList"
      :defaultTheme="defaultTheme"
      @setTheme="setTheme"
      :bookAvailable="bookAvailable"
      @onprogressChange="onProgressChange"
      @jumpTo="jumpTo"
      :navigation="navigation"
    ></menu-bar>
  </div>
</template>
<script>
import TitleBar from "./components/TitleBar";
import MenuBar from "./components/MenuBar";
import Epub from "epubjs";
const DOWNLOAD_URL = "/static/2020_Book_ProgrammingPersistentMemory.epub";
export default {
  data() {
    return {
      isTitleAndMenuShow: false,
      fontSizeList: [
        { fontSize: 12 },
        { fontSize: 14 },
        { fontSize: 16 },
        { fontSize: 18 },
        { fontSize: 20 },
        { fontSize: 22 },
        { fontSize: 24 }
      ],
      defaultFontSize: 16,
      themeList: [
        {
          name: "default",
          style: {
            body: {
              color: "#000",
              background: "#fff"
            }
          }
        },
        {
          name: "eye",
          style: {
            body: {
              color: "#000",
              background: "#ceeaba"
            }
          }
        },
        {
          name: "night",
          style: {
            body: {
              color: "#fff",
              background: "#000"
            }
          }
        },
        {
          name: "gold",
          style: {
            body: {
              color: "#000",
              background: "rgb(241, 236, 226)"
            }
          }
        }
      ],
      defaultTheme: 0,
      // 图书是否处于可用状态
      bookAvailable: false,
      navigation: {}
    };
  },
  components: {
    TitleBar,
    MenuBar
  },
  methods: {
    // 用于电子书的解析和渲染
    showEpub() {
      //生成book对象
      this.book = new Epub(DOWNLOAD_URL);
      //   console.log(this.book);
      //生成Rendition
      this.rendition = this.book.renderTo("read", {
        width: window.innerWidth,
        height: window.innerHeight
        // method: "default"
      });
      //通过Rendition.display渲染电子书
      this.rendition.display();
      // 需要改变字体需要先获得Themes
      this.themes = this.rendition.themes;
      // 实现主题切换功能，使用Themes提供的register方法进行主题注册
      // 然后使用select方法进行主题切换
      this.registerTheme();
      this.setTheme(this.defaultTheme);
      // 获取locations对象
      // 使用epubjs的钩子函数 -- 解析需要时间
      this.book.ready
        .then(() => {
          return this.book.locations.generate();
        })
        .then(result => {
          this.navigation = this.book.navigation;
          //   console.log(this.navigation);
          this.locations = this.book.locations;
          //   console.log("---图书解析完成----");
          // 图书解析完毕，将可用状态设为true
          this.bookAvailable = true;
        });
    },

    prevPage() {
      if (this.rendition) {
        this.rendition.prev();
      }
    },
    nextPage() {
      if (this.rendition) {
        this.rendition.next();
      }
    },
    toggleTitleAndMenu() {
      this.isTitleAndMenuShow = !this.isTitleAndMenuShow;
      if (!this.isTitleAndMenuShow) {
        this.$refs.menuBar.hideSetting();
      }
    },
    setFontSize(fontSize) {
      this.defaultFontSize = fontSize;
      this.themes.fontSize(fontSize + "px");
    },
    registerTheme() {
      this.themeList.forEach(theme => {
        this.themes.register(theme.name, theme.style);
      });
    },
    setTheme(index) {
      this.themes.select(this.themeList[index].name);
      this.defaultTheme = index;
    },
    // 进度条发生改变 （0-100）
    onProgressChange(progress) {
      const percentage = progress / 100;
      const location =
        percentage > 0 ? this.locations.cfiFromPercentage(percentage) : 0;
      this.rendition.display(location);
    },
    jumpTo(href) {
      this.rendition.display(href);
      this.hideTitleAndMenu();
    },
    hideTitleAndMenu() {
      // 隐藏标题栏和菜单栏
      this.isTitleAndMenuShow = false;
      // 隐藏菜单栏弹出的菜单
      this.$refs.menuBar.hideSetting();
      // 隐藏目录
      this.$refs.menuBar.hideContent();
    }
  },
  mounted() {
    this.showEpub();
  }
};
</script>
<style lang="scss" scoped>
@import "assets/styles/global";
.ebook {
  position: relative;
  .read-wrapper {
    .mask {
      position: absolute;
      top: 0;
      left: 0;
      display: flex;
      z-index: 100;
      width: 100%;
      height: 100%;
      .left {
        flex: 0 0 px2rem(100);
      }
      .center {
        flex: 1;
      }
      .right {
        flex: 0 0 px2rem(100);
      }
    }
  }
}
</style>