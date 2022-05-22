<template>
  <div id="penetrate-chart" class="penetrate-chart">
    <div class="bt-group">
      <button class="save" @click="saveImg">保存</button>
      <button class="reset" @click="resetSvg">重置</button>
    </div>
    <div id="penetrateChart"></div>
  </div>
</template>

<script>
import Vue from "vue";
// 过渡时间
const DURATION = 0;
// 加减符号半径
const SYMBOLA_S_R = 9;
// 公司
const COMPANY = 0;
// 人
const PERSON = 1;

const MaxIndex = 3;
export default Vue.extend({
  name: "D3",
  data() {
    return {
      layoutTree: "",
      diamonds: "",
      i: 0,
      hasChildNodeArr: [],
      originDiamonds: "",
      diagonalUp: "",
      diagonalDown: "",
      allData: {
        name: "多多包",
        children: [
          { name: "一卡通公司一卡通公司一卡通公司一卡通公司一卡通公司一卡通公司", type: 0, num: 7.89 },
          {
            name: "一卡通公司2",
            type: 0,
            num: 7.89,
            children: [
              {
                name: "小公司1212",
                type: 0,
                children: [
                  { type: 0, name: "笑小下" },
                  { type: 0, name: "笑小下" },
                  { type: 0, name: "笑小下" },
                  { type: 0, name: "笑小下" },
                  { type: 0, name: "笑小下" },
                  {
                    name: "小小小",
                    type: 0,
                    children: [
                      { type: 1, name: "笑小下" },
                      {
                        type: 1,
                        name: "笑小下222",
                        children: [
                          {
                            name: "小小小",
                            type: 0,
                            children: [
                              { type: 1, name: "笑小下" },
                              { type: 1, name: "笑小下222" },
                            ],
                          },
                        ],
                      },
                    ],
                  },
                ],
              },
              { type: 0, name: "小公司223" },
            ],
          },
          {
            name: "一卡通公司2333",
            type: 0,
            num: 7.89,
            children: [
              { type: 0, name: "小公司4545" },
              { type: 0, name: "小公司266565" },
            ],
          },
          { type: 0, num: 7.89, name: "一卡通公司2222一卡通公司2222" },
          { type: 0, num: 9.89, name: "我的公司" },
          {
            type: 0,
            num: 9.89,
            name: "我的公司1111",
            children: [
              { type: 0, name: "小公司7878" },
              { type: 0, name: "小公司2990" },
            ],
          },
        ],
        parents: [
          {
            name: "大公司大公司大公司大公司大公司大公司大公司大公司大公司大公司",
            num: 98,
            type: 0,
            children: [
              {
                name: "发发委",
                type: 0,
                money: "780万元",
                children: [{ type: 0, money: "780万元", name: "123" }],
              },
              {
                name: "123发发委",
                money: "780万元",
                type: 0,
                children: [{ money: "780万元", type: 0, name: "123" }],
              },
              {
                name: "3331122发发委",
                money: "780万元",
                type: 0,
                children: [
                  { money: "780万元", type: 0, name: "123" },
                  {
                    money: "780万元",
                    type: 0,
                    name: "8888",
                    children: [{ type: 0, name: "最后一层" }],
                  },
                ],
              },
            ],
          },
          {
            name: "Apple Flavor & Fragrance Group Co.,Ltd.",
            num: 98,
            type: 0,
            children: [
              {
                name: "发发委",
                type: 0,
                money: "780万元",
                children: [{ type: 0, money: "780万元", name: "123" }],
              },
              {
                name: "123发发委",
                money: "780万元",
                type: 0,
                children: [{ money: "780万元", type: 0, name: "123" }],
              },
            ],
          },
          {
            name: "Apple Flavor & Fragrance Group Co.,Ltd.",
            num: 98,
            type: 0,
            children: [
              {
                name: "发发委",
                type: 0,
                money: "780万元",
                children: [{ type: 0, money: "780万元", name: "123" }],
              },
              {
                name: "123发发委",
                money: "780万元",
                type: 0,
                children: [{ money: "780万元", type: 0, name: "123" }],
              },
            ],
          },
          {
            name: "多多网",
            money: "780万元",
            num: 98,
            type: 0,
            children: [{ type: 0, money: "780万元", name: "发哈哈" }],
          },
          {
            name: "龙龙投资",
            money: "780万元",
            num: 98,
            type: 0,
            children: [
              { type: 0, money: "780万元", name: "王林" },
              { type: 0, money: "780万元", name: "张峰" },
              { type: 0, money: "780万元", name: "侯明" },
            ],
          },
        ],
      },
      tree: {},
      rootUp: "",
      allrootUp: "",
      rootDown: "",
      allrootDown: "",
      treeArr: [],
      alltreeArr: [],
      svg: "",
      showAll: false,
    };
  },
  async asyncData({ query }) {
    console.log(JSON.parse(JSON.stringify(query)), "query.id");
    return { title: "数据" };
  },
  mounted() {
    if (this.allData.children.length > MaxIndex) {
      let allData = JSON.parse(JSON.stringify(this.allData));
      this.tree = { ...allData };
      this.tree.children = allData.children
        .map((item, index) => {
          if (index === allData.children.length - 1) {
            // others = 2 点击收起
            this.allData.children[index].others = 2;
          }
          return item;
        })
        .filter((item, index) => index < MaxIndex);
      // others = 1 点击展开
      this.tree.children.push({ name: "Others", others: 1, type: 0, num: 100 });
    }
    this.init();
    // this.$d3.select('body').append('span').text('Hello, world!')
  },
  methods: {
    async gotoUrl() {},
    dataInit(data, flag) {
      let d3 = this.$d3;
      let upTree = null;
      let downTree = null;
      // 拷贝树的数据
      Object.keys(data).map((item) => {
        if (item === "parents") {
          upTree = JSON.parse(JSON.stringify(data));
          upTree.children = data[item];
          upTree.parents = null;
        } else if (item === "children") {
          downTree = JSON.parse(JSON.stringify(data));
          downTree.children = data[item];
          downTree.parents = null;
        }
      });
      if (flag === 1) {
        // hierarchy 返回新的结构 x0,y0初始化起点坐标
        this.rootUp = d3.hierarchy(upTree, (d) => d.children);
        this.rootUp.x0 = 0;
        this.rootUp.y0 = 0;

        this.rootDown = d3.hierarchy(downTree, (d) => d.children);
        this.rootDown.x0 = 0;
        this.rootDown.y0 = 0;
        // 上 和 下 结构
        this.treeArr = [
          {
            data: this.rootUp,
            type: "up",
          },
          {
            data: this.rootDown,
            type: "down",
          },
        ];
        this.treeArr.map((item) => {
          item.data.children.forEach(this.collapse);
          this.update(item.data, item.type, item.data);
        });
      } else {
        // hierarchy 返回新的结构 x0,y0初始化起点坐标
        this.allrootUp = d3.hierarchy(upTree, (d) => d.children);
        this.allrootUp.x0 = 0;
        this.allrootUp.y0 = 0;

        this.allrootDown = d3.hierarchy(downTree, (d) => d.children);
        this.allrootDown.x0 = 0;
        this.allrootDown.y0 = 0;
        // 上 和 下 结构
        this.alltreeArr = [
          {
            data: this.allrootUp,
            type: "up",
          },
          {
            data: this.allrootDown,
            type: "down",
          },
        ];
        this.alltreeArr.map((item) => {
          item.data.children.forEach(this.collapse);
        });
      }
    },
    init() {
      let d3 = this.$d3;
      let node = document.getElementById("penetrate-chart");
      let svgW = node.clientWidth;
      let svgH = node.clientHeight;

      // 方块形状
      this.diamonds = {
        w: 150,
        h: 84,
        intervalW: 200,
        intervalH: 150,
      };
      // 源头对象
      this.originDiamonds = {
        w: 440,
      };
      this.layoutTree = d3
        .tree()
        .nodeSize([this.diamonds.intervalW, this.diamonds.intervalH])
        .separation(() => 1);
      // 主图
      this.svg = d3
        .select("#penetrateChart")
        .append("svg")
        .attr("width", svgW)
        .attr("height", svgH)
        .attr("id", "treesvg")
        .call(
          d3
            .zoom()
            .scaleExtent([0, 5])
            .on("zoom", (event, d) => {
              this.svg.attr(
                "transform",
                event.transform.translate(svgW / 2, svgH / 2)
              );
            })
        )
        .attr("style", "position: relative;z-index: 2;")
        .append("g")
        .attr("id", "g")
        .attr("transform", "translate(" + svgW / 2 + "," + svgH / 2 + ")")
      this.dataInit(this.tree, 1);
      this.dataInit(this.allData, 2);
    },

    /*
     *[update 函数描述], [click 函数描述]
     *  @param  {[Object]} source 第一次是初始源对象，后面是点击的对象
     *  @param  {[String]} showtype up表示向上 down表示向下
     *  @param  {[Object]} sourceTree 初始源对象
     */
    update(source, showtype, sourceTree) {
      let _this = this;
      if (source.parents === null) {
        source.isOpen = !source.isOpen;
      }
      let nodes;
      if (showtype === "up") {
        nodes = this.layoutTree(this.rootUp).descendants();
      } else {
        let showAll = this.showAll;
        nodes = this.layoutTree(
          showAll ? this.allrootDown : this.rootDown
        ).descendants();
      }
      let links = nodes.slice(1);
      nodes.forEach((d) => {
        // console.log(d.depth * this.diamonds.intervalH, 'd.depth * this.diamonds.intervalH')
        d.y = d.depth * this.diamonds.intervalH;
      });

      let node = this.svg
        .selectAll("g.node" + showtype)
        .data(nodes, (d) => d.id || (d.id = showtype + ++this.i));

      let nodeEnter = node
        .enter()
        .append("g")
        .attr("class", (d) =>
          showtype === "up" && !d.depth ? "hide-node" : "node" + showtype
        )
        .attr("transform", (d) =>
          showtype === "up"
            ? "translate(" + d.x + "," + -d.y + ")"
            : "translate(" + d.x + "," + d.y + ")"
        )
        .attr("opacity", (d) =>
          showtype === "up" && !d.depth
            ? this.showAll
              ? this.allrootDown.data.children.length
              : this.rootDown.data.children.length
              ? 0
              : 1
            : 1
        ); // 拥有下部分则隐藏初始块

      // // 创建矩形1
      nodeEnter
        .append("rect")
        .attr("type", "rectangle")
        .attr("width", (d) => (d.depth === 1 ? 50 : 0))
        .attr("height", (d) => (d.depth === 1 ? 20 : 0))
        .attr("x", (d) => {
          return d.depth === 1
            ? -(this.diamonds.w / 2 - 50 / 2) / 2
            : -this.originDiamonds.w / 2;
        })
        .attr("y", (d) =>
          d.depth === 1
            ? showtype === "up"
              ? this.diamonds.h / 4 + 10 / 2 + 20
              : -this.diamonds.h / 2 - 40 / 2
            : -22
        )
        .attr("rx", 10)
        .attr("ry", 10)
        .style("fill", (d) => {
          return d.depth === 1 ? "#EBF8FF" : "";
        });

      // 矩形1文本
      nodeEnter
        .append("text")
        .attr("type", "rectangletext")
        .attr("x", (d) => {
          return d.depth === 1 ? -50 / 4 : -this.originDiamonds.w / 2;
        })
        .attr("y", (d) =>
          d.depth === 1
            ? showtype === "up"
              ? this.diamonds.h / 4 + 10 / 2 + 20 + 10 + 10 / 2
              : -this.diamonds.h / 2 - 14 / 2
            : 0
        )
        .attr("fill", (d) => (d.depth === 1 ? "#008EDA" : "#fff"))
        .style("font-size", "10px")
        .style("font-family", "PingFangSC-Medium")
        .style("font-weight", "600")
        .text((d) => (d.depth === 1 ? `${d.data?.num}%` : ""));

      // 创建渐变
      let a = "#0FABFF";
      let b = "#0798E5";
      let defs = nodeEnter.append("defs");
      let linerGradient = defs
        .append("linearGradient")
        .attr("id", "linearColor");
      linerGradient
        .append("stop")
        .attr("offset", "0%")
        .style("stop-color", a.toString());
      linerGradient
        .append("stop")
        .attr("offset", "100%")
        .style("stop-color", b.toString());

      // 创建矩形2
      nodeEnter
        .append("rect")
        .attr("type", (d) => d.id)
        .attr("width", (d) =>
          d.depth ? this.diamonds.w : this.originDiamonds.w
        )
        .attr("height", (d) => (d.depth ? this.diamonds.h : 44))
        .attr("x", (d) =>
          d.depth ? -this.diamonds.w / 2 : -this.originDiamonds.w / 2
        )
        .attr("y", (d) =>
          d.depth
            ? showtype === "up"
              ? -this.diamonds.h / 2
              : -this.diamonds.h / 2 + 10 / 2
            : -22
        )
        .attr("rx", (d) => (d.depth ? 10 : 22))
        .attr("ry", (d) => (d.depth ? 10 : 22))
        .style("fill", (d) => {
          if (d.depth === 0) {
            return "url(#" + linerGradient.attr("id") + ")";
          }
          if (d.data.type === COMPANY || !d.depth) {
            return d._children ? "#F2F4F9" : d.depth ? "#F2F4F9" : "#0FABFF";
          }
          if (d.data.type === PERSON || !d.depth) {
            return d._children ? "#F2F4F9" : d.depth ? "#F2F4F9" : "#0FABFF";
          }
        });

      // 创建子节点的圆
      nodeEnter
        .append("circle")
        .attr("type", (d) => d.id || (d.id = showtype + "text" + ++this.i))
        .attr("r", (d) => {
          return d.depth
            ? this.hasChildNodeArr.indexOf(d) === -1
              ? 0
              : SYMBOLA_S_R
            : 0;
        })
        .attr("cy", (d) => {
          return d.depth
            ? showtype === "up"
              ? -this.diamonds.h / 2
              : this.diamonds.h / 2 + 9 / 2
            : 0;
        })
        .attr("cx", 0)
        .style("cursor", "pointer")
        .attr("fill", "#EBF8FF") // 填充颜色
        .on("click", function (event, d) {
          // 点击为 展示子节点
          let showChild = true;
          _this.click(d, showtype, sourceTree, showChild);
          setTimeout(() => {
            if (
              document.querySelector(`text[type="${d.id}"]`).innerHTML === "-"
            ) {
              d.isOpen = false;
              document.querySelector(`text[type="${d.id}"]`).innerHTML = "+";
            } else {
              d.isOpen = true;
              document.querySelector(`text[type="${d.id}"]`).innerHTML = "-";
            }
          }, DURATION);
        });

      // 创建右侧 other 圆
      nodeEnter
        .append("circle")
        .attr("type", (d) => d.id || (d.id = showtype + "text" + ++this.i))
        .attr("r", (d) => {
          if (d.data.others === 1 || d.data.others === 2) {
            return 9;
          }
          return d.depth
            ? this.hasChildNodeArr.indexOf(d) === -1
              ? 0
              : SYMBOLA_S_R
            : 0;
        })
        .attr("cy", (d) => {
          if (d.depth && (d.data.others === 1 || d.data.others === 2)) {
            return 4.5;
          }
        })
        .attr("cx", (d) =>
          d.data.others === 1 || d.data.others === 2 ? this.diamonds.w / 2 : 0
        )
        .style("cursor", "pointer")
        .style("display", (d) => (d.data.others ? "block" : "none"))
        .attr("fill", "#EBF8FF") // 填充颜色
        .on("click", function (event, d) {
          console.log(d, "右侧圆的点击时间");
          _this.showAll = !_this.showAll;
          _this.click(d, showtype, sourceTree);
          setTimeout(() => {
            if (
              document.querySelector(`text[type="${d.id}"]`).innerHTML === "-"
            ) {
              d.isOpen = false;
              document.querySelector(`text[type="${d.id}"]`).innerHTML = "+";
            } else {
              d.isOpen = true;
              document.querySelector(`text[type="${d.id}"]`).innerHTML = "-";
            }
          }, DURATION);
        });

      // 公司名称
      // y轴 否表源头的字体距离
      nodeEnter
        .append("text")
        .attr("x", (d)=> {
          if(d.depth) {
           return -this.diamonds.w / 2 + 10
          }
        })
        .attr("y", (d) => {
          if (d.depth) {
            if (showtype === "up") {
              return -this.diamonds.h / 2 + 5 ;
            } else {
              return -this.diamonds.h / 2 + 10 ;
            }
          } 
        })
        .attr("dy", (d) =>
          d.depth ? (d.data.name.length > 12 ? "1.5em" : "2em") : ".3em"
        )
        .attr("fill", (d) => (d.depth ? "#172434" : "#fff"))
        .text((d) =>
          d.data.name.length > 12 ? d.data.name.substr(0, 12) : d.data.name
        )
        .attr("text-anchor", (d) => d.depth? '':"middle")
        .style("font-size", (d) => (d.depth === 0 ? "18px" : "10px"))
        .style("font-family", (d) =>
          d.depth === 0
            ? "HarmonyOS_Sans_Condensed_Bold_Italic"
            : "PingFangSC-Medium, PingFang SC"
        )
        .style("font-weight", "500");

      // 名称过长 第二段
      nodeEnter
        .append("text")
        .attr("x", -this.diamonds.w / 2 + 10)
        .attr("y", (d) => {
          if (d.depth) {
            if (showtype === "up") {
              return -this.diamonds.h / 2 + 5 ;
            } else {
              return -this.diamonds.h / 2 + 10 ;
            }
          }
        })
        .attr("dy", (d) => (d.depth ? "3em" : ".3em"))
        .attr("fill", (d) => (d.depth ? "#172434" : "#fff"))
        .text((d) => {
          // 索引从第19个开始截取 截取长度为1 有值表示超出
          if (d.data.name.substr(19, 1)) {
            // 英文应该截取的更长些
            return d.data.name.substr(12, 12) + "...";
          }
          return d.data.name.substr(12, 12);
        })
        .style("font-size", "10px")
        .style("font-family", "PingFangSC-Medium, PingFang SC")
        .style("font-weight", "500");

      // 金额label
      nodeEnter
        .append("text")
        .attr("x", -this.diamonds.w / 2 + 10)
        .attr("y", (d) => {
          if (d.depth) {
            if (showtype === "up") {
              return -this.diamonds.h / 2 + 5 ;
            } else {
              return -this.diamonds.h / 2 + 10 ;
            }
          }
        })
        .attr("dy", "4.5em")
        .attr("fill", (d) => (d.depth ? "#838D99" : "#fff"))
        .text("Subscribed amount：")
        .style("font-size", "10px")
        .style("font-family", "PingFangSC-Regular, PingFang SC")

      // 金额
      nodeEnter
        .append("text")
        .attr("x", -this.diamonds.w / 2 + 10)
        .attr("y", (d) => {
          if (d.depth) {
            if (showtype === "up") {
              return -this.diamonds.h / 2 + 5 ;
            } else {
              return -this.diamonds.h / 2 + 10 ;
            }
          }
        })
        .attr("dy", "6.2em")
        .attr("fill", (d) => (d.depth ? "#008EDA" : "#fff"))
        .text("$517,030.00")
        .style("font-size", "10px")
        .style("font-family", "HarmonyOS_Sans_Condensed_Bold_Italic");

      // 将节点转换到它们的新位置。
      let nodeUpdate = node
        .transition()
        .duration(DURATION)
        .attr("transform", (d) =>
          showtype === "up"
            ? "translate(" + d.x + "," + -d.y + ")"
            : "translate(" + d.x + "," + d.y + ")"
        );

      // 代表是否展开的+-号,function this指向当前dom
      nodeEnter
        .append("svg:text")
        .attr("type", (d) => d.id || (d.id = showtype + "text" + ++this.i))
        .on("click", function (event, d) {
          let showChild = true;
          _this.click(d, showtype, sourceTree, showChild);
          console.log("这是点击下面的+-展开子节点", d);
          setTimeout(() => {
            if (this.innerHTML === "-") {
              d.isOpen = false;
              this.innerHTML = "+";
              this.setAttribute("fill", "#008EDA");
              document
                .querySelector(`circle[type="${d.id}"]`)
                .setAttribute("fill", "#EBF8FF");
              document
                .querySelector(`rect[type="${d.id}"]`)
                .setAttribute("style", "fill:#F2F4F9");
            } else {
              d.isOpen = true;
              this.innerHTML = "-";
              this.setAttribute("fill", "#008EDA");
              // circle 圆形
              console.log(d, "zheli报错了");
              document
                .querySelector(`circle[type="${d.id}"]`)
                .setAttribute("fill", "#EBF8FF");
              // rect 矩形
              document
                .querySelector(`rect[type="${d.id}"]`)
                .setAttribute("style", "fill:#F2F4F9");
            }
          }, DURATION);
        })
        .attr("x", 0)
        .attr("dy", (d) => {
          return d.depth
            ? showtype === "up"
              ? -this.diamonds.h / 2 + 9 / 2
              : this.diamonds.h / 2 + 10
            : 0;
        })
        .attr("text-anchor", "middle")
        .style("cursor", "pointer")
        .attr("fill", (d) => (d._children ? "#008EDA" : "#008EDA"))
        .text((d) => {
          return this.hasChildNodeArr.indexOf(d) !== -1
            ? source.depth && d.isOpen
              ? "-"
              : "+"
            : "";
        });
      // 创建右侧 圆 中的加减
      nodeEnter
        .append("svg:text")
        .attr("type", (d) => d.id || (d.id = showtype + "righttext" + ++this.i))
        .on("click", function (event, d) {
          _this.showAll = !_this.showAll;
          _this.click(d, showtype, sourceTree);
          console.log(d, "这是点击右边的圆中的加减");
          setTimeout(() => {
            if (this.innerHTML === "-") {
              d.isOpen = false;
              this.innerHTML = "+";
              this.setAttribute("fill", "#008EDA");
              document
                .querySelector(`circle[type="${d.id}"]`)
                .setAttribute("fill", "#EBF8FF");
              document
                .querySelector(`rect[type="${d.id}"]`)
                .setAttribute("style", "fill:#F2F4F9");
            } else {
              d.isOpen = true;
              this.innerHTML = "-";
              this.setAttribute("fill", "#008EDA");
              // circle 圆形
              console.log(d, "zheli报错了");
              document
                .querySelector(`circle[type="${d.id}"]`)
                .setAttribute("fill", "#EBF8FF");
              // rect 矩形
              document
                .querySelector(`rect[type="${d.id}"]`)
                .setAttribute("style", "fill:#F2F4F9");
            }
          }, DURATION);
        })
        .attr("x", (d) => (d.data.others === 1 || d.data.others === 2 ? 75 : 0))
        .attr("dy", (d) => {
          if (d.data.others === 1 || d.data.others === 2) {
            return 9 / 2 + 10 / 2;
          }
          return d.depth
            ? showtype === "up"
              ? -this.diamonds.h / 2 + 9 / 2
              : this.diamonds.h + 9 / 2 + 10 / 5
            : 0;
        })
        .attr("text-anchor", "middle")
        .style("cursor", "pointer")
        .style("display", (d) => (d.data.others ? "block" : "none"))
        .attr("fill", (d) => (d._children ? "#008EDA" : "#008EDA"))
        .text((d) => {
          if (d.data.others === 1) {
            return "+";
          }
          if (d.data.others === 2) {
            return "-";
          }
          return this.hasChildNodeArr.indexOf(d) !== -1
            ? source.depth && d.isOpen
              ? "-"
              : "+"
            : "";
        });

      // 将退出节点转换到父节点的新位置.
      let nodeExit = node
        .exit()
        .transition()
        .duration(DURATION)
        .attr("transform", () =>
          showtype === "up"
            ? "translate(" + source.x + "," + -source.y + ")"
            : "translate(" + source.x + "," + parseInt(source.y) + ")"
        )
        .remove();

      nodeExit
        .select("rect")
        .attr("width", this.diamonds.w)
        .attr("height", this.diamonds.h)
        .attr("stroke", "black")
        .attr("stroke-width", 1);

      // 修改线条
      let link = this.svg
        .selectAll("path.link" + showtype)
        .data(links, (d) => d.id);

      // 在父级前的位置画线。
      let linkEnter = link
        .enter()
        .insert("path", "g")
        .attr("class", "link" + showtype)
        .attr("marker-start", (d) => `url(#${showtype}resolved${d.data.type})`) // 根据箭头标记的id号标记箭头
        .attr("stroke", (d) =>
          d.data.type === COMPANY ? "#008EDA" : "#008EDA"
        )
        .style("fill-opacity", 1)
        .attr("fill", "none")
        .attr("stroke-width", "1px")
        .attr("d", (d) => {
          let o = {
            x: source.x0,
            y: source.y0,
          };
          return _this.diagonal(o, o, showtype, d.depth);
        });

      let linkUpdate = linkEnter.merge(link);
      // 过渡更新位置.
      linkUpdate
        .transition()
        .duration(DURATION)
        .attr("d", (d) => _this.diagonal(d, d.parent, showtype, d.depth));

      // 将退出节点转换到父节点的新位置
      link
        .exit()
        .transition()
        .duration(DURATION)
        .attr("d", (d) => {
          let o = {
            x: source.x,
            y: source.y,
          };
          return _this.diagonal(o, o, showtype, d.depth);
        })
        .remove();

      // 隐藏旧位置方面过渡.
      nodes.forEach((d) => {
        d.x0 = d.x;
        d.y0 = d.y;
      });
    },

    // 拷贝到_children 隐藏1排以后的树
    collapse(source) {
      if (source.children) {
        source._children = source.children;
        source._children.forEach(this.collapse);
        source.children = null;
        this.hasChildNodeArr.push(source);
      }
    },
    // closeChid(val) {
    //   let arr = []
    //   let arrFun = (data) => {
    //     data.map((item, index)=> {
    //       if (item.isOpen) {
    //         item.isOpen = false
    //       }
    //       if (item.data && item.data.children && item.data.children.length) {
    //         arrFun(item.data.children)
    //       } else {
    //         arr.push(item)
    //       }
    //     })
    //   }
    //   arrFun(val)
    //   return arr
    // },
    click(source, showType, sourceTree, showChild = false) {
      // 不是起点才能点
      if (source.depth) {
        if (
          source.data.others === 1 &&
          source.data.name === "Others" &&
          showType === "down"
        ) {
          this.alltreeArr.map((item) => {
            if (item.type === "down") {
              this.update(item.data, showType, item.data);
            }
          });
        }
        // others = 2 有两种情况
        if (source.data.others === 2 && !showChild && showType === "down") {
          // 点击 收起同级 则进行数据替换
          this.treeArr.map((item) => {
            if (item.type === "down") {
              this.update(item.data, showType, item.data);
            }
          });
        }
        // 点击展开子数据
        if (!source.data.others || (source.data.others === 2 && showChild)) {
          if (source.children) {
            source._children = source.children;
            source.children = null;
          } else {
            source.children = source._children;
            source._children = null;
          }
          this.update(source, showType, sourceTree);
        }
      }
    },

    diagonal(s, d, showtype, depth) {
      let path;
      if (showtype === "up") {
        if (depth === 1) {
          // 层级1
          path = `M${s.x} ${-s.y + 55}
          ${s.x} -${(s.y + d.y) * 0.45}
          M${s.x} -${(s.y + d.y) * 0.45}
          ${
            s.x
              ? `A20 20 0 0 ${s.x > d.x ? 1 : 0} ${
                  s.x > d.x ? s.x - 20 : s.x + 20
                } -${d.y + 50}`
              : `${s.x === d.x ? s.x : s.x > d.x ? s.x - 20 : s.x + 20} -${
                  d.y + 50
                }`
          }
          M${s.x === d.x ? s.x : s.x > d.x ? s.x - 20 : s.x + 20} -${d.y + 50}
          ${d.x} -50
          L${d.x} -${d.y}`;
        } else {
          // 其他层级
          path = `M${s.x} ${-s.y + 24}
          ${s.x} -${(s.y + d.y) * 0.52}
          M${s.x} -${(s.y + d.y) * 0.52}
          ${
            s.x !== d.x
              ? `A20 20 0 0 ${s.x > d.x ? 1 : 0} ${
                  s.x > d.x ? s.x - 20 : s.x + 20
                } -${d.y + 70}`
              : `${d.x} -${d.y + 70}`
          }
          M${s.x > d.x ? s.x - 20 : s.x == d.x ? s.x : s.x + 20} -${d.y + 70}
          ${d.x} -${d.y + 70}
          L${d.x} -${d.y}`;
        }
      } else {
        if (depth === 1) {
          path = `M ${s.x} ${s.y - 50}
            ${s.x} ${(s.y + d.y) * 0.45}
            M${s.x} ${(s.y + d.y) * 0.45}
            ${
              s.x
                ? `A20 20 0 0 ${s.x > d.x ? 0 : 1} ${
                    s.x > d.x ? s.x - 20 : s.x + 20
                  } ${d.y + 50}`
                : `${s.x === 0 ? s.x : s.x > d.x ? s.x - 20 : s.x + 20} ${
                    d.y + 50
                  }`
            } 
            M${s.x === 0 ? s.x : s.x > d.x ? s.x - 20 : s.x + 20} ${d.y + 50}
            ${d.x} ${depth === 1 ? 50 : d.y}
            ${d.x} ${d.y}`;
        } else {
          path = `M${s.x} ${s.y}
          ${s.x} ${s.y - 55}
          M${s.x} ${s.y - 55}
          ${
            s.x !== d.x
              ? `A20 20 0 0 ${s.x > d.x ? 0 : 1} ${
                  s.x > d.x ? s.x - 20 : s.x + 20
                } ${d.y + 80}`
              : `${d.x} ${d.y + 70}`
          }
          M${s.x > d.x ? s.x - 20 : s.x == d.x ? s.x : s.x + 20} ${d.y + 80}
          ${d.x} ${d.y + 80}
          L${d.x} ${d.y}`;
        }
      }
      return path;
    },

    saveImg() {
      alert("保存");
    },

    resetSvg() {
      this.d3.select("#treesvg").remove();
      this.init();
    },
  },
});
</script>

