<template>
  <div ref="clockContainer"></div>
</template>

<script>
import * as d3 from 'd3';

export default {
  name: 'UtcClock',
  mounted() {
    this.drawClock();
    this.interval = setInterval(() => {
      this.updateClock();
    }, 1000); // Update every minute
  },
  beforeDestroy() {
    clearInterval(this.interval);
  },
  methods: {
    drawClock() {
      const width = 400, height = 400;
      const clockRadius = 195;
      const hourLength = 180; // Length of the hour hand
      const minLength = 190; // Length of the minute hand
      const secLength = 190; // Length of the second hand

      const svg = d3.select(this.$refs.clockContainer)
          .append('svg')
          .attr('width', width)
          .attr('height', height);

      // Clock circle
      svg.append('circle')
          .attr('cx', width / 2)
          .attr('cy', height / 2)
          .attr('r', clockRadius)
          .style('fill', 'none')
          .style('stroke', 'black');

      // Hour markings
      for (let i = 0; i < 24; i++) {
        const angle = (i - 3) * (360 / 24) * Math.PI / 180; // Adjust starting point and convert to radians
        const lineLength = i % 3 === 0 ? 20 : 10; // Longer lines for every 3 hours
        svg.append('line')
            .attr('x1', width / 2 + (clockRadius - lineLength) * Math.cos(angle))
            .attr('y1', height / 2 + (clockRadius - lineLength) * Math.sin(angle))
            .attr('x2', width / 2 + clockRadius * Math.cos(angle))
            .attr('y2', height / 2 + clockRadius * Math.sin(angle))
            .style('stroke', 'black');
      }

      // Initial hands setup
      svg.append('line') // Hour hand
          .attr('id', 'hourHand')
          .attr('x1', width / 2)
          .attr('y1', height / 2)
          .attr('x2', width / 2) // Initial x2, y2 will be set by updateClock
          .attr('y2', height / 2 - hourLength)
          .style('stroke', 'black')
          .style('stroke-width', 6);

      svg.append('line') // Minute hand
          .attr('id', 'minHand')
          .attr('x1', width / 2)
          .attr('y1', height / 2)
          .attr('x2', width / 2)
          .attr('y2', height / 2 - minLength)
          .style('stroke', 'blue')
          .style('stroke-width', 4);

      svg.append('line') // Second hand
          .attr('id', 'secHand')
          .attr('x1', width / 2)
          .attr('y1', height / 2)
          .attr('x2', width / 2)
          .attr('y2', height / 2 - secLength)
          .style('stroke', 'red')
          .style('stroke-width', 2);

      this.updateClock(); // Initialize hands positions based on current time
    },
    updateClock() {
      const now = new Date();
      const hours = now.getUTCHours();
      const minutes = now.getUTCMinutes();
      const seconds = now.getUTCSeconds();

      // Calculate angles
      const hourAngle = (hours % 12) * 30 + minutes * 0.5; // 360/12 = 30 degrees per hour, 0.5 degree per minute
      const minuteAngle = minutes * 6; // 360/60 = 6 degrees per minute
      const secondAngle = seconds * 6; // 360/60 = 6 degrees per second

      // Update hand positions
      d3.select('#hourHand')
          .attr('transform', `rotate(${hourAngle}, 200, 200)`);
      d3.select('#minHand')
          .attr('transform', `rotate(${minuteAngle}, 200, 200)`);
      d3.select('#secHand')
          .attr('transform', `rotate(${secondAngle}, 200, 200)`);
    }
}};
</script>
