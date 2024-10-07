<template>
  	<div id="app">
		<div class="jumbotron logo">
			<h1>
				Nested Draggable Test
			</h1>
			<img style="margin-top: 3rem;" src="./assets/logo.png">
		</div>
    	<DraggableList />
  	</div>
</template>

<script>
import DraggableList from "./components/DraggableList";

export default {
  name: "app",
  components: {
    DraggableList
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}

#app .logo {
  text-align: center;
}
</style>
