<template>
  <div class="block">
    <div class="block__display">
      <div class="block__display__info">
        <div class="block__display__info__counter">
          <img 
            class="block__display__info__counter__image"
            v-for="number in Object.keys(mineCounter)"
            :src="'../assets/img/numbers/' + mineCounter[number] + '.png'" 
            :alt="mineCounter[number]"
            :key="2 ** (mineCounter[number] == '-' ? 0 : mineCounter[number]) + number"
          >
        </div>
        <button 
          class="block__display__info__button"
          @click="resetGame()"
        >
          <img 
            class="block__display__info__button__image" 
            :src="'../assets/img/icons/' + ((isGameOver && minesLeft != 0) ? 'unsmile' : 'smile') + '.png'" 
            alt="reset"
          >
        </button>
        <div class="block__display__info__counter">
          <img 
            class="block__display__info__counter__image"
            v-for="number in transformNumber(time)"
            :src="'../assets/img/numbers/' + number + '.png'" 
            :alt="number"
            :key="number"
          >
        </div>
      </div>
      <div class="block__display__board">
        <div 
          class="block__display__board__cell"
          v-for="cell in Object.keys(cellData)"
          :key="cell"
          :class="{
            hidden__cell: ((!isGameOver && (cellData[cell].status == 'hidden' || cellData[cell].status == 'flagged')) || (isGameOver && cellData[cell].status == 'flagged' && cellData[cell].value == -1) || (isGameOver && cellData[cell].status == 'hidden' && cellData[cell].value != -1)),
            exploded__cell: cellData[cell].status == 'mine_exploded'
          }"
          :style="{color: setColorByNumber(cellData[cell].value)}"
        >
          {{ (cellData[cell].status == "opened" && cellData[cell].value > 0) ? cellData[cell].value : "" }}
          <img 
            class="block__display__board__cell__image"
            :src="'../assets/img/icons/' + (cellData[cell].status == 'flagged' ? ((isGameOver && cellData[cell].value != -1) ? 'empty.png' : 'flag.png') : 'mine.png')" 
            alt="*"
            v-if="
              cellData[cell].status == 'flagged' || 
              cellData[cell].status == 'mine_exploded' || 
              (isGameOver && cellData[cell].value == -1) ||
              (isGameOver && cellData[cell].status == 'flagged' && cellData[cell].value == 0)
            "
          >
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HomePage',
  data(){
    return {
      cellData: {},
      difficulty: 'easy',
      time: 0,
      rows: 0,
      columns: 0,
      mineCount: 0,
      timeInterval: null,
      isGameStarted: false,
      isGameOver: false
    }
  },
  methods: {
    setGameDataByDifficulty(){
      switch(this.difficulty){
        case 'easy':
          this.columns = 9;
          this.rows = 9;
          this.mineCount = 10;
          break;
      }
    },
    fillCellData(){
      let obj = {};

      for(let row=1; row<=this.rows; row++){
        for(let col=1; col<=this.columns; col++){
          const id = row.toString() + "," + col.toString();
          obj[id] = {value: 0, status: "hidden"};
        }
      }

      this.cellData = obj;
    },
    fillMinesInCells(){
      let obj = {...this.cellData};

      let counter = 0;
      const chance = Number((this.mineCount / (this.rows * this.columns - 4)).toFixed(4));

      while(counter != this.mineCount){
        const id = String(Math.floor(Math.random() * (this.rows - 1 + 1)) + 1) + "," + String(Math.floor(Math.random() * (this.columns - 1 + 1)) + 1);
          
        if(id != "1,1" && id != "1,2" && id != "2,1" && id != "2,2"){
          if(obj[id].value == 0){
            if(this.getChance(chance)){
              obj[id].value = -1;
              counter++
            }
          }
        }
      }

      this.cellData = obj;
    },
    fillNumbersInCells(){
      let obj = {...this.cellData};

      for(let row=1; row<=this.rows; row++){
        for(let col=1; col<=this.columns; col++){
          const id = row.toString() + "," + col.toString();
          if(obj[id]?.value == -1){
            for(let i = row - 1; i <= row + 1; i++){
              for(let n = col - 1; n <= col + 1; n++){
                const checkId = i.toString() + "," + n.toString();
                if(obj[checkId]?.value >= 0){
                  obj[checkId].value = obj[checkId].value + 1;
                }
              }
            }
          }
        }
      }

      this.cellData = obj;
    },
    setColorByNumber(number){
      switch(number){
        case 1:
          return 'rgb(0,0,255)'
        case 2:
          return 'rgb(0,123,0)'
        case 3:
          return 'rgb(255,0,0)'
        case 4:
          return 'rgb(0,0,128)'
        case 5:
          return 'rgb(128,0,0)'
        default:
          return 'rgb(128,0,0)'
      }
    },
    getChance(chance){
      if(chance >= 0 && chance <= 100){
        if(chance == 0 || chance == 100){
          return chance ? true : false
        } else {
          const partition = chance / 100;
          return Math.random() < partition ? true : false
        }
      }
    },
    startTimer(){
      this.timeInterval = setInterval(() => {this.time += 1}, 1000);
      this.isGameStarted = true;
    },
    stopTimer(){
      clearInterval(this.timeInterval);
      this.isGameStarted = false;
    },
    transformNumber(number){
      if(number >= 0){
        number = number > 999 ? 999 : number;
        number = ('0'.repeat(3 - String(number).length) + number);
      } else {
        number = number < -99 ? -99 : number;
        number = ('-' + '0'.repeat(2 - String(number * -1).length) + (number * -1));
      }
      
      return number.split("")
    },
    resetGame(){
      if(this.isGameStarted){
        this.stopTimer();
        this.time = 0;
        this.isGameOver = false;
        this.fillCellData();
        this.fillMinesInCells();
        this.fillNumbersInCells();
      }
    }
  },
  computed: {
    minesLeft(){
      return this.mineCount - Object.keys(this.cellData).filter(id => this.cellData[id].status == "flagged").length
    },
    mineCounter(){
      return this.transformNumber(this.minesLeft)
    }
  },
  beforeMount(){
    this.setGameDataByDifficulty();
    this.fillCellData();
    this.fillMinesInCells();
    this.fillNumbersInCells();
  }
}
</script>

