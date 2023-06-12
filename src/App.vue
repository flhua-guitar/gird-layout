<template>
  <div id="app">
    <!--  :layout.sync="layout"    //栅格的初始布局数据源。值必须为 `Array`，其数据项为 `Object`。 每条数据项必须有 `i, x, y, w 和 h` 属性。
            :col-num="12"            //定义栅格系统的列数，其值需为自然数。
            :row-height="30"         //每行的高度，单位像素。
            :is-draggable="true"     //标识栅格中的元素是否可拖拽。
            :is-resizable="true"     //标识栅格中的元素是否可调整大小。
            :is-mirrored="false"     //标识栅格中的元素是否可镜像反转。 (个人感觉镜像反转在实际应用中并不会常用)
            :vertical-compact="true" //标识布局是否垂直压缩。即行内垂直方向上顶端对齐
            :margin="[10, 10]"       //每个栅格之间的间距
            :use-css-transforms="true">   //标识是否使用CSS属性 `transition-property: transform;`
     -->
    <div class="header">
      <div class="headerBox">
        <button v-if="!isDraggable" @click="edit()">编辑</button>
        <button v-else="!isDraggable" @click="saveEdit()">保存</button>
      </div>
      <div>
        <button @click="toggleFullScreen()">全屏</button>
      </div>
    </div>

    <div class="views">
      <div class="viewsContainer">
        <div class="left">
          <div class="leftBox">
            <div class="leftItemBox">
              <div
                class="leftItem"
                v-for="item in leftItemsData"
                :key="item.id"
                draggable="true"
                @mousedown="getLeftItem(item)"
                @dblclick="dialogVisible = true"
              >
                {{ item.title }}
              </div>
            </div>
          </div>
          <!-- 弹窗 -->

          <el-dialog
            title="提示"
            :visible.sync="dialogVisible"
            width="30%"
            :before-close="handleClose"
          >
            <!-- <span>这是一段信息</span> -->
            <el-select v-model="value" placeholder="请选择">
              <el-option
                v-for="item in options"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              >
              </el-option>
            </el-select>

            <span slot="footer" class="dialog-footer">
              <el-button @click="dialogVisible = false">取 消</el-button>
              <el-button type="primary" @click="submit()">确 定</el-button>
            </span>
          </el-dialog>

          <!-- 弹窗 end -->
        </div>
        <div class="addview" ref="addview">
          <grid-layout
            :layout.sync="this.layout"
            :col-num="20"
            :row-height="60"
            :is-draggable="isDraggable"
            :is-resizable="isResizable"
            :is-mirrored="false"
            :vertical-compact="true"
            :margin="[10, 10]"
            :use-css-transforms="true"
            @layout-created="layoutCreatedEvent"
            @layout-before-mount="layoutBeforeMountEvent"
            @layout-mounted="layoutMountedEvent"
          >
            <grid-item
              :class="
                isDraggable == false
                  ? 'vue-grid-item'
                  : 'vue-grid-item vue-grid-item-edit'
              "
              v-for="item in this.layout"
              :x="item.x"
              :y="item.y"
              :w="item.w"
              :h="item.h"
              :i="item.i"
              :key="item.i"
              @resized="resizedEvent"
              id="gridItem"
            >
              <button v-show="isDraggable" @click="removeItem(item.i)">
                关闭
              </button>
              <div class="g2-1" v-if="item.index == 'g2-1'" ref="g2-1">
                <HelloWorld ref="g20" :msg="activeObject" />
              </div>
              <div class="g2-2" v-if="item.index == 'g2-2'" ref="g2-2">
                <HelloWorldA ref="g21" :msg="activeObject" />
              </div>
              <div class="g2-3" v-if="item.index == 'g2-3'" ref="g2-3">
                <HelloWorldB ref="g22" :msg="activeObject" />
              </div>
              <div class="g2-4" v-if="item.index == 'g2-4'" ref="g2-4">
                <testA ref="g23"></testA>
              </div>
              <div class="g2-5" v-if="item.index == 'g2-5'" ref="g2-5">
                <testB ref="g24"></testB>
              </div>
            </grid-item>
          </grid-layout>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld.vue";
