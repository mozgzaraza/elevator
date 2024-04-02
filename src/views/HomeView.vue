<template>
  <div class="elevator">
    <ul class="elevator__floor-list">
      <li
        class="elevator__floor-item"
        v-for="(elevatorFloor, index) in elevatorFloors"
        :key="index"
        :style="{
          height: `calc(1 / ${floor} * 100%)`,
        }"
      ></li>
      <li
        class="elevator__floor-item"
        :style="{
          bottom: floorHeight * (cabinIndex - 1) + '%',
          height: `calc(1 / ${floor} * 100%)`,
        }"
      >
        <div class="elevator__cabin-info">
          <span class="elevator__cabin-span"> {{ cabinIndex }} </span>
          <span class="elevator__cabin-span"> {{ targetFloor[0] }} </span>
          <span
            class="elevator__cabin-span elevator__cabin-span--up"
            :class="{ 'elevator__cabin-span--active': direction === 1 }"
          >
          </span>
          <span
            class="elevator__cabin-span elevator__cabin-span--down"
            :class="{ 'elevator__cabin-span--active': direction === -1 }"
          >
          </span>
        </div>
      </li>
    </ul>
    <ul class="elevator__button-list">
      <li
        class="elevator__button-item"
        :class="{
          'elevator__button-item--active': targetFloor.includes(index + 1),
        }"
        v-for="(elevatorButton, index) in elevatorButtons"
        :key="index"
        @click="call(index)"
      >
        {{ index + 1 }}
        <span class="elevator__button"></span>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: "HomeView",
  data() {
    return {
      floor: 5, // кол-во этажей
      elevatorFloors: [], // список этажей
      elevatorButtons: [], // список кнопок
      floorHeight: "", // высота 1 этажа
      cabinIndex: 1, // этаж на котором находится кабина
      direction: null, // направление движения
      targetFloor: [], // список этажей на которые надо ехать
    };
  },

  created() {
    this.elevatorFloors = Array.from({ length: this.floor }); //генерируем список этажей после создания страницы
    this.elevatorButtons = Array.from({ length: this.floor }); //генерируем список кнопок после создания страницы
    this.floorHeight = 100 / this.floor; //генерируем высоту этажа после создания страницы
  },
  methods: {
    call(index) {
      this.targetFloor.push(index + 1);
      if (!this.direction) {
        this.move();
      }
    },
    move() {
      if (this.targetFloor.length === 0) {
        return;
      }

      const targetFloor = this.targetFloor[0];
      if (this.cabinIndex < targetFloor) {
        //определение движения в зависимости от того где выше целевой этаж или ниже
        this.direction = 1;
      } else if (this.cabinIndex > targetFloor) {
        this.direction = -1;
      } else {
        this.direction = 0;
      }

      if (this.direction !== 0) {
        this.movingInterval = setInterval(() => {
          //интервал движения по каждому этажу 1с / 1 этаж
          this.cabinIndex += this.direction;
          if (this.cabinIndex === this.targetFloor[0]) {
            clearInterval(this.movingInterval);
            this.direction = 0;
            setTimeout(() => {
              //3 секунды пауза при прибытии на нужный этаж
              this.targetFloor.shift();
              this.move();
            }, 3000);
          }
        }, 1000);
      }
    },
  },
};
</script>

<style lang="scss">
.elevator {
  display: flex;
  &__floor-list {
    position: relative;

    display: flex;
    flex-direction: column;
    justify-content: space-between;

    height: 100vh;
  }
  &__floor-item {
    width: 100px;
    background-color: red;

    border: 1px solid black;
    &:last-child {
      position: absolute;
      bottom: 0;

      transition: bottom 1s;
      background-color: green;
    }
  }
  &__cabin-info {
    display: flex;
    justify-content: space-between;
    flex-wrap: wrap;

    margin: 15% auto 0;
    max-width: 50px;

    background-color: white;
  }
  &__cabin-span {
    display: block;

    width: 100%;
    max-width: 50%;

    text-align: center;

    &--up,
    &--down {
      position: relative;

      opacity: 0.2;
      &:before {
        position: absolute;
        left: 0;
        right: 0;
        bottom: 0;
        top: 0;

        width: 25px;
        height: 25px;

        content: "";

        background-repeat: no-repeat;
        background-position: center;
        background-size: cover;
      }
    }
    &--active {
      opacity: 1;
    }
    &--up {
      &:before {
        background-image: url("../assets/images/icons/angle-up.svg");
      }
    }

    &--down {
      &:before {
        background-image: url("../assets/images/icons/angle-down.svg");
      }
    }
  }
  &__button-list {
    display: flex;
    flex-direction: column-reverse;
    justify-content: space-between;

    width: 100px;
  }

  &__button-item {
    position: relative;

    height: 100%;
  }

  &__button-item--active & {
    &__button {
      cursor: auto;
      background-color: red;
    }
  }
  &__button {
    position: absolute;
    top: 50%;
    left: 50%;

    display: block;

    height: 20px;
    width: 20px;

    cursor: pointer;
    transform: translate(-50%, -50%);

    border-radius: 50%;
    border: 1px solid black;

    &:after {
      position: absolute;
      top: 50%;
      left: 50%;

      height: 10px;
      width: 10px;

      content: "";
      transform: translate(-50%, -50%);

      background-color: black;
      border-radius: 50%;
    }
  }
}
</style>
