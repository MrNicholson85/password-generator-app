<template>
  <div class="pw-gen">
    <div class="pw-gen__container">
      <div class="heading heading--md">Password Generator</div>
      <div class="pw-gen__pw-display">
        <div class="pw-gen__pw-text">
          {{ password || "password display here" }}
        </div>
        <span class="pw-gen__copy-icon"
          ><img :src="copyImg" alt="copy-icon"
        /></span>
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
            label="password slider"
          />
        </div>
        <div class="pw-gen__pw-option">
          <div class="pw-gen__pw-option-item">
            <input type="checkbox" id="checkbox-upper" v-model="includeUpper" />
            <label for="checkbox-upper" class="body">
              Include Uppercase Letters
            </label>
          </div>
          <div class="pw-gen__pw-option-item">
            <input type="checkbox" id="checkbox-lower" v-model="includeLower" />
            <label for="checkbox-lower" class="body">
              Include Lowercase Letters
            </label>
          </div>
          <div class="pw-gen__pw-option-item">
            <input
              type="checkbox"
              id="checkbox-number"
              v-model="includeNumbers"
            />
            <label for="checkbox-number" class="body">Include Numbers</label>
          </div>
          <div class="pw-gen__pw-option-item">
            <input type="checkbox" id="checkbox-sym" v-model="includeSymbols" />
            <label for="checkbox-sym" class="body">Include Symbols</label>
          </div>
        </div>
        <div class="pw-gen__strength">
          <div class="pw-gen__strength-title">Strength</div>
          <div class="pw-gen__strength-levels">
            <span
              class="pw-gen__strength-label"
              :style="{ color: strengthColor }"
              >{{ strengthLabel }}</span
            >
            <ul class="pw-gen__strength-bar">
              <li
                v-for="n in 4"
                :key="n"
                :style="{
                  background:
                    n <= strengthLevel ? strengthColor : 'transparent',
                  borderColor: n <= strengthLevel ? strengthColor : '',
                }"
              ></li>
            </ul>
          </div>
        </div>
        <button
          @click="generatePassword"
          @mouseover="btnHover = true"
          @mouseleave="btnHover = false"
          :class="{ active: btnHover }"
          class="pw-gen__generate-btn"
        >
          <span>Generate</span>
          <div>
            <span v-if="!btnHover"
              ><img :src="genBtnArrow" alt="arrow-icon"
            /></span>
            <span v-else-if="btnHover"
              ><img :src="genBtnArrowHover" alt="arrow-icon"
            /></span>
          </div>
        </button>
      </div>
    </div>
  </div>
</template>
<script>
import copyImg from "../assets/images/Shape.svg";
import genBtnArrow from "../assets/images/bx_arrow-to-left.svg";
import genBtnArrowHover from "../assets/images/bx_arrow-to-left-hover.svg";

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
      genBtnArrow,
      genBtnArrowHover,
      currentValue: this.value,
      btnHover: false,
      // password and options
      password: "",
      includeUpper: true,
      includeLower: true,
      includeNumbers: true,
      includeSymbols: false,
    };
  },
  methods: {
    generatePassword() {
      const length = Number(this.currentValue) || 10;
      const upper = "ABCDEFGHIJKLMNOPQRSTUVWXYZ";
      const lower = "abcdefghijklmnopqrstuvwxyz";
      const numbers = "0123456789";
      const symbols = "!@#$%^&*()_+[]{}|;:,.<>?/~`-=";

      let charset = "";
      if (this.includeUpper) charset += upper;
      if (this.includeLower) charset += lower;
      if (this.includeNumbers) charset += numbers;
      if (this.includeSymbols) charset += symbols;

      if (!charset) {
        // nothing selected, set empty password
        this.password = "";
        return;
      }

      let result = "";
      const cryptoObj = window.crypto || window.msCrypto;
      if (cryptoObj && cryptoObj.getRandomValues) {
        // use crypto for better randomness
        const randomValues = new Uint32Array(length);
        cryptoObj.getRandomValues(randomValues);
        for (let i = 0; i < length; i++) {
          const idx = randomValues[i] % charset.length;
          result += charset.charAt(idx);
        }
      } else {
        for (let i = 0; i < length; i++) {
          const idx = Math.floor(Math.random() * charset.length);
          result += charset.charAt(idx);
        }
      }

      this.password = result;
    },
    onInput(e) {
      // keep currentValue synced as a number/string from the range input
      this.currentValue = e.target.value;
    },
  },
  computed: {
    // strength level 1..4
    strengthLevel() {
      const length = Number(this.currentValue) || 0;
      let score = 0;
      if (length >= 8) score++;
      if (this.includeLower) score++;
      if (this.includeUpper) score++;
      if (this.includeNumbers) score++;
      if (this.includeSymbols) score++;

      // map score to 1..4
      if (score <= 1) return 1;
      if (score === 2) return 2;
      if (score === 3) return 3;
      return 4;
    },
    strengthLabel() {
      switch (this.strengthLevel) {
        case 1:
          return "Weak";
        case 2:
          return "Fair";
        case 3:
          return "Good";
        case 4:
        default:
          return "Strong";
      }
    },
    strengthColor() {
      switch (this.strengthLevel) {
        case 1:
          return "#e74c3c"; // red
        case 2:
          return "#f39c12"; // orange
        case 3:
          return "#f1c40f"; // yellow
        case 4:
        default:
          return "#2ecc71"; // green
      }
    },
  },
};
</script>

<style lang="scss">
@import "@/assets/scss/typography.scss";
@import "@/assets/scss/variables.scss";

.pw-gen {
  display: grid;
  place-items: center;
  height: 100vh;
  margin: 0 16px;

  &__container {
    width: 100%;
    max-width: 540px;
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

    &-item {
      display: flex;
      align-items: center;
      column-gap: 20px;

      input {
        width: 21px;
        height: 21px;
        cursor: pointer;
      }

      label.body {
        padding: 0;
        margin: 0;
        cursor: pointer;
      }
    }
  }

  &__strength {
    display: flex;
    justify-content: space-between;
    align-items: center;
    width: 100%;
    height: 72px;
    background: $app-black;
    margin: 32px 0;

    &-title {
      color: $app-gray;
      text-transform: uppercase;
      font-size: 18px;
      margin-left: 32px;
    }

    &-label {
      text-transform: uppercase;
      margin-right: 15px;
      font-size: 24px;
    }

    &-levels {
      display: flex;
      align-items: center;
      margin-right: 32px;

      ul {
        display: inline-grid;
        list-style: none;
        padding: 0;
        margin: 0;
        grid-template-columns: repeat(4, 14px);
        gap: 8px;

        li {
          background: transparent;
          border: 2px $app-white solid;
          height: 28px;
          width: 10px;
        }
      }
    }
  }

  &__generate-btn {
    background: $app-green;
    text-align: center;
    width: 100%;
    color: $app-black;
    height: 65px;
    text-transform: uppercase;
    display: flex;
    align-items: center;
    justify-content: center;
    margin-bottom: 10px;
    border: 0 transparent solid;
    cursor: pointer;

    &:hover {
      background-color: transparent;
      border: 2px solid $app-green;
      color: $app-green;
    }

    span {
      margin-left: 24px;
      display: flex;
    }
  }
}
</style>
