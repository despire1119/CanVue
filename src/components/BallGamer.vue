<template>
  <div>
    <canvas class="shaking-box" ref="shakingBox"></canvas>
    <div class="gift-box">
      <span class="gift-ball"></span>
    </div>
  </div>
</template>
<script>
export default {
  name: 'BallGamer',
  props: {

  },
  data(){
    return {
      balls:[],
      count: 0,
      limit: 5,
      maxRadius: 40,
      width: "300px",
      height: "100px",
      colors: ["#D90866", "#E87E0C", "#FF0000", "#8B0CE8", "#0D68FF"]
    }
  },
  mounted(){
    this.width = this.$refs.shakingBox.width
    this.height = this.$refs.shakingBox.height

    const canvas = document.querySelector("canvas")
    const ctx = canvas.getContext("2d")

    canvas.width = this.width
    canvas.height = this.height
    
    canvas.addEventListener("contextmenu",e => {
      if(!e) e = window.event
      e.preventDefault()
      this.balls = []
    },false)
    
    let animate = ()=>{
      ctx.clearRect(0, 0, this.width, this.height)

      if(this.count < this.limit){
        this.count++
        let ball = this.Ball(this.random(this.width),this.random(this.height))        
        this.balls.push(ball)
        this.count==4?console.log(this.balls):null
      }

      //draw ball
      for (const ball of this.balls) {
        let radius = ball.radius,
            radgradX = ball.x - radius*0.5,
            radgradY = ball.y - radius*0.5,
            radgradR1 = radius*0.02,
            radgradR2 = radius*2

        const radgrad = ctx.createLinearGradient(radgradX, radgradY, radgradR1, radgradX, radgradY, radgradR2)        
        radgrad.addColorStop(0, ball.color)
        radgrad.addColorStop(0.2, "#F7F7F7")
        radgrad.addColorStop(1, "#F7F7F7")

        ctx.fillStyle = radgrad
        ctx.shadowColor = "#403938"
        ctx.beginPath()
        ctx.arc(ball.x, ball.y, ball.radius, 0, 2 * Math.PI, false)
        ctx.shadowOffsetX = ball.radius * 0.08
        ctx.shadowOffsetY = ball.radius * 0.08
        ctx.shadowBlur = ball.radius * 0.2
        ctx.closePath()
        ctx.fill()
      }

      //ball move
      for(const ball of this.balls){
        // let ball = this.balls[i]
        if(ball.radius >= this.maxRadius){
          ball.x += ball.vx
          ball.y += ball.vy
          if (ball.x - ball.radius < 0 ) {
            ball.x = ball.radius; 
            ball.vx *= -1;
          }
          if (ball.x + ball.radius > this.width) {
            ball.x = this.width - ball.radius; 
            ball.vx *= -1;
          }
          if (ball.y - ball.radius < 0 ) {
            ball.y = ball.radius;
            ball.vy *= -1;
          }
          if (ball.y + ball.radius > this.height) {
            ball.y = this.height - ball.radius;
            ball.vy *= -1;
          }
        }
      }

      //ball collision
      for (var i = 0; i < this.balls.length; i++) {
        var ball1 = this.balls[i];
        for (var j = i + 1; j < this.balls.length; j++) {
          var ball2 = this.balls[j];
          if (ball1.radius + ball2.radius >= this.maxRadius * 2) {
            var dx = ball1.x - ball2.x;
            var dy = ball1.y - ball2.y;
            var dds = dx * dx + dy * dy;
            var dm = ball1.radius + ball2.radius;
            var dms = dm * dm;            
            if (dds < dms) {
              var angle = Math.atan2(dy, dx);
              var dist = Math.sqrt(dds);
              var depth = (dist - dm) / dist;
              
              ball1.x -= dx * depth * 0.5;
              ball1.y -= dy * depth * 0.5;
              ball2.x += dx * depth * 0.5;
              ball2.y += dy * depth * 0.5;

              var v1 = Math.sqrt(ball1.vx * ball1.vx + ball1.vy * ball1.vy);
              var v2 = Math.sqrt(ball2.vx * ball2.vx + ball2.vy * ball2.vy);

              var a1 = Math.atan2(ball1.vy, ball1.vx);
              var a2 = Math.atan2(ball2.vy, ball2.vx);

              var rvx1 = v1 * Math.cos(a1-angle);
              var rvy1 = v1 * Math.sin(a1-angle);
              var rvx2 = v2 * Math.cos(a2-angle);
              var rvy2 = v2 * Math.sin(a2-angle);

              var evx1 = ((ball1.m - ball2.m) * rvx1 + 2 * ball2.m * rvx2) / (ball1.m + ball2.m);
              var evx2 = ((ball2.m - ball1.m) * rvx2 + 2 * ball1.m * rvx1) / (ball1.m + ball2.m);
              var evy1 = rvy1;
              var evy2 = rvy2;

              ball1.vx =  Math.cos(angle) * evx1 + Math.cos(angle + Math.PI/2) * evy1;
              ball1.vy =  Math.sin(angle) * evx1 + Math.sin(angle + Math.PI/2) * evy1;
              ball2.vx =  Math.cos(angle) * evx2 + Math.cos(angle + Math.PI/2) * evy2;
              ball2.vy =  Math.sin(angle) * evx2 + Math.sin(angle + Math.PI/2) * evy2;
            }
          }
        }
      }
      requestAnimationFrame(animate) 
    }
    animate()
  },
  methods: {
    random: function(n){
      return Math.floor(Math.random()*n)
    },
    Ball: function(x,y){
      const colors = ["#D90866", "#E87E0C", "#8B0CE8", "#0D68FF"]
      let ball = new Object
      ball.x = x
      ball.y = y
      ball.vx = 3*(Math.random() + Math.random() + Math.random() - 1.5)
      ball.vy = 3*(Math.random() + Math.random() + Math.random() - 1.5)
      ball.radius = 25
      ball.m = ball.radius*0.5
      ball.color = colors[this.random(colors.length)]
      return ball
    }
  }
}
</script>
<style lang="stylus" scoped>
  .shaking-box
    width 670px
    height 440px
    margin 20px auto
    // border-top-right-radius 220px
    // border-top-left-radius 220px
  .gift-box {
    position absolute
    top 638px
    left 50%
    width 180px
    height 180px
    border-radius 90px
    margin-left -90px
    // overflow hidden
  }
  .gift-ball {
    display block    
    width 100%
    height 100%
    border-radius 100%
    background-image url('../assets/img/ball1.png')
    background-size 100%
    background-repeat no-repeat
    animation: bounce 1000ms linear both;
  }
  @keyframes bounce { 
    0% { transform: matrix3d(1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0, 0, -100, 0, 1); }
    2.92% { transform: matrix3d(1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0, 0, -45.073, 0, 1); }
    3.37% { transform: matrix3d(1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0, 0, -38.29, 0, 1); }
    3.47% { transform: matrix3d(1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0, 0, -36.865, 0, 1); }
    4.58% { transform: matrix3d(1, 0, 0, 0, 0, 2.061, 0, 0, 0, 0, 1, 0, 0, -22.883, 0, 1); }
    5.69% { transform: matrix3d(1, 0, 0, 0, 0, 2.321, 0, 0, 0, 0, 1, 0, 0, -12.184, 0, 1); }
    5.76% { transform: matrix3d(1, 0, 0, 0, 0, 2.32, 0, 0, 0, 0, 1, 0, 0, -11.589, 0, 1); }
    7.41% { transform: matrix3d(1, 0, 0, 0, 0, 1.99, 0, 0, 0, 0, 1, 0, 0, -1.268, 0, 1); }
    7.51% { transform: matrix3d(1, 0, 0, 0, 0, 1.961, 0, 0, 0, 0, 1, 0, 0, -0.818, 0, 1); }
    7.88% { transform: matrix3d(1.062, 0, 0, 0, 0, 1.771, 0, 0, 0, 0, 1, 0, 0, 0.669, 0, 1); }
    8.68% { transform: matrix3d(1.181, 0, 0, 0, 0, 1.408, 0, 0, 0, 0, 1, 0, 0, 3.215, 0, 1); }
    10.03% { transform: matrix3d(1.333, 0, 0, 0, 0, 0.982, 0, 0, 0, 0, 1, 0, 0, 5.618, 0, 1); }
    10.85% { transform: matrix3d(1.398, 0, 0, 0, 0, 0.822, 0, 0, 0, 0, 1, 0, 0, 6.204, 0, 1); }
    11.53% { transform: matrix3d(1.439, 0, 0, 0, 0, 0.732, 0, 0, 0, 0, 1, 0, 0, 6.331, 0, 1); }
    12.22% { transform: matrix3d(1.469, 0, 0, 0, 0, 0.672, 0, 0, 0, 0, 1, 0, 0, 6.206, 0, 1); }
    14.18% { transform: matrix3d(1.501, 0, 0, 0, 0, 0.612, 0, 0, 0, 0, 1, 0, 0, 5.018, 0, 1); }
    14.37% { transform: matrix3d(1.501, 0, 0, 0, 0, 0.612, 0, 0, 0, 0, 1, 0, 0, 4.868, 0, 1); }
    19.23% { transform: matrix3d(1.371, 0, 0, 0, 0, 0.737, 0, 0, 0, 0, 1, 0, 0, 1.285, 0, 1); }
    20.01% { transform: matrix3d(1.338, 0, 0, 0, 0, 0.763, 0, 0, 0, 0, 1, 0, 0, 0.908, 0, 1); }
    23.05% { transform: matrix3d(1.211, 0, 0, 0, 0, 0.856, 0, 0, 0, 0, 1, 0, 0, 0.012, 0, 1); }
    25.75% { transform: matrix3d(1.114, 0, 0, 0, 0, 0.923, 0, 0, 0, 0, 1, 0, 0, -0.236, 0, 1); }
    26.94% { transform: matrix3d(1.078, 0, 0, 0, 0, 0.947, 0, 0, 0, 0, 1, 0, 0, -0.253, 0, 1); }
    31.58% { transform: matrix3d(0.987, 0, 0, 0, 0, 1.009, 0, 0, 0, 0, 1, 0, 0, -0.135, 0, 1); }
    31.73% { transform: matrix3d(0.986, 0, 0, 0, 0, 1.01, 0, 0, 0, 0, 1, 0, 0, -0.131, 0, 1); }
    37.32% { transform: matrix3d(0.958, 0, 0, 0, 0, 1.029, 0, 0, 0, 0, 1, 0, 0, -0.01, 0, 1); }
    38.15% { transform: matrix3d(0.958, 0, 0, 0, 0, 1.029, 0, 0, 0, 0, 1, 0, 0, -0.003, 0, 1); }
    42.35% { transform: matrix3d(0.969, 0, 0, 0, 0, 1.022, 0, 0, 0, 0, 1, 0, 0, 0.01, 0, 1); }
    48.9% { transform: matrix3d(0.99, 0, 0, 0, 0, 1.007, 0, 0, 0, 0, 1, 0, 0, 0.003, 0, 1); }
    57.77% { transform: matrix3d(1.003, 0, 0, 0, 0, 0.998, 0, 0, 0, 0, 1, 0, 0, 0, 0, 1); }
    60.47% { transform: matrix3d(1.004, 0, 0, 0, 0, 0.998, 0, 0, 0, 0, 1, 0, 0, 0, 0, 1); }
    69.36% { transform: matrix3d(1.001, 0, 0, 0, 0, 0.999, 0, 0, 0, 0, 1, 0, 0, 0, 0, 1); }
    83.61% { transform: matrix3d(1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 1); }
    100% { transform: matrix3d(1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 1); }
  }
</style>
