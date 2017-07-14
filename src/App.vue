<template>
  <div id="app">
    <div id="app">
      <div class="lazyLoadImg" style="display:none">
        <img v-for="item in bgIndex" :src="'http://osww7m2bf.bkt.clouddn.com/'+item+'.jpg'" alt="">
      </div>
      <div class="showTab" :class="{active:showTab}" @click="toggleTab">
        +
      </div>
      <div class="body" :class="{bgMoveOut:showTab}" :style="{backgroundImage:'url('+this.bgUrl+')',backgroundRepeat:'no-repeat'}">
        <div class="input">
          <div class="innerinput">
            <img src="./assets/google.png" alt="" width="30" height="30">
            <input type="text" placeholder="Google" v-model="text" @keyup.enter="submit">
            <a class="search" v-bind:href="'https://www.google.com.hk/search?q='+this.text" target="_blank">
              <i class="iconfont icon-sousuo"></i>
            </a>
          </div>
        </div>
      </div>
      <transition name="slideTab">
        <div class="tab" v-show="showTab">
          <div class="select">
            <span @click="setLishi" :class="{active:showLishi}"><i class="iconfont icon-lishi"></i></span>
            <span @click="setShuqian" :class="{active:!showLishi}"><i class="iconfont icon-bookmarks"></i></span>
          </div>
          <div class="tab-body">
            <div class="lishi-body" v-show="showLishi"></div>
            <div class="shuqian-body" v-show="!showLishi">
              <ul class="bookmarks">
                <li v-for="list in lists" class="list">
                  <a :href="list.url" target="_blank" :style="{color:textColor[Math.floor(Math.random()*textColor.length)]}">
                    <span class="title" :alt="list.url">{{list.title}}</span>
                  </a>
                  <span class="close" @click.stop="removeShuqian(list.id)">X</span>
                </li>
              </ul>
            </div>
          </div>
        </div>
      </transition>
      <div class="fengche">
        <img src="./assets/fengche.png" alt="" width="40" height="40" @click="newBgIndex" ref="fengche">
        <div class="fengchegan"></div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      text: '',
      showTab: false,
      currentBgIndex: 0,
      bgIndex: [44524, 67664, 74674, 67466, 45454, 75676, 76546, 65766, 42344, 87665, 87566],
      lists: [],
      showLishi: false,
      textColor: ['#70DB93', '#5C3317', '#9F5F9F', '#B5A642', '#D9D919', '#A67D3D', '#8C7853', '#5F9F9F', '#D98719', '#B87333', '#42426F', '#871F78', '#856363']
    }
  },
  computed: {
    bgUrl () {
      return 'http://osww7m2bf.bkt.clouddn.com/' + this.bgIndex[this.currentBgIndex] + '.jpg'
    }
  },
  methods: {
    toggleTab () {
      this.showTab = !this.showTab
    },
    newBgIndex () {
      this.currentBgIndex = Math.floor(Math.random() * this.bgIndex.length)
      this.$refs.fengche.className = 'active'
      var fengche = this.$refs.fengche
      setTimeout(function () {
        fengche.className = ''
      }, 500)
    },
    submit () {
      window.open('https://www.google.com.hk/search?q=' + this.text)
    },
    toggleLishi () {
      this.showLishi = !this.showLishi
    },
    processNode (node) {
      if (node.children) {
        node.children.forEach((item) => {
          this.processNode(item)
        })
      }
      if (node.url) {
        this.lists.push({
          'title': node.title,
          'url': node.url,
          'icon': 'content: -webkit-image-set(url("chrome://favicon/size/16@1x/' + node.url + '") 1x, url("chrome://favicon/size/16@2x/' + node.url + '") 2x);',
          'id': node.id
        })
      }
    },
    setLishi () {
      this.showLishi = true
    },
    setShuqian () {
      this.showLishi = false
    },
    removeShuqian (id) {
      this.lists.forEach((list, index) => {
        if (list.id === id) {
          console.log(index, this.lists)
          this.lists.splice(index, 1)
        }
      })
      chrome.bookmarks.remove(id, () => {
        alert('书签删除成功！')
      })
    }
  },
  mounted () {
    chrome.bookmarks.getTree((lists) => {
      lists.forEach((list) => {
        this.processNode(list)
      })
    })
  }
}
</script>
<style>
#app {
  position: relative;
  width: 100%;
  height: 100%;
  display: flex;
}

