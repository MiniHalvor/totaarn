<!-- src/routes/+page.svelte -->
<script>
  export let data;
</script>

<style>
.header {
  background-color: #f8d7da;
  padding: 10px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.header .logo {
  font-weight: bold;
  font-size: 1.2rem;
  color: #5a403b;
}

.header nav a {
  margin: 0 15px;
  text-decoration: none;
  color: #5a403b;
  font-weight: bold;
}

.container {
  text-align: center;
  padding: 50px 20px;
}

.container h1 {
  font-size: 2.5rem;
  margin-bottom: 50px;
}

.winner-loser-section {
  display: flex;
  justify-content: center;
  gap: 20px;
}

.winner-loser-section .box {
  background-color: #eaeaea;
  width: 200px;
  height: 200px;
}
</style>

<div class="header">
<div class="logo">To Tårn</div>
<nav>
  <a href="#">Toppliste</a>
  <a href="#">Events</a>
  <a href="#">Ressurser</a>
  <a href="#">Janus Carlsen</a>
</nav>
</div>

<div class="container">
<h1>Vinnere og Tapere</h1>
<div class="winner-loser-section">
  <div class="box"></div>
  <div class="box"></div>
</div>
</div>
