<template>
  <div class="imageLabel-box">
    <div class="imageLabel">
      <div class="imageLabel-img-boxs" @mousemove="imgBoxmove($event)">
        <span class="imageLabel-img-body" ref="imgbody" :style="bodyStyle">
          <div class="imageLabel-loading-body" v-show="false">
            <div class="imageLabel-loading"></div>
          </div>
          <div class="imageLabel-jisuan" ref="jisuan" :style="jisunStyle">
            <img
              :src="imgSrc"
              alt=""
              id="img"
              ref="img"
              class="imageLabel-img"
            />
            <div
              class="imageLabel-content"
              ref="content"
              @click="contentClick($event)"
              @mousedown="contentMousedown($event)"
              @mouseup="contentMouseup($event)"
              @mousemove="contentMousemove($event)"
            >
              <div
                @mousedown="nodeSelected(item)"
                class="
                  imageLabel-imgdrop imageLabel-drop-edit imageLabel-drop-has
                "
                :style="`width:${Math.abs(item.ex - item.x) * 100}%;
                height:${Math.abs(item.ey - item.y) * 100}%;
                left:${item.x * 100}%;top:${item.y * 100}%;z-index:${
                  index + 1
                }`"
                v-for="(item, index) in labeledList"
                :key="index"
                :class="[item.id == curNode.id ? 'active' : '']"
              >
                <span class="imageLabel-imgdrop-font">{{ item.name }}</span>
                <div class="imageLable-i-s">
                  <i class="imageLable-i"></i><i class="imageLable-i"></i
                  ><i class="imageLable-i"></i><i class="imageLable-i"></i
                  ><i class="imageLable-i"></i><i class="imageLable-i"></i
                  ><i class="imageLable-i"></i><i class="imageLable-i"></i>
                </div>
                <em class="imageLable-em"></em><em class="imageLable-em"></em
                ><em class="imageLable-em"></em><em class="imageLable-em"></em>
              </div>
              <div
                v-show="tempNode.x"
                class="
                  imageLabel-imgdrop imageLabel-drop-edit imageLabel-drop-has
                "
                :style="`width:${Math.abs(tempNode.ex - tempNode.x) * 100}%;
                height:${Math.abs(tempNode.ey - tempNode.y) * 100}%;
                left:${tempNode.x * 100}%;top:${tempNode.y * 100}%;z-index:${
                   999
                }`"
              >
                <span class="imageLabel-imgdrop-font">{{tempNode.name}}</span>
                <div class="imageLable-i-s">
                  <i class="imageLable-i"></i><i class="imageLable-i"></i
                  ><i class="imageLable-i"></i><i class="imageLable-i"></i
                  ><i class="imageLable-i"></i><i class="imageLable-i"></i
                  ><i class="imageLable-i"></i><i class="imageLable-i"></i>
                </div>
                <em class="imageLable-em"></em><em class="imageLable-em"></em
                ><em class="imageLable-em"></em><em class="imageLable-em"></em>
              </div>
            </div>
          </div>
        </span>
        <ul class="imageLabel-drap-menu">
          <div style="overflow: hidden">
            <div style="cursor: pointer" class="imageLabel-delete btns">
              删除
            </div>
            <div style="cursor: pointer" class="imageLabel-edit btns">编辑</div>
          </div>
          <li style="padding: 10px"><i></i>红色</li>
          <li style="padding: 10px"><i></i>红色</li>
        </ul>
      </div>
      <div class="imageLabel-buttons">
        <div class="imageLabel-btn imageLabel-closes">关闭</div>
        <div class="imageLabel-btn">确定</div>
      </div>
      <div
        v-show="false"
        class="imageLabel-input"
        style="background-color: rgba(255, 255, 255, 0.3)"
      >
        <div class="imageLabel-input-box" style="width: 250px">
          <div style="background-color: #333">
            <div style="color: #fff; overflow: hidden; line-height: 40px">
              <span style="float: left; margin-left: 20px">编辑内容</span>
              <span
                class="imageLabel-input-close"
                style="float: right; margin-right: 20px; cursor: pointer"
                >X</span
              >
            </div>
          </div>
          <div style="background: #fff; padding: 20px">
            <input
              type="text"
              value=""
              max="10"
              style="width: 100%; padding: 5px"
            />
            <div style="margin-top: 20px; overflow: hidden">
              <div
                class="imageLabel-input-close imageLabel-btn"
                style="float: left; width: 90px; background-color: #959595"
              >
                取消
              </div>
              <div
                class="imageLabel-input-ok imageLabel-btn"
                style="float: right; width: 90px"
              >
                确定
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      imgSrc: require("../assets/img/build.jpg"),
      imgNaturalWidth: 0,
      imgNaturalHeight: 0,
      bodyWidth: 0,
      bodyHeight: 0,
      imgbodyDetail: {},
      imgDetail: {},
      labeledList: [
        {
          x: 0.5353982300884956,
          y: 0.28649764150943396,
          ex: 0.8300884955752212,
          ey: 0.6563089622641509,
          name: "111",
          id: 1,
          selected: false,
        },
        {
          x: 0.22459322286908495,
          y: 0.3854695431472081,
          ex: 0.44959322286908493,
          ey: 0.6189720812182741,
          name: "222",
          id: 2,
          selected: false,
        },
      ],
      tempNode:{

      },
      model: {
        create: false,
        moving: false,
        scale: false,
      },
      curNode: {},
      contenMsg: {},
      startPosition: {},
      newNode: {},
    };
  },
  computed: {
    jisunStyle() {
      let width = 0;
      let height = 0;
      let img = this.imgDetail;
      let body = this.imgbodyDetail;
      if (img.naturalWidth / img.naturalHeight > body.width / body.height) {
        width = "100%";
        height = (img.naturalHeight / img.naturalWidth) * body.width + "px";
      } else {
        height = "100%";
        width = (img.naturalWidth / img.naturalHeight) * body.height + "px";
      }
      return `width:${width};height:${height}`;
    },
    bodyStyle() {
      return "";
    },
  },
  mounted() {
    this.imgbodyDetail = this.getElWidthAndHeight(this.$refs.imgbody);
    this.imgDetail = this.getElWidthAndHeight(this.$refs.img);
    document.querySelector(".imageLabel-box").oncontextmenu = () => {
      return false;
    };
  },
  methods: {
    getElWidthAndHeight(el) {
      return {
        width: el.offsetWidth,
        height: el.offsetHeight,
        naturalWidth: el.naturalWidth,
        naturalHeight: el.naturalHeight,
        offsetLeft: el.offsetLeft,
        offsetTop: el.offsetTop,
      };
    },
    contentClick(e) {},
    contentMousedown(e) {
      let buttonIdx = e.button;
      console.log(buttonIdx);
      if (buttonIdx != 2) {
        let content = this.getElWidthAndHeight(this.$refs.jisuan);
        let contenMsg = {
          x: content.offsetLeft,
          y: content.offsetTop,
          cx: e.clientX,
          cy: e.clientY,
          w: content.width,
          h: content.height,
        };
        console.log(contenMsg)
        this.contenMsg = contenMsg;
        this.startPosition = {
          x: (contenMsg.cx - contenMsg.x) / contenMsg.w,
          y: (contenMsg.cy - contenMsg.y) / contenMsg.h,
          ex: 0,
          ey: 0,
        };
        console.log(this.startPosition)
        // 点击已经存在的框
        if (e.target.className.indexOf("imageLabel-imgdrop") > -1) {
          this.model.moving = true;
        } else if (e.target.className.indexOf("imageLable-i") > -1) {
          this.model.scale = true;
        } else {
          this.model.create = true;
        }
        console.log(this.model.create);
      }
    },
    contentMouseup(e) {
      this.model.create = false;
      this.model.moving = false;
      this.model.scale = false;
      this.labeledList.push(this.tempNode);
      this.tempNode={}
    },
    contentMousemove(e) {
            // if(!this.model.create&&!this.model.scale&&!this.model.moving) return;
      if (this.model.create) {
        let c = this.curNode;
        let t = this.contenMsg;
        this.tempNode.x= this.startPosition.x;
        this.tempNode.y=this.startPosition.y;
        this.tempNode.ey=(e.clientY - t.cy) / t.h;
        this.tempNode.ex=(e.clientX - t.cx) / t.w;
        console.log(this.tempNode)
      } else if (this.model.scale) {
      } else if (this.model.moving) {
      }
    },
    nodeSelected(cur) {
      this.curNode = cur;
    },
    imgBoxmove(e) {

    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="less" scoped>
.imageLabel-jisuan {
  position: relative;
  overflow: hidden;
  width: 100%;
  height: 100%;
}
#img {
  position: absolute;
  width: 100%;
  left: 0;
}
.active {
  background: rgba(115, 133, 67, 0.2) !important;
  border: 2px solid rgb(42, 197, 197) !important ;
  // border-image: url("../assets/img/border.gif");
}
</style>
