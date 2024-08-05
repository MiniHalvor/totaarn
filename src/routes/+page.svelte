<!-- src/routes/+page.svelte -->
<script>
    export let data;
  </script>
  
  <style>
    .container {
      max-width: 800px;
      margin: 0 auto;
      padding: 2rem;
      text-align: center;
    }
  
    img {
      max-width: 100%;
      height: auto;
    }
  </style>
  
  <div class="container">
    <h1>Velkommen til Janus: To tårn</h1>
    <p>Bli med på turnering</p>
    <img src="/chess-club.jpg" alt="Chess Club">
  </div>
  