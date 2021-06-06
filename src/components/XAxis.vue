<template>
  <canvas ref="xaxis" width="360" height="50"></canvas>
</template>

<script>
export default {
  name: 'XAxis',

  data() {
    return {
      ctx: null,
      canvasWidth: 360,
      canvasHeight: 50,
    };
  },

  props: {
    timeData: {
      type: Array,
      required: true,
    },

    unitWidth: {
      type: Number,
      required: true,
    },

    startIndex: {
      type: Number,
      required: true,
    },
  },

  methods: {
    writeXAxis() {
      console.log(this.timeData);

      this.timeData.forEach(([time, index]) => {
        this.ctx.fillText(
          `${time}`,
          10 +
            this.unitWidth * (index - this.startIndex) -
            this.ctx.measureText(`${time}`).width / 2,
          20
        );
      });
    },
  },

  mounted() {
    this.ctx = this.$refs.xaxis.getContext('2d');
  },

  watch: {
    timeData() {
      this.ctx.clearRect(0, 0, this.canvasWidth, this.canvasHeight);
      this.writeXAxis();
    },
  },
};
</script>

<style></style>
