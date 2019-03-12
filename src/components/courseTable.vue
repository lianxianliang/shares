<template>
  <div
    class="box"
    v-cloak
  >
    <header>
      <div class="item">&nbsp;</div>
      <div
        v-for="item in 7"
        :key="item"
        class="item"
        :class="{curDay:item==day}"
      >{{week[item-1]}}</div>
    </header>
    <div class="articleBox">
      <article
        v-for="item in 10"
        :key="item"
      >
        <div class="item"><span>{{item}}</span></div>
        <div
          v-for="el in 7"
          :key="el"
          class="item"
        >&nbsp;</div>
      </article>

      <!-- 今天周几 -->
      <div
        class="curWeek"
        :style="{left:left}"
      >&nbsp;</div>

      <!-- 课程 -->
      <div
        class="lesson"
        v-for="(item,idx) in course"
        :key="'xxx'+idx"
        :style="{width:item.width,height:item.height,top:item.top,left:item.left,background:color[idx]}"
        @click.stop="handleClick(item)"
      >
        <div class='le'>{{item.course}}</div>
        <div class='le'>{{item.teacherName}}</div>
        <div class='le'>{{item.address}}</div>
        <div class='le'>{{item.week}}</div>
      </div>

    </div>

    <div
      class="cover"
      v-show="isActive"
      @click="isActive=false"
      v-cloak
    ></div>
    <div
      class="modal"
      v-show="isActive"
      v-cloak
    >
      <div class='mo'>{{cur.course}}</div>
      <div class='mo'>{{cur.teacherName}}</div>
      <div class='mo'>{{cur.address}}</div>
      <div class='mo'>{{cur.week}}</div>
    </div>
  </div>
</template>


<script>
import courseData from '@/assets/data.js';
export default {
  data() {

    let left = new Date().getDay();
    if (left === 0) {
      left = 7;
    }
    let day = left;
    left = left * 12.5 + '%';

    return {
      isActive: false,
      cur: {},
      left,
      day,
      courseData,
      week: ['一', '二', '三', '四', '五', '六', '日'],
      color: ['#39D0A7', '#ACD43F', '#B997EB', '#EE836E', '#FDAA42', '#FC8796', '#718AE6', '#FFD23F']
    }
  },

  computed: {
    course: function (params) {
      let arr = [];
      this.courseData.forEach((array, idx) => {
        array.forEach(element => {
          let temp = element.lesson.split('-');
          let o = {
            left: (idx + 1) * 12.5 + '%',
            top: (temp[0] - 1) * 10 + '%',
            height: (temp[1] - temp[0] + 1) * 10 + '%',
            width: '12.5%'
          }
          let obj = {
            ...element,
            ...o
          }
          arr.push(obj);
        });
      });
      return arr;
    }
  },

  methods: {
    handleClick(item) {
      let obj = {
        ...this.cur,
        ...item
      }
      this.cur = obj;
      this.isActive = true;
    }
  },
}
</script>

<style lang="scss">
[v-cloak] {
  display: none;
}
.box {
  display: flex;
  flex-direction: column;
  height: 100vh;
  position: relative;
}
.item {
  border-right: 1px solid #ccc;
  &:last-of-type {
    border-right: none;
  }
}
header {
  display: flex;
  height: 40px;
  line-height: 40px;
  .item {
    flex: 1;
    &.curDay {
      font-size: 24px;
      color: #11aff1;
      text-shadow: 2px 3px 5px #11aff1;
    }
  }
}
.articleBox {
  display: flex;
  flex-direction: column;
  height: calc(100% - 40px);
  position: relative;
}
.curWeek {
  position: absolute;
  top: 0;
  width: 12.5%;
  height: 100%;
  background: linear-gradient(#fff, #ffc0cb);
}
article {
  display: flex;
  flex: 1;
  .item {
    border-top: 1px solid #ccc;
    flex: 1;
    align-items: center;
    justify-content: center;
    display: flex;
  }
}
.lesson {
  position: absolute;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  color: #fff;
  .le {
    font-size: 12px;
    overflow: hidden;
    width: 100%;
    white-space: nowrap;
    text-overflow: ellipsis;
    line-height: 18px;
  }
}
.cover {
  position: fixed;
  width: 100%;
  height: 100%;
  left: 0;
  top: 0;
  background: rgba(0, 0, 0, 0.3);
}
.modal {
  width: 80%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  color: #fff;
  line-height: 32px;
  padding: 20px;
  position: fixed;
  background: #000;
  left: 50%;
  top: 35%;
  margin-left: -45%;
  border-radius: 5px;
}
</style>

