<template>
  <div class="elevator">
    <ul class="elevator__floor-list">
      <li
        class="elevator__floor-item"
        v-for="(elevatorFloor, index) in elevatorFloors"
        :key="index"
        :style="{
          bottom: floorHeight * (cabinIndex - 1) + '%',
          height: `calc(1 / ${floor} * 100%)`,
        }"
      ></li>
    </ul>
    <ul class="elevator__button-list">
      <li
        class="elevator__button-item"
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
      floor: 5,
      elevatorFloors: [],
      elevatorButtons: [],
      floorHeight: "",
      cabinIndex: 1,
      direction: null,
      targetFloor: null,
    };
  },

  created() {
    this.elevatorFloors = Array.from({ length: this.floor + 1 });
    this.elevatorButtons = Array.from({ length: this.floor });
    this.floorHeight = 100 / this.floor;
  },
  methods: {
    call(index) {
      this.targetFloor = index + 1;
      if (this.cabinIndex < this.targetFloor) {
        this.direction = 1;
      } else if (this.cabinIndex > this.targetFloor) {
        this.direction = -1;
      } else {
        this.direction = 0;
      }
      this.move();
    },
    move() {
      if (this.direction !== 0) {
        this.movingInterval = setInterval(() => {
          this.cabinIndex += this.direction;
          if (this.cabinIndex === this.targetFloor) {
            clearInterval(this.movingInterval);
            this.direction = null;
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

    background-color: red;
  }
  &__floor-item {
    width: 100px;

    border: 1px solid black;
    &:last-child {
      position: absolute;
      bottom: 0;

      transition: bottom 1s;
      background-color: green;
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
