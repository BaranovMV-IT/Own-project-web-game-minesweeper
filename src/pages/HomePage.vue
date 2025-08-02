<template>
  <div class="block">
    <div class="block__display">
      <div class="block__display__info">
        <div class="block__display__info__counter">
          <img class="block__display__info__counter__image" src="" alt="">
        </div>
        <button class="block__display__info__button">
          <img class="block__display__info__button__image" src="" alt="reset">
        </button>
        <div class="block__display__info__counter">
          <img class="block__display__info__counter__image" src="" alt="number">
        </div>
      </div>
      <div class="block__display__board">
        <div class="block__display__board__cell">
          <img class="block__display__board__cell__image" src="" alt="">
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
      
    }
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