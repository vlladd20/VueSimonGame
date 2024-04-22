<template>

  <div class="game">
    <div class="game_inner">
      <div class="col">
        <div class="red" ref="1" @click="click(1)"></div>
        <div class="blue" ref="2" @click="click(2)"></div>
      </div>
      <div class="col">
        <div class="yellow" ref="3" @click="click(3)"></div>
        <div class="green" ref="4" @click="click(4)"></div>
      </div>
      <button @click="start">
        {{buttonText}}
      </button>
    </div>

    <div class="content_res">
      <p>Раунд: {{round}}</p>
      <p>Уровень сложности</p>
      <label v-for="item in ['Легкий', 'Средний', 'Сложный']" :key="item">
        <input
          type="radio"
          v-model="level"
          name="level"
          @click="time = levelTime[item]"
          :value="item">
        {{item}}
      </label>
    </div>
  </div>
</template>

<script>

export default {
  data () {
    return {
      round: 0,
      buttonText: 'Старт',
      inGame: false,
      level: 'Легкий',
      time: 1500,
      timeoutID: null,
      audio: {
        1: new Audio('https://s3.amazonaws.com/freecodecamp/simonSound1.mp3'),
        2: new Audio('https://s3.amazonaws.com/freecodecamp/simonSound2.mp3'),
        3: new Audio('https://s3.amazonaws.com/freecodecamp/simonSound3.mp3'),
        4: new Audio('https://s3.amazonaws.com/freecodecamp/simonSound4.mp3')
      },
      levelTime: {
        Легкий: '1500',
        Средний: '1000',
        Сложный: '400'
      },
      orderList: [],
      userList: []
    }
  },
  methods: {
    click (id) {
      this.audio[id].play()
      this.$refs[id].style.opacity = '1'
      setTimeout(() => {
        this.$refs[id].style.opacity = '0.5'
      }, 200)
      if (this.inGame) {
        clearTimeout(this.timeoutID)
        this.userList.push(id)
        this.checkUserList(this.userList.length - 1)
      }
    },

    start () {
      this.orderList = []
      this.userList = []
      this.time = this.levelTime[this.level]
      this.step()
    },

    step () {
      this.userList = []
      this.playOrderList()
      this.buttonText = 'Слушайте'
    },

    playOrderList () {
      this.inGame = false
      this.orderList.push(this.random())

      this.orderList.forEach((id, i) => {
        setTimeout(() => {
          this.click(id)
        }, 700 * i)
      })
      setTimeout(() => {
        this.inGame = true
        this.buttonText = 'Повторите'
        this.timer()
      }, this.orderList.length * 700)
    },

    checkUserList (item) {
      if (this.userList[item] !== this.orderList[item]) {
        return this.gameOver()
      }
      if (this.userList.length === this.orderList.length) {
        this.round++
        setTimeout(() => {
          this.step()
        }, 1000)
      } else {
        this.timer()
      }
    },

    gameOver () {
      this.buttonText = 'Game Over'
      setTimeout(() => {
        this.buttonText = 'Старт'
      }, 1000)
      clearTimeout(this.timeoutID)
      this.round = 0
      this.inGame = false
      this.userList = []
      this.orderList = []
    },

    timer () {
      const start = new Date().getTime()
      this.timeoutID = setTimeout(() => {
        const end = new Date().getTime()
        console.log(end - start)
        this.gameOver()
      }, this.time)
    },

    random () {
      const num = 1 + Math.random() * (4 + 1 - 1)
      return Math.floor(num)
    }
  }

}
</script>

<style lang="scss">

</style>