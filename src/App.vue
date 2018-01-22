<template>
  <div id="app">
    <transition-group name="fade" tag="ul">
        <li class="postit" :key="pId" v-for="(p, pId) in postits" :style="postitCss(p)" @mousedown="selectId($event, pId)" @touchstart="selectId($event,pId)">
          <textarea name="content" class="text" :style="fontColor(p)" v-model="p.text"></textarea>
          <div class="controlBar">
            <div class="colorBlock" v-for="color in colorList" @click="p.color = color.name" :style="{backgroundColor:color.bgColor}"></div>
            <div class="delete" @click="deletePostit(pId)">Delete</div>
        </div>
      </li>
    </transition-group>
    <div class="addButton" @click="addPostit">
      <span class="icon"></span>
      <span class="text">Add Postit</span>
    </div>
  </div>
</template>

<script>

export default {
  name: 'App',
  data() {
    return {
      colorList:[
        {
          name: 'yellow',
          bgColor: '#FCE3B3',
          color: '#E27687'
        },
        {
          name: 'pink',
          bgColor: '#E27687',
          color: '#FCE3B3'
        },
        {
          name: 'blue',
          bgColor: '#81B3DA',
          color: '#F7F7F7'
        },
        {
          name: 'green',
          bgColor: '#63A7A4',
          color: '#FCE3B3'
        }
      ],
      postits:[
        {
          text: '待辦事項',
          color: 'pink',
          bgColor: '',
          pos: {x: 180, y: 20}
        },
        {
          text: '待辦事項',
          color: 'yellow',
          bgColor: '',
          pos: {x: 600, y: 100}
        }
      ],
      nowId: -1,
      startPos: {
        x:0,
        y:0
      }
    }
  },
  methods: {
    postitCss(p) {
      let postitColor=p.color;
      return {
        left: p.pos.x+'px',
        top: p.pos.y+'px',
      }
    },
    fontColor(p) {
      let postitColor=p.color;
      function fontSize(textLength) {
        let fontSize;
        if (textLength <= 2) {
          fontSize = 60;
        } else if (textLength>2 && textLength<=10){
          fontSize = 40;
        } else {
          fontSize = 30;
        }
        return fontSize+'px';
      }
      return {
        fontSize: fontSize(p.text.length),
        color: this.colorList.find(i => i.name==postitColor).color,
        backgroundColor: this.colorList.find(i => i.name==postitColor).bgColor
      }
    },
    selectId(e, id) {
      let isBlock = e.srcElement.classList.contains('colorBlock');
      let isBtn = e.srcElement.classList.contains('delete');
      this.startPos={x: e.offsetX, y:e.offsetY};
      if (isBlock || isBtn) {
        this.nowId = -1;
        // console.log(this.nowId);
      } else {
        this.nowId = id;
        // console.log(this.nowId);
      }
      
    },
    posMove(e) {
      if (this.nowId != -1) {
        this.postits[this.nowId].pos.x = e.pageX - this.startPos.x;
        this.postits[this.nowId].pos.y = e.pageY - this.startPos.y;
      }
    },
    notSelected() {
      this.nowId = -1;
    },
    addPostit() {
      const colorRandom = Math.floor(Math.random() * Math.floor(4));
      this.postits.push(
        {
          text: '文字',
          color: this.colorList[colorRandom].name,
          bgColor: '',
          pos: {x: 200+Math.random()*200, y: 200+Math.random()*200}
        }
      )
    },
    deletePostit(id) {

      this.postits.splice(id, 1);
    }
  },
  created() {
    window.addEventListener('mousemove', this.posMove);
    window.addEventListener('mouseup', this.notSelected);
  },
  destroyed() {
    window.removeEventListener('mousemove', this.posMove);
    window.removeEventListener('mouseup', this.notSelected);
  }
}
</script>

<style lang="scss">
@import url('//fonts.googleapis.com/earlyaccess/notosanstc.css');
$colorDeepBlue: #44557E;
$colorRed: #E27687;
$colorYellow: #FCE3B3;
$colorBlue: #81B3DA;
$colorGreen: #63A7A4;
$colorPink: #F4C2CB;
$colorWhite: #F7F7F7;
@mixin size($w, $h:$w) {
  width: $w;
  height: $h;
}
// *, *:before, *:after {
//   border:1px solid black;
// }
html, body {
  margin: 0;
  padding: 0;
  background-color: $colorDeepBlue;
}
#app {
  font-family: 'Noto Sans TC', '微軟正黑體';
}
.postit {
  @include size(240px, 280px);
  position: absolute;
  background-color: transparent;
  color: $colorRed;
  display: flex;
  textarea {
    cursor: default;
    height: 240px;
    max-height: 240px;
    width: 240px;
    border: none;
    background-color: $colorGreen;
    box-shadow: 10px 8px 30px rgba(black, .15);
    font-size: 20px;
    resize: none;
    text-align: center;
    overflow: auto;
    font-weight: 500;
    letter-spacing: .1em;
    line-height: 1.3em;
    transition: background-color .5s ;
    &:focus {
      outline: none;
    }
  }
  &:hover, &:focus {
    .controlBar {
      opacity:1;
    }
  }
}
.controlBar {
  opacity:0;
  position: absolute;
  bottom: 0px;
  transition: .3s;
  .colorBlock {
    cursor: pointer;
    display: inline-block;
    @include size(20px);
    margin-right: 10px;
    vertical-align:bottom;
    transition: .3s;
  }
  .delete {
    display:inline-block;
    float: right;
    color: $colorPink;
    border: 1px solid $colorPink;
    border-radius: 3px;
    font-size: 13px;
    padding: 2px 7px;
    margin-left: 63px;
    cursor: pointer;
    transition: .3s;
    &:hover {
      background-color: $colorPink;
      color: $colorDeepBlue;
    }
  }
}
.addButton {
  position: fixed;
  bottom: 40px;
  right:40px;
  color: $colorPink;
  border: 1px solid $colorPink;
  font-size: 18px;
  padding: 8px 10px;
  border-radius:3px;
  cursor: pointer;
  background-color: transparent;
  transition: .3s;
  .icon {
    display: inline-block;
    @include size(25px);
    border-radius: 50%;
    background-color: $colorPink;
    vertical-align: middle;
    margin-right: 10px;
    position:relative;
    bottom: 2px;
    &:before {
      position:absolute;
      color: $colorDeepBlue;
      content:'+';
      font-size: 24px;
      left: 48%;
      top:48%;
      transform: translate(-50%, -50%);
    }
  }
  .text {
    display: inline-block;
  }
  &:hover {
    color: $colorDeepBlue;
    background-color: rgba($colorPink, .8);
    border-color: rgba($colorPink, .3);
    .icon {
      background-color: $colorDeepBlue;
      &:before {
        color: $colorPink;
      }
    }
  }
}

.fade-enter-active {
  transition: all .5s ease;
}
.fade-leave-active {
  transition: all .3s ease;
}
.fade-enter, .fade-leave-to {
  transform: scale(.1);
  opacity: 0;
}
</style>
