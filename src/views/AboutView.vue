<script>
import { RouterLink } from 'vue-router'
</script>
<template>
  <nav>
    <RouterLink to="/">Play!!</RouterLink>
  </nav>
  <div class="about">
    <ol>
      <li>
        click "Start!"
      </li>
      <li>
        refresh if clicked a tile before hitting "Start!"
      </li>
      <li>
        click a tile inside the board
      </li>
      <li>
        to mark a flag click the flag button and click a tile
      </li>
      <li>
        to remove the flag click the same tile while flag is still enabled
      </li>
      <li>
        to change the number of mines input it in the "Mines🎀" input
      </li>
      <li>
        to change the amount of columns input in the first "Grid" input
      </li>
      <li>
        to change the amount of rows input in the second "Grid" input
      </li>
      <li>
        to change your difficulty select from options "Hard" or "Easy" next to the "Start!" button
      </li>
      <li>
        on "Hard" mode if you don't win in less that 30 seconds, you lose
      </li>
      <li>
        refresh to start again
      </li>
      <li>
        sometimes game is over on the first move sorry:((
      </li>
    </ol>
  </div>
</template>

<style>
.about{
  display: flex;
  flex-direction: row;
  justify-content: center;
}
ol{
  text-align: left;
}
</style>