#bg {
  width: 100%;
  height: 100%;
  position: fixed;
  filter: blur(4px);
  z-index: -1000;
}

.showTab {
  position: absolute;
  z-index: 10000;
  right: 20px;
  top: 20px;
  width: 40px;
  height: 40px;
  background: #E74C3C;
  color: #fff;
  text-align: center;
  line-height: 38px;
  border-radius: 50%;
  font-size: 22px;
  box-shadow: #000 0 0 5px;
  cursor: pointer;
  transition: all 0.5s cubic-bezier(.77, .14, .16, .78);
}

.body {
  flex: 0 0 100%;
  padding: 100px 0;
  flex-direction: column;
  justify-content: space-around;
  transition: all 0.5s cubic-bezier(.77, .14, .16, .78);
  transform: translateX(0);
  background-size: cover;
}

.body .input .innerinput {
  position: relative;
  top: 10%;
  width: 40%;
  height: 34px;
  margin: 0 auto;
}

.body .input .innerinput img {
  position: absolute;
  left: 10px;
  top: 2px;
  z-index: 10;
}

.body .input input {
  padding: 0 50px;
  width: 100%;
  height: 34px;
  border: none;
  border-radius: 16px;
}

.body .input input:focus {
  outline: none;
}

.body .input .search {
  display: inline-block;
  position: absolute;
  right: 0;
  top: 0;
  width: 60px;
  height: 100%;
  border-radius: 0 17px 17px 0;
  background: #2ECC71;
  color: #fff;
  text-align: center;
  line-height: 34px;
  font-size: 14px;
  text-decoration: none;
}

.tab {
  flex: 0 0 316px;
  width: 316px;
  position: relative;
  background: #fff;
  transition: all 0.5s cubic-bezier(.77, .14, .16, .78);
  transform: translateX(-300px)
}

.tab .select {
  display: flex;
  width: 100%;
  height: 50px;
  background: #2CCE71;
}
.tab .select span.active{
  color: red;
}

.tab .select span {
  display: inline-block;
  flex: 0 0 33%;
  color: #fff;
  line-height: 50px;
  text-align: center;
  cursor: pointer;
}
.tab .select span i{
  font-size: 20px;
}
.tab .select span:hover{
  color: red;
}
.slideTab-enter-to,
.slideTab-leave {
  transform: translateX(-300px)
}

.slideTab-enter,
.slideTab-leave-to {
  transform: translateX(0)
}
.tab-body{
  width: 99%;
  height: 100%;
  overflow: auto;
}
.tab-body .shuqian-body .bookmarks{
  width: 92%;
  padding: 15px 0;
}
.tab-body .shuqian-body .bookmarks .list{
  position: relative;
  padding-left: 20px;
  width: 100%;
  height: 30px;
}
.tab-body .shuqian-body .bookmarks .list a{
  display: inline-block;
  width: 100%;
  height: 100%;
  text-decoration: none;
  font-size: 12px;
  color: #000;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}
.tab-body .shuqian-body .bookmarks .list a span{
  display: inline-block;
  height: 100%;
  line-height: 30px;
}
.tab-body .shuqian-body .bookmarks .list span.close{
  display: none;
  position: absolute;
  right: 5px;
  top: 5px;
  width: 20px;
  height: 20px;
  transition: all 0.5s linear;
  background: #DD0000;
  color: #fff;
  text-align: center;
  line-height: 20px;
  border-radius: 50%;
  cursor: pointer;
}
.tab-body .shuqian-body .bookmarks .list:hover span.close{
  display: inline-block;
}
.bgMoveOut {
  transform: translateX(-180px);
}

.showTab.active {
  right: 10px;
  top: 5px;
  transform: rotate(405deg) scale(0.8);
}

.fengche {
  position: absolute;
  bottom: 0;
  right: 50px;
  width: 40px;
  height: 65px;
  cursor: pointer;
}

.fengche img.active {
  transition: all 0.5s cubic-bezier(.77, .14, .16, .78);
  transform: rotate(360deg);
}

.fengche img {
  position: absolute;
  top: 0;
  z-index: 3;
}

.fengche .fengchegan {
  position: absolute;
  bottom: 0;
  right: 15px;
  width: 5px;
  height: 40px;
  background: #AAA;
  z-index: 2;
}
</style>
