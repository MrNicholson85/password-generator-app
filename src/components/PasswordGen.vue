<template>
  <div class="pw-gen">
    <div class="heading heading--md">Password Generator</div>
    <div class="pw-gen__container">
      <div class="pw-gen__pw-display">
        password display here
        <span class="pw-gen__copy-icon"><img :src="copyImg" /></span>
      </div>
      <div class="pw-gen__generator">
        <div class="pw-gen__generator-char-limit">
          <div class="pw-gen__generator-content">Character Length</div>
          <div class="pw-gen__generator-value">
            <div v-if="currentValue">{{ currentValue }}</div>
            <div v-else>10</div>
          </div>
        </div>
        <div class="pw-gen__slider">
          <input
            ref="input"
            v-model="currentValue"
            type="range"
            :min="1"
            :max="20"
            class="slider"
            @input="onInput"
          />
        </div>
        <div class="pw-gen__pw-option">
          <div class="pw-gen__pw-option-item">
            <input type="checkbox" id="checkbox" v-model="checked" />
            <label for="checkbox" class="body">Include Uppercase Letters</label>
          </div>
          <div class="pw-gen__pw-option-item">
            <input type="checkbox" id="checkbox" v-model="checked" />
            <label for="checkbox" class="body">Include Lowercase Letters</label>
          </div>
          <div class="pw-gen__pw-option-item">
            <input type="checkbox" id="checkbox" v-model="checked" />
            <label for="checkbox" class="body">Include Numbers</label>
          </div>
          <div class="pw-gen__pw-option-item">
            <input type="checkbox" id="checkbox" v-model="checked" />
            <label for="checkbox" class="body">Include Symbols</label>
          </div>
        </div>
        <div class="pw-gen__strength">
          <div></div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import copyImg from "../assets/images/Shape.svg";
export default {
  props: {
    value: {
      type: Number,
      required: true,
    },
    min: {
      type: Number,
      required: true,
    },
    max: {
      type: Number,
      required: true,
    },
  },
  data() {
    return {
      copyImg,
      currentValue: this.value,
    };
  },
};
</script>
<style lang="scss">
@import "@/assets/scss/typography.scss";
@import "@/assets/scss/variables.scss";

.pw-gen {
  &__container {
    width: 540px;
    margin: 0 auto;
  }

  &__pw-display {
    display: flex;
    justify-content: space-between;
    background-color: $app-coal;
    padding: 19px 32px;
  }

  &__generator {
    margin-top: 24px;
    background-color: $app-coal;
    padding: 19px 32px;

    &-char-limit {
      display: flex;
      justify-content: space-between;
    }

    &-value {
      color: $app-green;
    }
  }

  /* @function webkit-slider-thumb-shadow($i: 1) {
    display: block;
  } */

  &__slider {
    margin-top: 10px;

    .slider {
      display: block;
      appearance: none;
      width: 100%;
      margin: 0;
      height: $height;
      overflow: hidden;
      cursor: pointer;

      &:focus {
        outline: none;
      }

      &::-webkit-slider-runnable-track {
        width: 100%;
        height: $height;
        background: $lower-background;
      }

      &::-webkit-slider-thumb {
        position: relative;
        appearance: none;
        width: 18px;
        height: 18px;
        background: $app-white;
        border-radius: 50%;
        cursor: pointer;
        box-shadow: webkit-slider-thumb-shadow();
        outline: none;
        top: 50%;
        margin-top: (-$thumb-height/2);

        &:hover {
          outline: solid $app-green 2px;
          background: $app-black;
        }
      }

      &::-moz-range-thumb {
        width: 18px;
        height: 18px;
        background: #d8a22e;
        cursor: pointer;
      }

      &::-webkit-range-progress {
        height: 8px;
        background: $app-green;
      }
    }

    input {
      width: 100%;
      background: transparent;
    }
  }

  &__pw-option {
    margin-top: 20px;
    display: grid;
    gap: 10px;

    .body {
      padding: 15px;
    }
  }
}
</style>
