<template>
  <canvas />
  <input
    ref="codeInput"
    :v-model="inputValue"
    class="code-input"
    :style="{ top: inputY * fontSize + 'px', left: inputX * fontSize + 'px' }"
    maxlength="6"
    @input="checkInput"
  />
  <h2 v-if="phraseVisible">Don't believe your eyes. Shall we play?</h2>
</template>

<script>
export default {
  async mounted() {
    this.createCanvas();
    this.context.font = `${this.fontSize}px system-ui`;
    await this.animate();
  },

  data() {
    return {
      canvas: null,
      context: null,
      fontSize: 20,
      fontColor: '#0f0',
      textSymbols:
        '1111640401671411630401410401501651471450540401641671570551471511621641500401571411530540401671511641500401421571651471501630401421621571531451560401571461460540401411601601411621451561641541710401541571561470401411471570540401411561440401671511641500401421621571531451560401421411621530540401571661451621471621571671560401671511641500401571541440401631571621451630560560401271511641500401501511630401501651471450540401431541651551631511541710540401411631711551551451641621511431411541541710401631601621451411440401471561411621541451440401501411561441630401411561440401461511561471451621630540401501450401631641571571440401541511531450401411560401571541440540401411561471621710401411561440401431571561641451551601641651571651630401461621451411530401421451641671451451560401641501450401631551511541511561470401421511621431501451630560401221451601541411710561111640401671411630401410401501651471450540401641671570551471511621641500401571411530540401671511641500401421571651471501630401421621571531451560401571461460540401411601601411621451561641541710401541571561470401411471570540401411561440401671511641500401421621571531451560401421411621530540401571661451621471621571671560401671511641500401571541440401631571621451630560560401271511641500401501511630401501651471450540401431541651551631511541710540401411631711551551451641621511431411541541710401631601621451411440401471561411621541451440401501411561441630401411561440401461511561471451621630540401501450401631641571571440401541511531450401411560401571541440540401411561471621710401411561440401431571561641451551601641651571651630401461621451411530401421451641671451451560401641501450401631551511541511561470401421511621431501451630561231507'.split(
          '',
        ),
      inputX: 0,
      inputY: 0,
      inputVisible: false,
      inputValue: '',
      phraseVisible: false,
    };
  },

  computed: {
    calculateColumns() {
      return Math.round(window.innerWidth / this.fontSize) - 2;
    },
  },

  methods: {
    sleep(ms) {
      return new Promise((resolve) => setTimeout(resolve, ms));
    },
    drawNumber(txt, x, y) {
      this.context.fillStyle = this.fontColor;
      this.context.fillText(txt, x * this.fontSize, y * this.fontSize);
    },

    createCanvas() {
      this.canvas = document.querySelector('canvas');
      this.canvas.width = window.innerWidth;
      this.canvas.height = window.innerHeight;
      this.context = this.canvas.getContext('2d');
    },

    async animate() {
      let x = 1;
      let y = 1;
      for (let txt of this.textSymbols) {
        this.drawNumber(txt, x, y);
        await this.sleep(0.01);
        x++;
        if (x >= this.calculateColumns) {
          y++;
          x = 1;
        }
      }

      this.inputX = x;
      this.inputY = y - 1;
      this.inputVisible = true;

      this.$refs.codeInput.focus();
    },

    async deleteNumbers() {
      for (let y = this.inputY; y >= 0; y--) {
        for (let x = this.calculateColumns; x > 0; x--) {
          this.context.fillStyle = 'black';
          this.context.fillRect(x * this.fontSize, y * this.fontSize, this.fontSize, this.fontSize);
          await this.sleep(0.01);
        }
      }
      this.phraseVisible = true;
    },

    async checkInput(event) {
      if (event.data) {
        this.inputValue += event.data;
      } else {
        this.inputValue = this.inputValue.slice(0, -1);
      }
      console.log(event.data, this.inputValue);
      if (this.inputValue == '051321') {
        this.$refs.codeInput.value = '';
        this.$refs.codeInput.blur();
        await this.deleteNumbers();
      }
    },
  },
};
</script>

<style scoped lang="scss">
canvas {
  height: 100%;
}

input {
  position: absolute;
  background: transparent;
  font-size: 20px;
  font-style: 'system-ui';
  color: #00ff00;
  letter-spacing: 8px;
  border: 0px;
}

input:focus {
  outline: none;
}

h2 {
  position: absolute;
  top: 25%;
  margin: 0 auto;
  display: block;
  color: #00ff00;
  font-family: 'system-ui';
  left: 40%;
}
</style>