<style lang="scss" scoped>
.block {
  display: flex;
  flex-direction: column;
  width: fit-content;
  height: fit-content;
  &__display {
    display: flex;
    flex-direction: column;
    width: 436px; //436x553
    aspect-ratio: 1/1.2684;
    background-color: rgb(189,189,189);
    border-width: 5px;
    border-style: solid;
    border-top-color: rgb(255,255,255);
    border-left-color: rgb(255,255,255);
    border-right-color: rgb(123,123,123);
    border-bottom-color: rgb(123,123,123);
    padding: 16px;
    &__info {
      display: flex;
      width: 100%;
      height: 101px;
      border-width: 5px;
      border-style: solid;
      border-top-color: rgb(123,123,123);
      border-left-color: rgb(123,123,123);
      border-right-color: rgb(255,255,255);
      border-bottom-color: rgb(255,255,255);
      margin-bottom: 16px;
      padding: 0 14px;
      justify-content: space-between;
      align-items: center;
      &__counter {
        height: 61px;
        max-height: 61px;
        aspect-ratio: 1.7 / 1;
        &__image {
          height: 100%;
        }
      }
      &__button {
        display: flex;
        justify-content: center;
        align-items: center;
        width: 69px;
        max-width: 69px;
        height: 69px;
        max-height: 69px;
        background-color: rgb(189,189,189);
        border-width: 8px;
        border-style: solid;
        border-top-color: rgb(255,255,255);
        border-left-color: rgb(255,255,255);
        border-right-color: rgb(123,123,123);
        border-bottom-color: rgb(123,123,123);
        cursor: pointer;
        &__image {
          height: 97.5%;
        }
      }
    }
    &__board {
      display: flex;
      flex-wrap: wrap;
      width: 100%;
      flex-grow: 1;
      border-width: 5px;
      border-style: solid;
      border-top-color: rgb(123,123,123);
      border-left-color: rgb(123,123,123);
      border-right-color: rgb(255,255,255);
      border-bottom-color: rgb(255,255,255);
      &__cell {
        display: flex;
        justify-content: center;
        align-items: center;
        width: 42.6px;
        max-width: 42.6px;
        height: 42.6px;
        max-height: 42.6px;
        border-width: 3px;
        border-style: solid;
        border-top-color: rgb(123,123,123);
        border-left-color: rgb(123,123,123);
        border-right-color: transparent;
        border-bottom-color: transparent;
        font-size: 35px;
        font-weight: 800;
        line-height: 0px;
        cursor: pointer;
        &__image {
          width: 97.5%;
        }
      }
    }
  }
}

.hidden__cell {
  border-width: 5px;
  border-top-color: rgb(255,255,255);
  border-left-color: rgb(255,255,255);
  border-right-color: rgb(123,123,123);
  border-bottom-color: rgb(123,123,123);
}

.exploded__cell {
  background-color: rgb(255,0,0);
  border-top-color: rgb(255,0,0);
  border-left-color: rgb(255,0,0);
}
</style>