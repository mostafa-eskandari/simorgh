<!-- صفحه اصلی -->
<template>
  <div class="home" ma-4 id="">
    <v-flex>
      <p id="test">zxdfvsdfgvsadz</p>
      <v-btn class="hj" id="DOMContentLoaded" wrap @click="looaded">
        somting
      </v-btn>
    </v-flex>
    <div>
      <div wrap class="wrapper">
        <!-- <button class="accelerated box"></button> -->
        <button class="janky box"></button>
      </div>
    </div>

    <!-- <img alt="Vue logo" src="../assets/VueLogo.png"> -->
    <HelloWorld msg="" />
    <div my-5>
      <svg-logo class="logo-fade-in--open" style="opacity: 0.5" />
    </div>
    <SvgLogoText />
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from "@/components/HelloWorld.vue";
const SvgLogo = () => import("@/components/SvgLogo.vue");
const SvgLogoText = () => import("@/components/SvgLogoText.vue");

export default {
  name: "Home",
  components: {
    SvgLogo,
    HelloWorld,
    SvgLogoText,
  },
  mounted: function () {
    setTimeout(() => {
      var e = (document.getElementById("test").innerHTML = "adf;kjsdflvnsdf.,/vnkdsfuvnkjnxfvfd");
      this.e.style.background = red;
    }, 2000);
  },
  methods: {},
};
</script>
<style scoped>
.logo-fade-in {
  opacity: 0;
}
.logo-fade-in--open {
  animation: fadeIn linear 1s forwards;
}
@keyframes fadeIn {
  0% {
    opacity: 0;
  }
  75% {
    opacity: 0.3;
  }
  100% {
    opacity: 1;
  }
}
/*ظاهر المان */
.box {
  width: 50px;
  height: 50px;
  border: none;
  border-radius: 4px;
  outline-offset: 4px;
}
/* .box:first-of-type {
  margin-right: 16px;
} */

.wrapper {
  display: flex;
}
/* .accelerated.box {
  transition: transform 750ms;
  will-change: transform;
  background: slateblue;
} */

/* جا به جایی در ارتفاع  */
/* .accelerated.box:hover,
.accelerated.box:focus {
  transform: translateY(10px);
} */

.janky.box {
  transition: margin-top 750ms;
  will-change: margin-top;
  background: deeppink;
}
.janky.box:hover,
.janky.box:focus {
  margin-top: 10px;
}
.test:focus {
  transition: margin-top 750ms;
  will-change: margin-top;
  margin-top: 10px;
}
</style>