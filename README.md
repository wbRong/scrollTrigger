
## ScrollTrigger使用
官方介绍引入的方法非常有意思，直接到[官网](https://greensock.com/docs/v3/Installation)选择对应的插件`CDN/npm`引入。
### 引入CDN
 - https://cdnjs.cloudflare.com/ajax/libs/gsap/3.10.4/gsap.min.js
 - https://cdnjs.cloudflare.com/ajax/libs/gsap/3.10.4/ScrollTrigger.min.js

**最常见的GSAP错误**

    // Not recommended
    x: "50%",
    y: "50%",

    // Recommended
    xPercent: 50,
    yPercent: 50

    // The right method
    x: 100,
    y: 300,
    xPercent: 50,
    yPercent: 50
  
简单使用，相当于页面加载完即开始动画

    gsap.to(".target", {
      x: 400,
      rotation: 360,
      duration: 3
    });

toggleActions: 当 scroll 滚动的几种场景触发条件

    gsap.to(".target", {
      scrollTrigger: {
        trigger: ".target",
        // toggleActions: "onEnter onLeave onEnterBack onLeaveBack"
        toggleActions: "restart pause reverse pause" // play,pause,resume,reverse,restart,reset,complete,none
      },
      x: 400,
      rotation: 360,
      duration: 3
    });

start: 何时开始/结束动画

    gsap.to(".target", {
      scrollTrigger: {
        trigger: ".target",
        // String: top, bottom, ((left, right) && (horizontal: true)), center, 80%, 100px, bottom-=100px => 距离底部100px
        // Number: 200 => 一个精确的滚动值，所以当viewport/scroller滚动正好200像素时，200将触发
        // Function: () => "+=" + document.querySelector(".target").offsetWidth
        start: "top center",
        end: "+=100",
        toggleActions: "restart pause reverse pause"
      },
      x: 400,
      rotation: 360,
      duration: 3
    });

markers: 标记触发点

    gsap.to(".target", {
      scrollTrigger: {
        trigger: ".target",
        start: "top center",
        markers: true, // or {startColor: "white", endColor: "white", fontSize: "18px", fontWeight: "bold", indent: 20}
        toggleActions: "restart pause reverse pause"
      },
      x: 400,
      rotation: 360,
      duration: 3
    });

trigger/endTrigger: 根据元素来触发/结束动画

    gsap.to(".target", {
      scrollTrigger: {
        trigger: ".a",
        start: "top center",
        endTrigger: ".c",
        end: "bottom 80%",
        markers: true,
        toggleActions: "restart pause reverse pause"
      },
      x: 400,
      rotation: 360,
      duration: 3
    });

scrub: 让动画播放赶上滚动条，即根据进度条来控制动画进度

    gsap.to(".target", {
      scrollTrigger: {
        trigger: ".target",
        start: "top center",
        end: "top 100px",
        scrub: true, // 0.5, 以秒计算
        markers: true
      },
      x: 400,
      rotation: 360,
      duration: 3
    });

结合 timeline 完成动效

    let tl = gsap.timeline({
      scrollTrigger: {
        trigger: ".target",
        start: "top center",
        end: "top 100px",
        scrub: 1,
        markers: true
      }
    });

    tl.to(".target", {
      x: 400,
      rotation: 360,
      ease: "none",
      duration: 3
    })
    .to(".target", {
        backgroundColor: "purple",
        duration: 1
    })
    .to(".target", {
      x: 0,
      duration: 2
    });

pin: Boolean | String | Element，钉住元素的位置，类似 position: sticky

    gsap.to(".target", {
      scrollTrigger: {
        trigger: ".target",
        start: "top center",
        end: "top 100px",
        scrub: true,
        pin: ".target_ghost",
        markers: true
      },
      x: 400,
      rotation: 360,
      duration: 3
    });

使用 pin 完成动效

    gsap.defaults({ease: "none", duration: 2});

    const tl = gsap.timeline();
    tl.from(".a", {xPercent: -100})
      .from(".b", {xPercent: 100})
      .from(".c", {yPercent: -100});

    ScrollTrigger.create({
      animation: tl,
      trigger: "#container",
      start: "top top",
      end: "+=4000",
      scrub: true,
      pin: true,
      anticipatePin: 1, // 通常最合适，可以减少或增加该数字以控制钉扎的时间
    });

pinSpacing: 解决使用 pin 时出现的填充(padding)问题

    ScrollTrigger.create({
      trigger: ".target",
      start: "top top",
      end: "+=300px",
      // 默认情况下，填充将添加到底部(或在水平右边 && horizontal: true)以将其他元素下推，以便在固定元素被取消固定时，以下内容可以完美地捕捉到。 否则，事物可能会在固定元素下滚动
      pinSpacing: false, // "margin", 使用 margin 代替 padding
      pin: true
    });


对 Array[Element] 使用 pin

    gsap.utils.toArray(".panel").forEach((panel, i) => {
      ScrollTrigger.create({
        trigger: panel,
        start: "top top",
        pin: true,
        pinSpacing: false
      });
    });
snap: Number | Array | Function | Object | "labels" ,允许您在用户停止滚动后捕获某些进度值(在0到1之间)。所以snap: 0.1会以0.1为增量进行snap(10%， 20%， 30%，等等)。snap:[0, 0.1, 0.5, 0.8, 1]只会让它停留在其中一个特定的进度值上.
Number: 0.1 || 1 / (sections - 1);
Array: [0, 0.1, 0.5, 0.8, 1];
Function: (value) => Math.round(value / 0.2) * 0.2;
"labels": “标签”与时间线中最接近的标签对齐(动画必须是带有标签的时间线);
Object: {snapTo: "labels", duration: 0.3, delay: 0.1, ease: "power1.inOut"}; // snapTo 是必须的



Callbacks and Custon Options
toggleClass: String | Object，当ScrollTrigger切换活动/不活动时，将一个类添加/删除到一个(或多个)元素

    // 全局配置

    ScrollTrigger.defaults({
      toggleActions: "restart pause resume none",
      markers: true
    });

    ScrollTrigger.create({
      trigger: ".target",
      start: "top center",
      end: "top 100px",
      onEnter: () => console.log("enter"), // 滚动到进入
      onLeave: () => console.log("leave"), // 滚动到退出
      onEnterBack: () => console.log("enter back"), // 滚动到进入位置
      onLeaveBack: () => console.log("leave back"), // 滚动到退出位置
      onUpdate: (self) => console.log("update", self.progress.toFixed(3)), // 开始-结束之间滚动
      onToggle: (self) => console.log("toggle", self.isActive), // 从开始开结束为 true
      toggleClass: "active", // 当在 onToggle 中 self.isActive === true 时，为指定元素添加 class="active"
      id: "my-id", // 为元素添加id
      scroller: "#container", // 根据指定元素的滚动值来计算
      horizontal: true, // 水平滚动
      markers: true
    });

    如果在 ScrollTrigger.create 中添加了 id，则可以通过一下获取
    let trigger = ScrollTrigger.getById("my-id");

