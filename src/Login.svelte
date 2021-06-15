<script>
  import { createEventDispatcher } from "svelte";

  const dispatch = createEventDispatcher();

  const login = () => dispatch("login", true);
</script>

<style>
  button {
    margin-top: 1em;
  }
</style>

<button class="button is-primary" on:click={login}>Haga Login con Google😎</button>
