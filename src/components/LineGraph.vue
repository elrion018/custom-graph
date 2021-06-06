<template>
  <div>
    <canvas ref="graph" width="360" height="300"></canvas>
    <x-axis
      :timeData="timeData"
      :unitWidth="unitWidth"
      :startIndex="startIndex"
    ></x-axis>
  </div>
</template>

<script>
import XAxis from './XAxis.vue';

export default {
  name: 'LineGraph',
  components: {
    XAxis,
  },

  data() {
    return {
      ctx: null,

      canvasWidth: 340,
      canvasHeight: 300,
      graphBoxMargin: 10,
      graphBoxColor: 'green',
      graphColor: 'green',
      data: [
        ['1일', 320],
        ['2일', 350],
        ['3일', 230],
        ['4일', 320],
        ['5일', 220],
        ['6일', 430],
        ['7일', 450],
        ['8일', 250],
        ['9일', 430],
        ['10일', 420],
        ['11일', 520],
        ['12일', 650],
        ['13일', 730],
        ['14일', 820],
        ['15일', 920],
        ['16일', 1030],
        ['17일', 1150],
        ['18일', 1250],
        ['19일', 1330],
        ['20일', 1420],
        ['21일', 1520],
        ['22일', 1650],
        ['23일', 1730],
        ['24일', 1820],
        ['25일', 1920],
        ['26일', 2030],
        ['27일', 2150],
        ['28일', 2250],
        ['29일', 2330],
        ['30일', 2420],
        ['31일', 2520],
        ['32일', 2650],
        ['33일', 2730],
        ['34일', 2820],
        ['35일', 2920],
        ['36일', 3030],
        ['37일', 3150],
        ['38일', 3250],
        ['39일', 3330],
        ['40일', 3420],
        ['41일', 3520],
        ['42일', 3650],
        ['43일', 3730],
        ['44일', 3820],
        ['45일', 3920],
        ['46일', 4030],
        ['47일', 450],
        ['48일', 4250],
        ['49일', 4430],
        ['50일', 5320],
      ],

      tpCache: [],
      baseStartIndex: null,
      baseEndIndex: null,
      targetStartIndex: null,
      targetEndIndex: null,
      startIndex: 0,
      endIndex: null,

      timeData: [],
    };
  },

  computed: {
    graphBoxWidth() {
      return this.canvasWidth - 2 * this.graphBoxMargin;
    },

    graphBoxHeight() {
      return this.canvasHeight - 2 * this.graphBoxMargin;
    },

    ceilValue() {
      return this.getCeilAndFloorValue().ceilValue;
    },

    floorValue() {
      return this.getCeilAndFloorValue().floorValue;
    },

    unitWidth() {
      return this.graphBoxWidth / (this.endIndex - this.startIndex - 1);
    },

    heightRatio() {
      return this.graphBoxHeight / (this.ceilValue - this.floorValue);
    },
  },

  methods: {
    drawGraphBox() {
      this.ctx.strokeStyle = this.graphBoxColor;
      this.ctx.strokeRect(
        this.graphBoxMargin,
        this.graphBoxMargin,
        this.graphBoxWidth,
        this.graphBoxHeight
      );
    },

    drawGraph() {
      this.ctx.strokeStyle = this.graphColor;
      this.ctx.beginPath();
      this.ctx.moveTo(
        this.graphBoxMargin,
        this.graphBoxMargin + this.graphBoxHeight
      );

      let unitHeight = null;

      let interval = Math.floor((this.endIndex - this.startIndex) / 10);

      if (interval === 1) {
        interval = 2;
      } else if (interval === 0) {
        interval = 1;
      }

      console.log(this.endIndex - this.startIndex);
      console.log(interval);

      for (let i = this.startIndex; i < this.endIndex + 1; i++) {
        const [time, value] = this.data[i];
        unitHeight =
          this.graphBoxMargin +
          this.graphBoxHeight -
          (value - this.floorValue) * this.heightRatio;

        this.ctx.lineTo(
          this.graphBoxMargin + this.unitWidth * (i - this.startIndex),
          unitHeight
        );

        if (i % interval === 0) {
          this.timeData.push([time, i]);
        }
      }

      this.ctx.lineTo(
        this.graphBoxMargin + this.graphBoxWidth,
        this.graphBoxMargin + this.graphBoxHeight
      );
      this.ctx.closePath();
      this.ctx.stroke();
    },

    getCeilAndFloorValue() {
      let ceilValue = Number.MIN_VALUE;
      let floorValue = Number.MAX_VALUE;

      for (let i = this.startIndex; i < this.endIndex + 1; i++) {
        let value = this.data[i][1];
        ceilValue = Math.max(ceilValue, value);
        floorValue = Math.min(floorValue, value);
      }

      const diff = ceilValue - floorValue;
      ceilValue = ceilValue + 5 * 10 ** (parseInt(Math.log10(diff)) - 1);
      floorValue = floorValue - 5 * 10 ** (parseInt(Math.log10(diff)) - 1);

      if (floorValue < 0) {
        floorValue = 0;
      }

      return { ceilValue, floorValue };
    },

    touchStartHandler(event) {
      event.preventDefault();
      console.log('start');

      const touches = event.targetTouches;
      this.tpCache = [];

      touches.forEach(touch => {
        this.tpCache.push(touch);
      });

<<<<<<< HEAD
      if (event.targetTouches.length === 2) {
=======
      if (event.targetTouches.length === 1) {
        //
        // const pointIndex1 = Math.round(
        //   (touches[0].clientX - this.graphBoxMargin) / this.unitWidth
        // );
      } else if (event.targetTouches.length === 2) {
>>>>>>> ded532a9efda05cba6274c55bf8efcfc0d0110d4
        const pointIndex1 = Math.round(
          (touches[0].clientX - this.graphBoxMargin) / this.unitWidth
        );
        const pointIndex2 = Math.round(
          (touches[1].clientX - this.graphBoxMargin) / this.unitWidth
        );

        this.targetStartIndex =
          Math.min(pointIndex1, pointIndex2) + this.startIndex;
        this.targetEndIndex =
          Math.max(pointIndex1, pointIndex2) + this.startIndex;
      }
    },

    touchMoveHandler(event) {
      event.preventDefault();

      this.handlePinchZomm(event);
    },

    touchEndhandler(event) {
      event.preventDefault();

      if (event.targetTouches.length === 0) {
        console.log('touch end');

        this.baseStartIndex = this.startIndex;
        this.baseEndIndex = this.endIndex;
        this.targetStartIndex = null;
        this.targetEndIndex = null;
        this.tpCache = [];
      }
    },

    handlePinchZomm(event) {
      if (
        event.targetTouches.length === 2 &&
        event.changedTouches.length === 2
      ) {
        let leftBasePointClientX = -1;
        let rightBasePointClientX = -1;
        let leftBasePoint = -1;
        let rightBasePoint = -1;

        for (let i = 0; i < this.tpCache.length; i++) {
          if (
            this.tpCache[i].identifier === event.targetTouches[0].identifier
          ) {
            leftBasePoint = i;
            leftBasePointClientX = this.tpCache[i].clientX;
          }

          if (
            this.tpCache[i].identifier === event.targetTouches[1].identifier
          ) {
            rightBasePoint = i;
            rightBasePointClientX = this.tpCache[i].clientX;
          }
        }

        if (leftBasePointClientX > rightBasePointClientX) {
          const tempPoint = leftBasePoint;
          leftBasePoint = rightBasePoint;
          rightBasePoint = tempPoint;
        }

        if (leftBasePoint >= 0 && rightBasePoint >= 0) {
          const leftDiff =
            this.tpCache[leftBasePoint].clientX -
            event.targetTouches[leftBasePoint].clientX;

          const rightDiff =
            this.tpCache[rightBasePoint].clientX -
            event.targetTouches[rightBasePoint].clientX;

          const totalDiff = Math.abs(leftDiff) + Math.abs(rightDiff);
          const diffIndex = Math.round(totalDiff / 20);

          if (
            leftDiff > 0 &&
            rightDiff < 0 &&
            this.endIndex - this.startIndex > 5
          ) {
            if (this.startIndex < this.targetStartIndex) {
              if (this.baseStartIndex + diffIndex > this.targetStartIndex) {
                this.startIndex = this.targetStartIndex;
              } else {
                this.startIndex = this.baseStartIndex + diffIndex;
              }
            }

            if (this.endIndex > this.targetEndIndex) {
              if (this.baseEndIndex - diffIndex < this.targetEndIndex) {
                this.endIndex = this.targetEndIndex;
              } else {
                this.endIndex = this.baseEndIndex - diffIndex;
              }
            }
          } else if (leftDiff < 0 && rightDiff > 0) {
            if (this.startIndex > 0) {
              if (this.baseStartIndex - diffIndex < 0) {
                this.startIndex = 0;
              } else {
                this.startIndex = this.baseStartIndex - diffIndex;
              }
            }

            if (this.endIndex < this.data.length - 1) {
              if (this.baseEndIndex + diffIndex > this.data.length - 1) {
                this.endIndex = this.data.length - 1;
              } else {
                this.endIndex = this.baseEndIndex + diffIndex;
              }
            }
          }
        }
      } else if (
        event.targetTouches.length === 1 &&
        event.changedTouches.length === 1
      ) {
        const diff = this.tpCache[0].clientX - event.targetTouches[0].clientX;
        const diffIndex = Math.round(Math.abs(diff) / 20);

        if (diff > 0 && this.baseEndIndex + diffIndex <= this.data.length - 1) {
<<<<<<< HEAD
=======
          //

>>>>>>> ded532a9efda05cba6274c55bf8efcfc0d0110d4
          this.startIndex = this.baseStartIndex + diffIndex;
          this.endIndex =
            this.baseEndIndex + diffIndex > this.data.length - 1
              ? this.data.length - 1
              : this.baseEndIndex + diffIndex;
        }

        if (diff < 0 && this.baseStartIndex - diffIndex >= 0) {
<<<<<<< HEAD
=======
          //

>>>>>>> ded532a9efda05cba6274c55bf8efcfc0d0110d4
          this.startIndex =
            this.baseStartIndex - diffIndex < 0
              ? 0
              : this.baseStartIndex - diffIndex;
          this.endIndex = this.baseEndIndex - diffIndex;
        }
      }
    },
  },

  mounted() {
    this.ctx = this.$refs.graph.getContext('2d');
    this.startIndex = 0;
    this.endIndex = this.data.length - 1;
    this.baseStartIndex = 0;
    this.baseEndIndex = this.data.length - 1;

    this.drawGraphBox();
    this.drawGraph();

    this.$refs.graph.ontouchstart = this.touchStartHandler;
    this.$refs.graph.ontouchmove = this.touchMoveHandler;
    this.$refs.graph.ontouchend = this.touchEndhandler;
  },

  watch: {
    startIndex() {
      this.timeData = [];
      this.ctx.clearRect(0, 0, this.canvasWidth, this.canvasHeight);
      this.drawGraphBox();
      this.drawGraph();
      this.$refs.graph.ontouchstart = this.touchStartHandler;
      this.$refs.graph.ontouchmove = this.touchMoveHandler;
      this.$refs.graph.ontocuhcancel = this.touchEndhandler;
      this.$refs.graph.ontouchend = this.touchEndhandler;
    },
  },
};
</script>

<style></style>