import HelloWorldA from "./components/HelloWorldA.vue";
import HelloWorldB from "./components/HelloWorldB.vue";
import testA from "./components/testA.vue";
import testB from "./components/testB.vue";

import { GridLayout, GridItem } from "vue-grid-layout";
import screenfull from "screenfull";

export default {
  name: "App",
  components: {
    HelloWorld,
    HelloWorldA,
    HelloWorldB,
    testA,
    testB,
    GridLayout: GridLayout,
    GridItem: GridItem,
  },
  data() {
    return {
      // 选择数据 --- start

      options: [
        {
          value: "0",
          label: "柱状统计数据",
        },
        {
          value: "1",
          label: "论坛统计数据",
        },
        {
          value: "2",
          label: "温度数据",
        },
      ],
      value: "",

      // 选择数据 --- end

      dialogVisible: false, // 是否隐藏弹窗
      flag1: true, // 是否已显示柱状图  true为未显示
      flag2: true, // 是否已显示饼形图  true为未显示
      flag3: true, // 是否已显示线形图  true为未显示
      isDraggable: false, // 是否可拖拽
      isResizable: false, // 是否可缩放
      activeObject: {
        // 正在调整的方格item的宽高
        width: 400,
        height: 400,
      },
      leftItemsData: [
        // 左侧模块数据
        {
          id: 0,
          title: "柱状图",
          add: [
            {
              x: 0,
              y: 0,
              w: 6,
              h: 8,
              i: 0,
              index: "g2-1",
              isShow: false,
              flag: true,
            },
          ],
        },
        {
          id: 1,
          title: "饼形图",
          add: [
            {
              x: 6,
              y: 0,
              w: 6,
              h: 8,
              i: 1,
              index: "g2-2",
              flag: true,
            },
          ],
        },
        {
          id: 2,
          title: "线形图",
          add: [
            {
              x: 8,
              y: 0,
              w: 6,
              h: 8,
              i: 2,
              index: "g2-3",
              flag: true,
            },
          ],
        },
        {
          id: 3,
          title: "testA",
          add: [
            {
              x: 14,
              y: 0,
              w: 6,
              h: 8,
              i: 3,
              index: "g2-4",
              flag: true,
            },
          ],
        },
        {
          id: 4,
          title: "testB",
          add: [
            {
              x: 0,
              y: 12,
              w: 6,
              h: 8,
              i: 4,
              index: "g2-5",
              flag: true,
            },
          ],
        },
      ],
      layout: [
        // { x: 3, y: 0, w: 3, h: 7, i: "1", index: "g2-2" },
        // { x: 5, y: 0, w: 4, h: 6, i: "2", index: "g2-3" },
      ],
    };
  },
  mounted() {
    console.log("g2-2", this.$refs["g20"][0].clientHeight);
  },

  methods: {
    //自定义容器初始化
    initDesign() {
      let domstyleWidth =
          document.getElementById("grid-container").offsetWidth - 12 * 10,
        domstyleHeight =
          document.getElementById("grid-container").offsetHeight / 8,
        domContainer = document.getElementById("custom-design"),
        resWidth = domstyleWidth / 12,
        everyWidth = ((resWidth / domstyleWidth) * 100).toFixed(2);
      this.bjStyles = {
        right: "8px",
        background:
          "linear-gradient(rgba(241, 243, 242, 1) 10px, transparent 0px) 0% 0%," +
          "linear-gradient(to right, rgba(241, 243, 242, 1) 10px, transparent 0px) rgba(223, 232, 228, 1)",
        "background-size": `${everyWidth}% ${domstyleHeight}px`,
      };
      this.rowheight = domstyleHeight - 10;
      this.designLeft = domContainer.offsetLeft + 250;
      this.designTop = domContainer.offsetTop + 70;
    },
    // 对应Vue生命周期的created
    layoutCreatedEvent(newLayout) {
      console.log("Created layout: ", newLayout);
    },
    // 对应Vue生命周期的beforeMount
    layoutBeforeMountEvent(newLayout) {
      console.log("beforeMount layout: ", newLayout);
    },
    // 对应Vue生命周期的mounted
    layoutMountedEvent(newLayout) {
      console.log("Mounted layout: ", newLayout);
    },
    // 调整大小后的事件
    resizedEvent(i, newH, newW, newHPx, newWPx) {
      this.activeObject.height = newHPx;
      this.activeObject.width = newWPx;
      if (i == 0) {
        this.$refs.g20[0].changeG2();
      } else if (i == 1) {
        this.$refs.g21[0].changeG2();
      } else if (i == 2) {
        this.$refs.g22[0].changeG2();
      }

      // this.$refs.g20[0].forceFit();

      console.log(
        "RESIZE i=" +
          i +
          ", H=" +
          newH +
          ", W=" +
          newW +
          ", H(px)=" +
          newHPx +
          ", W(px)=" +
          newWPx
      );
    },
    removeItem: function (val) {
      const index = this.layout.map((item) => item.i).indexOf(val);
      console.log("layout", this.layout);
      this.layout.splice(index, 1);
    },

    // 点击编辑
    edit() {
      this.isDraggable = true;
      this.isResizable = true;
    },

    // 点击保存
    saveEdit() {
      this.isDraggable = false;
      this.isResizable = false;
    },

    // 点击左边栏获取id
    getLeftItem(item) {
      console.log("ididid", item);
      // this.mouseEnter();
      window.ondragstart = (ev) => {
        // ev.preventDefault();
        console.log("ondragstart", ev);
      };
      window.ondragend = (ev) => {
        // ev.preventDefault();
        // 判断是否已存在
        const index = this.layout.map((item) => item.i).indexOf(item.add[0].i);
        console.log("indexindex", index);

        if (ev.clientX >= 220 && ev.clientY >= 120) {
          if (index == -1) {
            console.log("item.add", item.add[0]);
            this.layout.push(item.add[0]);
          } else {
            return this.$message({
              message: "已存在该模型，请勿重复拉取！",
              type: "error",
              duration: 3000,
            });
          }
        }
      };
      window.ondragenter = (ev) => {
        // 改变鼠标拖拽时图标
        ev.preventDefault();
      };
      window.ondragover = (ev) => {
        // 改变鼠标拖拽时图标
        ev.preventDefault();
      };
    },

    // 点击关闭
    close(id) {},

    // 点击确定
    submit() {
      let val = this.value;
      if (val == 0 && this.flag1 == true) {
        // console.log("this.flag1", this);
        this.flag1 = false;
        this.layout.push({
          x: 0,
          y: 0,
          w: 3,
          h: 7,
          i: val,
          index: "g2-1",
          isShow: false,
        });
      } else if (val == 1 && this.flag2 == true) {
        this.flag2 = false;
        this.layout.push({ x: 8, y: 0, w: 3, h: 7, i: val, index: "g2-2" });
      } else if (val == 2 && this.flag3 == true) {
        this.flag3 = false;
        this.layout.push({ x: 4, y: 0, w: 4, h: 7, i: val, index: "g2-3" });
      } else {
        return this.$message({
          message: "已存在该模型，请勿重复拉取！",
          type: "error",
          duration: 3000,
        });
      }
      this.dialogVisible = false;
    },

    // 鼠标进入
    mouseEnter() {},

    // 点击关闭弹窗
    handleClose() {
      console.log("close");
    },
    // 指定元素全屏
    toggleFullScreen() {
      if (screenfull.isEnabled) {
        screenfull.toggle(this.$refs.addview);
      }
    },
  },
};
</script>

<style src="./css/app.scss" lang="scss"></style>