<style lang="scss">
.penetrate-chart {
  width: 100%;
  height: 700px;
  margin: 0 auto;
  .bt-group {
    position: fixed;
    z-index: 999;
    right: 15px;
    bottom: 15px;
    button {
      width: 88px;
      height: 32px;
      display: block;
      border-radius: 18px;
      font-size: 14px;
      font-family: PingFangSC-Medium;
      font-weight: 500;
      line-height: 20px;
    }
    .save {
      background: rgba(255, 168, 9, 1);
      color: rgba(255, 255, 255, 1);
    }
    .reset {
      margin-top: 8px;
      color: rgba(255, 168, 9, 1);
      background: white;
      border: 1px solid rgba(255, 168, 9, 1);
    }
  }
}
#treesvg {
  display: block;
  margin: auto;
  #g {
    .linkup,
    .linkdown {
      fill: none;
      stroke-width: 1px;
    }
    .text-style-name {
      font-size: 12px; /*no*/
      font-family: PingFangSC-Medium;
      font-weight: 500;
    }
    .text-style-money {
      font-size: 10px; /*no*/
      font-family: PingFangSC-Regular;
      font-weight: 400;
      color: rgba(70, 81, 102, 1);
    }
    .proportion {
      font-size: 10px;
      font-family: PingFangSC-Regular;
      font-weight: 400;
    }
  }
  .proportion-hide,
  .hide-node {
    display: none;
  }
  // .centerClass {
  //   width: 400px;
  //   height: 44px;
  //   background: linear-gradient(135deg, #0FABFF 0%, #0798E5 100%);
  //   box-shadow: 0px 2px 10px 0px rgba(0, 142, 218, 0.24);
  //   border-radius: 22px;
  // }
}
</style>
