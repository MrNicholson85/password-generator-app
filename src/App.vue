<template>
  <PasswordGen />
</template>

<script>
import UIkit from "uikit";
import Icons from "uikit/dist/js/uikit-icons";
import PasswordGen from "./components/PasswordGen.vue";

UIkit.use(Icons);

export default {
  name: "App",
  components: { PasswordGen },
  data() {
    return {
      PasswordGen,
    };
  },
};
</script>

<style lang="scss">
@import "../node_modules/uikit/dist/css/uikit.min.css";
@import "./assets/scss/variables.scss";
@import "./assets/scss/typography.scss";

#app {
  background-color: $app-black;
  height: 100vh;
  color: $app-white;
}
</style>
