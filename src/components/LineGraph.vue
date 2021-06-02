<template>
  <canvas width="360" height="300"></canvas>
</template>

<script>
export default {
  name: 'LineGraph',

  data() {
    return {
      canvasWidth: 360,
      canvasHeight: 300,
      graphBoxMargin: 0,
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
        ['10일', 320],
        ['1일', 320],
        ['2일', 350],
        ['3일', 230],
        ['4일', 320],
        ['5일', 220],
        ['6일', 430],
        ['7일', 450],
        ['8일', 250],
        ['9일', 430],
        ['10일', 320],
        ['1일', 320],
        ['2일', 350],
        ['3일', 230],
        ['4일', 320],
        ['5일', 220],
        ['6일', 430],
        ['7일', 450],
        ['8일', 250],
        ['9일', 430],
        ['10일', 320],
        ['1일', 320],
        ['2일', 350],
        ['3일', 230],
        ['4일', 320],
        ['5일', 220],
        ['6일', 430],
        ['7일', 450],
        ['8일', 250],
        ['9일', 430],
        ['10일', 320],
        ['1일', 320],
        ['2일', 350],
        ['3일', 230],
        ['4일', 320],
        ['5일', 220],
        ['6일', 430],
        ['7일', 450],
        ['8일', 250],
        ['9일', 430],
        ['10일', 320],
      ],

      tpCache: [],

      baseStartIndex: null,
      baseEndIdnex: null,
      targetStartIndex: null,
      targetEndIndex: null,
      startIndex: null,
      endIndex: null,
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
      return this.graphBoxWidth / (this.endIndex - this.startIndex);
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
        this.canvasWidth,
        this.canvasHeight
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

      for (let i = this.startIndex; i < this.endIndex; i++) {
        // const [time, value] = element;
        const value = this.data[i][1];
        unitHeight =
          this.graphBoxMargin +
          this.graphBoxHeight -
          (value - this.floorValue) * this.heightRatio;

        this.ctx.lineTo(
          this.graphBoxMargin + this.unitWidth * (i - this.startIndex),
          unitHeight
        );
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

      for (let i = this.startIndex; i < this.endIndex; i++) {
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

      if (event.targetTouches.length === 1) {
        //
      } else if (event.targetTouches.length === 2) {
        let touches = event.targetTouches;

        touches.forEach(touch => {
          this.tpCache.push(touch);
        });

        let pointIndex1 = Math.round(
          (touches[0].clientX - this.graphBoxMargin) / this.unitWidth
        );
        let pointIndex2 = Math.round(
          (touches[1].clientX - this.graphBoxMargin) / this.unitWidth
        );

        this.targetStartIndex =
          Math.min(pointIndex1, pointIndex2) + this.startIndex;
        this.targetEndIndex =
          Math.max(pointIndex1, pointIndex2) + this.startIndex;

        console.log(
          this.targetStartIndex,
          this.targetEndIndex,
          'start, end target index'
        );
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
        this.baseEndIdnex = this.endIndex;
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
        let basePoint1 = -1;
        let basePoint2 = -1;

        for (let i = 0; i < this.tpCache.length; i++) {
          if (
            this.tpCache[i].identifier === event.targetTouches[0].identifier
          ) {
            basePoint1 = i;
          }

          if (
            this.tpCache[i].identifier === event.targetTouches[1].identifier
          ) {
            basePoint2 = i;
          }
        }

        if (
          basePoint1 >= 0 &&
          basePoint2 >= 0 &&
          this.endIndex - this.startIndex > 5
        ) {
          const diff1 = Math.abs(
            this.tpCache[basePoint1].clientX - event.targetTouches[0].clientX
          );

          const diff2 = Math.abs(
            this.tpCache[basePoint2].clientX - event.targetTouches[1].clientX
          );

          const totalDiff = Math.round(diff1 + diff2);

          console.log(totalDiff, 'totalDiff');
          const diffIndex = parseInt(totalDiff / 15);

          if (this.startIndex < this.targetStartIndex) {
            if (this.baseStartIndex + diffIndex > this.targetStartIndex) {
              this.startIndex = this.targetStartIndex;
            } else {
              this.startIndex = this.baseStartIndex + diffIndex;
            }
          }

          if (this.endIndex > this.targetEndIndex) {
            if (this.baseEndIdnex - diffIndex < this.targetEndIndex) {
              this.endIndex = this.targetEndIndex;
            } else {
              this.endIndex = this.baseEndIdnex - diffIndex;
            }
          }

          console.log(this.startIndex, this.endIndex);
        }
      }
    },
  },

  mounted() {
    this.ctx = this.$el.getContext('2d');
    this.startIndex = 0;
    this.endIndex = this.data.length - 1;
    this.baseStartIndex = 0;
    this.baseEndIdnex = this.data.length - 1;

    this.drawGraphBox();
    this.drawGraph();
    this.$el.ontouchstart = this.touchStartHandler;
    this.$el.ontouchmove = this.touchMoveHandler;
    this.$el.ontouchend = this.touchEndhandler;
  },

  watch: {
    startIndex: function() {
      this.ctx.clearRect(0, 0, this.canvasWidth, this.canvasHeight);
      this.drawGraphBox();
      this.drawGraph();
      this.$el.ontouchstart = this.touchStartHandler;
      this.$el.ontouchmove = this.touchMoveHandler;
      this.$el.ontocuhcancel = this.touchEndhandler;
      this.$el.ontouchend = this.touchEndhandler;
    },
  },
};
</script>

<style></style>
